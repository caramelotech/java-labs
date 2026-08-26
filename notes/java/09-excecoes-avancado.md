# Exceções Avançado

Em Java Core você viu a mecânica de exceções: hierarquia, `try`/`catch`/`finally`, `throw`/`throws`, exceções customizadas, `try-with-resources`. Essa nota é sobre o que a mecânica não ensina sozinha, as decisões de design por trás de quando lançar, quando capturar, o que colocar na mensagem e como não perder informação pelo caminho. É provavelmente a parte de Java que mais separa código que funciona em desenvolvimento de código que resiste a um incidente em produção.

## Exceção sinaliza o inesperado, não decide o negócio

"Saldo insuficiente", "CPF inválido" e "cupom expirado" não são situações inesperadas. São respostas legítimas e previsíveis do seu domínio. Modelar esses casos com `throw` e `catch` tem um custo escondido: nada obriga quem chama o método a tratar esses caminhos. O código compila mesmo que ninguém trate a exceção, e a falha só aparece quando ela acontece de verdade, em produção.

```java
// o compilador não obriga ninguém a lidar com SaldoInsuficienteException
void debitar(Conta conta, BigDecimal valor) throws SaldoInsuficienteException {
    if (conta.getSaldo().compareTo(valor) < 0) {
        throw new SaldoInsuficienteException();
    }
    conta.subtrair(valor);
}
```

Uma alternativa é deixar sucesso e falha explícitos no próprio tipo de retorno, o chamado Result Pattern. Com `sealed interface` e pattern matching no `switch`, o compilador consegue garantir que todo caminho seja tratado, porque reconhece a hierarquia inteira:

```java
sealed interface ResultadoDebito permits Sucesso, SaldoInsuficiente {}
record Sucesso(Conta conta) implements ResultadoDebito {}
record SaldoInsuficiente(BigDecimal faltando) implements ResultadoDebito {}

ResultadoDebito resultado = debitar(conta, valor);
String mensagem = switch (resultado) {
    case Sucesso s -> "debitado com sucesso";
    case SaldoInsuficiente si -> "faltam " + si.faltando();
};
```

Isso não substitui exceção em todo lugar. Falha de infraestrutura, conexão recusada, timeout, disco cheio, não é uma decisão de negócio, é um evento que o chamador imediato normalmente não sabe resolver e que precisa subir até quem sabe lidar com ele, então exceção continua sendo o mecanismo certo. O mesmo vale para violação de invariante: um pedido com valor negativo que passou pela validação é um bug, não um caminho alternativo esperado.

Regra prática: se você está modelando o que o negócio pode responder, use um tipo de retorno explícito. Se está modelando o que deu errado e você não esperava que acontecesse, use exceção.

Isso conecta direto com o princípio Fail Fast: detecte o problema o mais cedo possível e pare ali, em vez de misturar validação com o caminho normal do método. Em Java moderno, dá para levar isso ao nível do tipo com um record e compact constructor, que valida no momento da construção e nunca permite um objeto inválido existir:

```java
record Estoque(int quantidade) {
    Estoque {
        if (quantidade < 0) throw new IllegalArgumentException("quantidade não pode ser negativa: " + quantidade);
    }
}
```

Vale separar dois tipos de falha diferentes. Um `Estoque` negativo é erro de contrato, `IllegalArgumentException` cabe bem. Um gateway de pagamento fora do ar é falha de recurso externo, merece uma exceção própria (`PaymentGatewayUnavailableException`), não a mesma usada para dado inválido. Misturar os dois confunde quem investiga o incidente depois.

## Checked ou unchecked: uma decisão de design, não só de sintaxe

Checked exceptions (as que estendem `Exception` sem estender `RuntimeException`) parecem mais seguras porque o compilador obriga o tratamento. Na prática, isso empurra para três problemas recorrentes:

1. Um `catch` vazio ou com log genérico só para satisfazer o compilador, sem nenhuma decisão real por trás.
2. `throws` se espalhando pela assinatura de métodos que não conseguem fazer nada útil com aquela falha, só repassando.
3. Atrito com lambdas e streams, já que as interfaces funcionais do `java.util.function` não declaram checked exceptions nas suas assinaturas (mais na seção sobre lambdas adiante).

Não é coincidência que Spring, Hibernate e a JPA usem majoritariamente unchecked exceptions nas próprias APIs. A leitura por trás dessa escolha, adotada explicitamente no design do Spring, é que exceções de acesso a dados raramente são recuperáveis pelo chamador imediato, então forçar o tratamento delas em cada camada intermediária produz código cerimonial sem benefício real.

```java
// SQLException (checked) empacotada numa DataAccessException (unchecked) pelo Spring
try {
    jdbcTemplate.update(sql, parametros);
} catch (DataAccessException e) {
    // aqui você decide se recupera, loga e propaga, ou deixa subir
}
```

Regra prática: se quem chama não pode fazer nada além de logar e propagar, prefira unchecked. Se existe uma recuperação real e específica esperada naquele ponto, checked pode fazer sentido, mas vale considerar primeiro se `Optional` ou um tipo de retorno explícito (como vimos na seção anterior) já resolve melhor.

## Capturar o específico, não o genérico

Um `catch (Exception e)` parece prático, uma linha e qualquer coisa é tratada. O problema é que ele captura muito mais do que o `try` de fato pode lançar. `Exception` cobre também as subclasses de `RuntimeException`, incluindo exceções que normalmente indicam bug de programação, como `NullPointerException`.

```java
try {
    LocalDate data = DATE_FORMAT.parse(texto); // só isso pode lançar DateTimeParseException
} catch (Exception e) {
    throw new IllegalArgumentException("data inválida");
}
```

Se `DATE_FORMAT` for `null` por um bug de inicialização em outro lugar, o `NullPointerException` resultante é reempacotado como se fosse erro de formato de data. O time investiga um suposto problema de parsing quando a causa real é um campo nunca inicializado. A correção é capturar só o que o `try` de fato pode lançar, e usar multi-catch (desde o Java 7) quando forem várias exceções específicas:

```java
try {
    LocalDate data = DATE_FORMAT.parse(texto);
} catch (DateTimeParseException e) {
    throw new IllegalArgumentException("data inválida: " + texto, e);
}
```

O mesmo raciocínio se aplica na fronteira entre `Exception` e `Throwable`. Em lógica de negócio, `catch (Throwable e)` é perigoso porque também captura `Error`, e um `Error` normalmente sinaliza algo irrecuperável (como `OutOfMemoryError`, visto em Memória na JVM). Capturar e seguir como se nada tivesse acontecido pode manter a aplicação viva num estado instável. Já em boundaries de infraestrutura, como o ponto de start de uma thread worker ou de um pool, `catch (Throwable e)` pode ser a escolha certa, porque registrar a falha grave antes do processo morrer é justamente a responsabilidade daquele ponto.

Um `catch` vazio merece o mesmo cuidado que um `catch` genérico demais, mas por outro motivo: para quem lê depois, ele é ambíguo. É um bug esquecido ou uma decisão consciente de ignorar aquela falha?

```java
try {
    arquivoTemporario.close();
} catch (IOException ignored) {
    // arquivo já foi lido por completo, não há nada a recuperar aqui
}
```

Quando ignorar de fato é a decisão certa, deixe isso registrado: renomeie a variável de `e` para `ignored` (convenção que várias IDEs e linters reconhecem para reduzir o alerta de "catch vazio") e explique a condição em um comentário curto. Um `catch` silencioso intencional e um acidente são visualmente idênticos até você documentar a diferença.

## Cast sem checar antes derruba produção

Quando um objeto vem de fora do seu controle direto, rede, driver, fila, versão diferente de uma dependência, o compilador perde a garantia sobre o subtipo concreto daquele objeto. Tratar um `cast` como se fosse validação é uma suposição disfarçada de código:

```java
Connection conexao = pool.obterConexao();
ConexaoSegura segura = (ConexaoSegura) conexao; // suposição, não validação
```

Cast não valida, ele só falha mais tarde, geralmente no pior momento, quando uma versão nova de alguma dependência, um protocolo diferente ou uma condição rara faz o subtipo real mudar. Bugs reais em bibliotecas amplamente usadas seguem esse mesmo padrão: um driver de banco fazendo cast direto de um tipo genérico de operação para um tipo específico dentro de um `try/catch` que trata `ClassCastException` como controle de fluxo, ou um sistema distribuído assumindo que toda resposta bem-sucedida chega como um único subtipo, até uma versão de protocolo diferente devolver outro.

A correção segue sempre a mesma forma: checar o tipo antes, tratar o caso inesperado explicitamente, e só então seguir:

```java
if (conexao instanceof ConexaoSegura segura) {
    // seguro, o compilador já garante o tipo aqui dentro
} else {
    throw new IllegalStateException("esperava ConexaoSegura, recebi " + conexao.getClass());
}
```

Quando a hierarquia é conhecida e fechada, `sealed` com `switch` exaustivo (que já vimos em Enums Avançado) leva essa checagem para o nível do compilador: um subtipo novo passa a ser erro de compilação em vez de surpresa em produção.

Regra prática: nunca use exceção para controlar fluxo baseado em tipo. Se a lógica depende do subtipo concreto, torne isso explícito com `instanceof` ou `switch` de pattern matching. E sempre que o código cruzar uma fronteira (rede, versão, biblioteca externa), desconfie do tipo em runtime, porque ali o compilador não tem como te proteger sozinho.

## Uma mensagem que ajuda quem investiga o incidente

Uma exceção sem mensagem, ou com uma mensagem genérica como `"erro ao processar"`, é um alarme que toca sem dizer onde está o problema. Quem investiga o incidente às 3 da manhã precisa ler o código-fonte e reconstruir o cenário na mão, o que transforma minutos de diagnóstico em horas.

```java
// mensagem sem contexto: qual payload? o que era esperado?
throw new IllegalArgumentException("payload inválido");

// mensagem com o trio esperado / recebido / contexto
throw new IllegalArgumentException(
    "esperava payload JSON, recebi XML para o pedido id=" + pedidoId);
```

A prática que resolve isso é simples: toda mensagem de exceção carrega o que era esperado, o que de fato chegou, e o contexto que identifica o caso (um ID, uma chave, um identificador de requisição). Isso fica ainda mais valioso quando a exceção precisa virar resposta HTTP, porque o padrão RFC 9457 (`ProblemDetail`, já visto em Spring Web) espera um campo `detail` explicável, e uma mensagem que já carrega o contexto certo faz esse mapeamento ser direto.

Uma decisão parecida, e que costuma gerar debate no time, é escolher entre `NullPointerException` e `IllegalArgumentException` quando um argumento obrigatório chega como `null`. Não existe resposta absoluta aqui: várias APIs do próprio JDK usam `Objects.requireNonNull(...)` e lançam NPE de propósito quando `null` é proibido; outra linha de pensamento trata `null` como só mais um valor de argumento inválido e usa `IllegalArgumentException` por consistência com as outras validações. O que tem custo real não é qual das duas você escolhe, é o time não escolher: metade do sistema lança uma, metade lança outra, e quem investiga um incidente perde tempo descobrindo qual padrão vale em cada lugar. Documente a escolha (num ADR ou num parágrafo de README) e aplique de forma consistente.

## Não perca a causa ao relançar

Traduzir uma exceção de uma camada para outra é legítimo, um repositório traduzindo uma falha do driver de banco para uma exceção de aplicação, por exemplo. O problema é quando essa tradução descarta a exceção original:

```java
try {
    repositorio.salvar(pedido);
} catch (Exception e) {
    throw new ServiceException(e.getMessage()); // perdeu o tipo, o stack trace, a causa
}
```

`e.getMessage()` parece inofensivo, mas passar só a mensagem descarta o objeto da exceção original inteiro: o tipo real, o stack trace original e qualquer causa que já estivesse encadeada nela. Sobra a frase, mas some a evidência. Encadear a causa é uma mudança pequena com efeito grande:

```java
try {
    repositorio.salvar(pedido);
} catch (Exception e) {
    throw new ServiceException("falha ao salvar pedido id=" + pedido.getId(), e); // causa preservada
}
```

Um padrão relacionado, e igualmente custoso, é logar a exceção e relançar ela mesma logo em seguida, em várias camadas diferentes:

```java
} catch (Exception e) {
    log.error("erro ao processar pedido", e); // logou aqui...
    throw e;
}
// ...
} catch (Exception e) {
    log.error("erro ao processar pedido", e); // ...e de novo aqui, mesmo stack trace
    throw e;
}
```

Isso duplica o stack trace nos logs (aumentando custo de ingestão em ferramentas de observabilidade) e dilui a causa real no meio do ruído repetido. A prática melhor: logue uma única vez, no ponto em que a política da falha realmente se decide, o handler HTTP, o processamento de uma fila, o job. As camadas internas só propagam, com a causa encadeada e contexto agregado, sem narrar o erro a cada passagem.

## ConcurrentModificationException nem sempre é sobre concorrência

O nome sugere um problema de múltiplas threads, mas essa exceção pode acontecer numa única thread, sem concorrência nenhuma envolvida:

```java
for (Promocao promocao : promocoesAtivas) {
    if (promocao.expirou()) {
        promocoesAtivas.remove(promocao); // ConcurrentModificationException na próxima iteração
    }
}
```

O gatilho é estrutural: a coleção foi modificada durante a própria iteração dela. Um `ArrayList` mantém um contador interno de modificações estruturais. O `Iterator` criado pelo `for-each` guarda uma cópia desse contador no início, e a cada `next()` compara com o valor atual, se divergirem, lança a exceção. O detalhe traiçoeiro é que ela estoura na chamada seguinte a `next()`, não na linha do `remove()`. Se a remoção acontecer no último elemento da lista, o loop pode simplesmente terminar sem lançar nada, o bug existe mas não aparece, o que é mais perigoso do que um erro visível.

A correção padrão é `removeIf`, que percorre a coleção sem expor o iterator para fora, eliminando o gatilho:

```java
promocoesAtivas.removeIf(Promocao::expirou);
```

Vale uma ressalva: se o predicado passado para `removeIf` lançar uma exceção no meio da execução, a coleção pode ficar parcialmente modificada mesmo assim. Por isso o predicado deve ser simples e sem efeito colateral.

## Exceções em código assíncrono

### CompletableFuture: cada callback tem um papel diferente

Em código síncrono, uma exceção sobe pela pilha de chamadas até encontrar um `catch`. Em `CompletableFuture`, a exceção passa a compor o estado do próprio estágio (uma "conclusão excepcional"), e os passos seguintes do encadeamento (`thenApply`, `thenCompose`) simplesmente deixam de executar, a menos que algum ponto anterior recupere a falha.

Os três pontos que lidam com falha em `CompletableFuture` não são intercambiáveis:

```java
CompletableFuture<Pedido> futuro = buscarPedidoAsync(id);

futuro.exceptionally(erro -> pedidoPadrao());      // recupera: devolve um valor alternativo
futuro.handle((pedido, erro) -> tratarAmbos(pedido, erro)); // transforma: recebe sucesso E falha
futuro.whenComplete((pedido, erro) -> logar(erro)); // observa: não muda o resultado
```

`exceptionally` recupera a falha e devolve um valor alternativo, a cadeia continua normalmente a partir dali. `handle` transforma, recebendo tanto o resultado quanto a exceção (um dos dois sempre `null`) e decidindo o que fazer com base nos dois. `whenComplete` só observa, para efeitos colaterais como log ou métrica, sem alterar o resultado, mesmo que você trate a exceção dentro dele, a conclusão excepcional original continua se propagando adiante.

```java
futuro.whenComplete((pedido, erro) -> log.error("falhou", erro))
      .join(); // ainda lança CompletionException aqui, whenComplete não recuperou nada
```

Usar o método errado não gera erro de compilação, só muda o comportamento da cadeia em runtime de um jeito que costuma surpreender. Regra prática: se o fluxo precisa continuar com um valor padrão após falha, `exceptionally`. Se precisa decidir algo com base em sucesso ou falha, `handle`. Se é só para registrar o que aconteceu sem interferir, `whenComplete`.

### Checked exceptions dentro de lambda

As interfaces funcionais do `java.util.function` (vistas em Programação Funcional Avançada) não declaram checked exceptions nas suas assinaturas. Quando o código dentro de um `map()` ou `forEach()` precisa chamar algo que lança uma checked exception, como `IOException`, o `try/catch` local dentro da lambda vira quase obrigatório:

```java
arquivos.stream()
    .map(caminho -> {
        try {
            return Files.readString(caminho);
        } catch (IOException e) {
            log.warn("falha ao ler " + caminho, e);
            return null; // o pipeline finge que está tudo bem
        }
    })
    .filter(Objects::nonNull)
    .toList();
```

O problema não é o `try/catch` em si, é o que costuma acontecer dentro dele: capturar, logar e devolver `null`. Isso transforma uma falha operacional real em "ausência de valor", e o `filter(Objects::nonNull)` seguinte remove esse item como se o stream nunca tivesse encontrado problema nenhum. O tipo do resultado continua correto, uma `List<String>`, mas a integridade dele foi perdida: o chamador recebe uma lista aparentemente válida que pode estar silenciosamente incompleta. Regra prática: não esconda checked exception dentro de um pipeline. Trate fora dele (com um loop tradicional, se fizer sentido) ou traduza para uma exceção unchecked preservando causa e contexto. `null` não recupera a falha, só disfarça.

## InterruptedException é um pedido de cancelamento

Métodos bloqueantes como `Thread.sleep()`, `Future.get()` e `BlockingQueue.take()` lançam `InterruptedException` quando a thread atual recebe um pedido de interrupção enquanto está esperando. Isso acontece em situações comuns: um `ExecutorService.shutdownNow()` durante um deploy, um timeout cancelando uma tarefa lenta.

Esse pedido funciona através de uma flag interna da thread, o interrupt status. Ao lançar a exceção, o próprio método bloqueante já limpa essa flag automaticamente. Se o `catch` só loga e segue em frente, sem restaurar a flag, a thread perde o único sinal de que deveria estar encerrando:

```java
try {
    fila.take();
} catch (InterruptedException e) {
    log.warn("interrompido", e); // a flag já foi limpa pelo take(), e não foi restaurada aqui
    // a thread continua rodando como se nada tivesse pedido para ela parar
}
```

O efeito em produção é silencioso: o processo de shutdown manda interrupt para as threads ativas esperando que elas encerrem; se alguma engole a exceção sem restaurar a flag, ela continua rodando, o processo demora para terminar ou nem termina, e o ambiente de deploy acaba matando tudo à força, cortando trabalho em andamento sem limpar nada.

O padrão correto tem dois passos: restaurar a flag imediatamente, e então decidir como encerrar de forma cooperativa.

```java
try {
    fila.take();
} catch (InterruptedException e) {
    Thread.currentThread().interrupt(); // restaura o sinal
    return; // encerra a tarefa de forma cooperativa
}
```

Regra prática: nunca engula um `InterruptedException` sem chamar `Thread.currentThread().interrupt()` primeiro.

## finally com return engole exceção

O bloco `finally` existe para garantir limpeza (fechar recurso, liberar lock), independente do que aconteceu no `try` ou no `catch`. Se esse bloco contém um `return`, o comportamento muda de um jeito que o compilador não avisa:

```java
boolean processar() {
    try {
        return operacaoQuePodeFalhar();
    } finally {
        return false; // qualquer exceção do try é descartada, o chamador recebe false e não sabe de nada
    }
}
```

Qualquer exceção propagada pelo `try` ou pelo `catch` é descartada nesse momento. O chamador recebe o valor do `return` do `finally` normalmente, sem nenhum sinal de que algo falhou. O mesmo vale mesmo sem exceção nenhuma: o `return` do `finally` sobrescreve o valor que o `try` estava prestes a devolver. O efeito atinge o fluxo de erro e o fluxo normal ao mesmo tempo.

O que torna esse bug traiçoeiro é que o código compila e roda sem nenhum aviso, e visualmente um `return` no `finally` não levanta suspeita da mesma forma que um `catch` vazio levanta em revisão de código. O mesmo problema vale para `break` e `continue` dentro de `finally`. Regra prática: `finally` é só para limpeza, cleanup e nada mais. Se precisa retornar algo, faça isso no `try` ou no `catch`, nunca no `finally`.

## toString() que percorre o grafo inteiro

A própria documentação do Java recomenda sobrescrever `toString()` para algo conciso, informativo e legível. O erro comum não é esquecer de sobrescrever, é transformar o método numa travessia de todo o grafo de objetos relacionados:

```java
@Override
public String toString() {
    return "Pedido{itens=" + itens + ", cliente=" + cliente + "}"; // cliente.toString() encadeia mais coisas
}
```

Quando `toString()` imprime coleções inteiras ou objetos com referência de volta para quem os chamou (uma relação bidirecional, comum em entidades JPA), o `toString()` de um lado chama o do outro, que chama o do primeiro de novo, até estourar um `StackOverflowError`. O problema piora com proxies lazy do Hibernate: um `toString()` aparentemente inofensivo, chamado por um log, pode tocar uma associação lazy, disparar uma consulta ao banco que ninguém esperava ali, e, se a sessão já tiver fechado, lançar uma exceção de lazy loading em vez de simplesmente imprimir texto.

Além disso, `toString()` não é uma camada de apresentação. Formato de moeda, data ou CEP sensíveis a localidade pertencem a um formatador dedicado, não ao método pensado para diagnóstico interno. Em entidades, prefira incluir só campos simples e seguros, deixando de fora relacionamentos e coleções. Em DTOs imutáveis (`record`), o `toString()` gerado automaticamente já costuma ser adequado, porque um record não deveria carregar relacionamento lazy de qualquer forma.

Regra prática: `toString()` mostra identidade e estado útil para diagnóstico, sem acessar banco, sem percorrer grafo de relacionamento e sem embutir formatação dependente de localidade.

## O contrato entre equals e hashCode

Uma das regras mais violadas do Java: se dois objetos são iguais por `equals`, eles precisam retornar o mesmo `hashCode`. Quebrar esse contrato não gera erro de compilação nem exceção, o código só passa a se comportar de forma errada, silenciosamente.

`HashSet` e `HashMap` funcionam como um armário de gavetas numeradas pelo `hashCode`. Ao inserir um objeto, ele vai para a gaveta correspondente ao hash dele; ao consultar, a mesma conta de hash é refeita para abrir a gaveta certa e comparar com `equals` o que está lá dentro. Se você sobrescreve `equals` mas esquece `hashCode`, dois objetos considerados iguais por `equals` podem cair em gavetas diferentes:

```java
Cliente cliente = new Cliente("123.456.789-00");
set.add(cliente);
// mais tarde, um objeto Cliente diferente mas "igual" pelo equals (mesmo CPF)
Cliente mesmoCliente = new Cliente("123.456.789-00");
set.contains(mesmoCliente); // pode devolver false, se hashCode não foi sobrescrito junto
```

O mesmo problema aparece quando o `hashCode` é calculado com base num campo mutável: se esse campo mudar depois que o objeto já foi inserido na coleção, a próxima consulta calcula um hash diferente, abre a gaveta errada, e não encontra nada. O objeto continua fisicamente lá, `size()` ainda conta ele, uma iteração ainda mostra ele, mas `contains()` devolve `false`. Virou, na prática, um fantasma dentro da coleção.

Em entidades JPA, isso pede cuidado redobrado: o identificador gerado pelo banco pode ser `null` antes da entidade ser persistida, então usar o `id` cru em `equals`/`hashCode` sem tratar esse caso é uma fonte comum de bug. O critério de identidade precisa ser estável ao longo de todo o ciclo de vida do objeto, evitando campos que mudam depois da inserção na coleção.

Regra prática: se `equals` define identidade, `hashCode` precisa contar exatamente a mesma história. Sempre sobrescreva os dois juntos, nunca um sem o outro.

## equals(Object) ou você só sobrecarregou, não sobrescreveu

Um erro sutil que passa despercebido em testes e só aparece em produção: escrever `equals(MeuTipo outro)` em vez de `equals(Object outro)`.

```java
public boolean equals(Cliente outro) { // isso SOBRECARREGA, não sobrescreve Object.equals
    return this.cpf.equals(outro.cpf);
}
```

Isso não sobrescreve o método de `Object`, apenas cria uma sobrecarga nova. As coleções e APIs do Java operam sempre com base na assinatura `equals(Object)`, então esse método novo nunca participa do polimorfismo esperado, e `ArrayList.contains()`, `HashSet`, tudo que depende de `equals` continua usando a implementação padrão de `Object` (que compara referência, não conteúdo). O código compila normalmente e os testes que chamam `cliente.equals(outroCliente)` diretamente até passam, porque o tipo estático já é `Cliente` ali. O bug só aparece quando o `equals` é chamado através de uma API genérica, como dentro de um `ArrayList.contains()`.

A correção é simples: sempre use `@Override` ao sobrescrever `equals`. Com a anotação, o compilador rejeita a assinatura errada na hora:

```java
@Override
public boolean equals(Object outro) { // compilador garante que essa é a assinatura certa
    if (this == outro) return true;
    if (!(outro instanceof Cliente c)) return false;
    return this.cpf.equals(c.cpf);
}
```

Além da assinatura certa, sobrescrever `equals` assume um contrato com cinco regras: reflexivo (`x.equals(x)` sempre `true`), simétrico (`a.equals(b)` implica `b.equals(a)`), transitivo (`a` igual a `b` e `b` igual a `c` implica `a` igual a `c`), consistente (o resultado não muda enquanto o estado usado na comparação não muda) e `x.equals(null)` sempre `false`. A receita que cobre as cinco regras: comparar `this == outro` primeiro, validar o tipo com `instanceof` (que já cobre o caso `null`, já que `null instanceof Qualquer` é sempre `false`), fazer o cast implícito do pattern matching, e comparar os campos que de fato definem identidade.
