# Programação Funcional Avançada

Você já conhece lambda, method reference e o básico de Streams (viu isso em Java Moderno). Essa nota é sobre as decisões que aparecem depois que o básico já não é problema: quando usar stream em vez de loop, como Supplier e Consumer se diferenciam de uma variável comum, onde o boxing se esconde dentro de uma Function e quando vale a pena criar sua própria interface funcional.

## Stream ou loop: qual constrói a regra melhor

Stream e loop fazem a mesma coisa por baixo, repetir um bloco de código várias vezes, mas cada um comunica uma intenção diferente. Um pipeline de stream lido de cima para baixo já quase descreve a regra em palavras: filtrar por um critério, transformar cada item, agrupar, reduzir a um valor só, achar o primeiro que bate com uma condição. Se o que você quer fazer cabe nesse vocabulário, o stream tende a comunicar melhor.

O loop ganha quando a repetição precisa mexer em algo fora dela. Dentro de um `for` tradicional você pode alterar uma variável local, sair do método com `return` no meio da iteração, ou deixar uma exceção verificada (checked) propagar sem embrulhar nada. Uma lambda não tem nenhum desses três poderes:

```java
int total = 0;
for (Pedido pedido : pedidos) {
    if (pedido.foiCancelado()) continue;
    total += pedido.getValor(); // variável local alterada, tranquilo
}
```

Tentar reescrever esse acumulador com `forEach` esbarra numa regra do compilador: a lambda só pode ler variáveis locais que sejam finais ou efetivamente finais (nunca reatribuídas fora dela). Somar dentro de um `forEach` simplesmente não compila:

```java
int total = 0;
pedidos.forEach(pedido -> {
    total += pedido.getValor(); // erro de compilação: variable used in lambda should be final or effectively final
});
```

Uma saída comum, mas ruim, é promover essa variável a campo da classe para "resolver" o erro de compilação. Isso troca um problema de sintaxe por um problema real: agora aquele total é compartilhado entre chamadas diferentes do método e, se o método rodar em mais de uma thread, também vira uma corrida de dados. Quando bater nessa parede, é sinal de que ali é lugar de loop, não de pipeline.

Nada disso é motivo para trocar código de stream que já funciona. A régua é a leitura: converta um loop em pipeline quando a versão com stream ficar mais clara, e deixe como está quando não ficar. Muita rotina do dia a dia fica melhor combinando as duas formas, um pipeline resolvendo a transformação dos dados e um loop tradicional cuidando do resto (persistência, chamada externa, efeito colateral).

Escolhido o pipeline, cuide dos nomes. Sem tipo explícito, o nome do parâmetro da lambda é a única pista de que valor está passando ali, então `pedido ->` conta muito mais do que `p ->`. Se uma etapa do pipeline cresce demais, extraia para um método privado com um nome que descreva a operação, em vez de deixar uma lambda de cinco linhas no meio da cadeia.

## Supplier guarda um jeito de obter, não o valor pronto

`java.util.function.Supplier<T>` e `Consumer<T>` ficam em pontas opostas: `Supplier` tem um único método, `T get()`, que devolve um valor sem receber nada; `Consumer` tem `void accept(T)`, que recebe um valor e não devolve nada.

A diferença para uma variável comum é o que cada um representa. Uma variável guarda um valor já calculado, o trabalho já aconteceu. Um `Supplier` guarda a receita para obter esse valor, o trabalho só acontece quando alguém chama `get()`, e pode acontecer de novo a cada chamada:

```java
Supplier<String> tokenNovo = () -> gerarTokenCaro();

String a = tokenNovo.get(); // gera um token
String b = tokenNovo.get(); // gera OUTRO token, não reaproveita o primeiro
```

Isso explica uma armadilha clássica em `Optional`. `orElse(valor)` recebe um valor pronto, então esse valor é calculado sempre, mesmo quando o `Optional` já está preenchido e o resultado nunca vai ser usado:

```java
// buscarNoCacheRemoto() roda mesmo se usuarioOptional já tiver valor
Usuario usuario = usuarioOptional.orElse(buscarNoCacheRemoto());
```

`orElseGet(Supplier)` resolve isso porque só chama `get()` quando realmente precisa do valor alternativo:

```java
Usuario usuario = usuarioOptional.orElseGet(() -> buscarNoCacheRemoto());
```

`Consumer` aparece nos lugares em que o valor já existe e falta decidir o que fazer com ele, como `forEach` e `Optional.ifPresent`. Em vez de executar uma ação na hora, você entrega essa ação para quem sabe o momento certo de rodar:

```java
usuarioOptional.ifPresent(usuario -> enviarBoasVindas(usuario));
```

Um detalhe de `Consumer.andThen`: ele encadeia dois consumidores, mas não isola falha entre eles. Se o primeiro lançar uma exceção, o segundo nem chega a rodar.

## O boxing escondido dentro de Function<Integer, Integer>

`Function<T, R>` é perfeita para transformar um objeto em outro: entidade em DTO, extrair uma propriedade, alimentar um `Stream.map`. O problema aparece quando você usa `Function<Integer, Integer>` para uma conta simples, tipo somar um valor ao estoque, porque `Function` trabalha com objetos, não com `int`:

```java
Function<Integer, Integer> somarDez = estoque -> estoque + 10;
```

Para essa linha rodar, o Java desempacota o `Integer` recebido em `int`, faz a soma, e empacota o resultado de volta em outro `Integer`. Se o pipeline terminar chamando `mapToInt`, o valor sai empacotado de novo para virar `int` primitivo. Cada uma dessas idas e vindas cria um objeto temporário.

Numa chamada isolada isso não importa. Processando milhões de itens (uma folha de pagamento, um lote de transações), esses `Integer` temporários viram trabalho extra de alocação e mais pressão sobre o coletor de lixo, o que já vimos em Memória na JVM.

A biblioteca padrão tem interfaces especializadas para primitivos justamente para evitar isso: `IntUnaryOperator`, `LongUnaryOperator`, `DoubleUnaryOperator`, entre outras. Usadas junto de `IntStream`/`LongStream`/`DoubleStream`, o valor primitivo atravessa o pipeline inteiro sem virar objeto:

```java
IntUnaryOperator somarDez = estoque -> estoque + 10;
int novoEstoque = somarDez.applyAsInt(150);
```

Isso não é motivo para abandonar `List<Integer>` ou trocar toda `Function` do seu código: em regra de negócio comum, a clareza e a integração com o resto da API do Java pesam mais do que esses microcustos. Vale a troca quando o caminho processa muitos itens ou é sensível a latência, e mesmo assim é melhor confirmar o ganho com profiling antes de sair reescrevendo tudo.

## Quando um method reference vale mais que a lambda

Method reference é uma forma curta de dizer "use este método já existente" no lugar de uma lambda. `PaymentAttempt::getPaymentId` não chama `getPaymentId()` na hora em que você escreve essa linha, só informa ao compilador qual método rodar quando a função for de fato executada.

O tipo esperado no contexto é o que dá sentido à expressão. Se o código espera uma `Function<PaymentAttempt, String>`, o compilador entende `PaymentAttempt::getPaymentId` como uma função que recebe um `PaymentAttempt` e devolve o `paymentId` dele, o mesmo que escrever `attempt -> attempt.getPaymentId()`, só que sem o parâmetro que seria recebido e imediatamente repassado:

```java
List<String> ids = tentativas.stream()
    .map(PaymentAttempt::getPaymentId)
    .toList();
```

Isso ajuda na leitura quando o method reference deixa a transformação em evidência e esconde o "encanamento" (o parâmetro que só passa de um lado para o outro). O erro é tratar method reference como meta em vez de ferramenta: forçar essa sintaxe quando o método tem várias sobrecargas, um receptor pouco óbvio, ou um nome genérico, obriga quem lê a reconstruir mentalmente os argumentos, e o compilador pode até resolver para uma sobrecarga diferente da que você tinha em mente. Nesses casos, uma lambda explícita, com o corpo visível, comunica melhor:

```java
// method reference ambíguo/pouco claro: qual overload de validar é essa?
tentativas.forEach(Validador::validar);

// lambda explícita: fica óbvio o que está sendo validado e com quê
tentativas.forEach(attempt -> Validador.validar(attempt, politicaAtual));
```

A pergunta prática: essa lambda faz alguma coisa além de chamar um método existente com os parâmetros recebidos, na mesma ordem? Se não faz, method reference cabe bem, desde que a leitura realmente melhore.

## Sua interface funcional só se justifica se ganhar algo

Java já vem com `Predicate`, `Function`, `Supplier`, `Consumer` e várias variantes em `java.util.function`. Criar uma interface funcional própria para cada callback parece expressivo, mas na maioria das vezes só aumenta o vocabulário que quem usa a API precisa aprender, sem ganhar nada em troca.

```java
// interface própria sem ganho real: é só um Function<Pedido, Boolean> com outro nome
@FunctionalInterface
interface RegraDeAprovacao {
    boolean aprova(Pedido pedido);
}
```

Se a assinatura cabe em `Predicate<Pedido>`, prefira `Predicate<Pedido>`. A interface padrão já é conhecida por quem lê o código, já se integra com o resto da API do Java e já traz combinadores prontos (`and`, `or`, `negate`) sem você escrever nada a mais. Para primitivos, existem especializações como `IntPredicate` e `IntFunction`, que evitam o boxing visto na seção anterior.

Uma interface própria compensa quando ela representa um conceito central do domínio, quando o contrato merece documentação própria, ou quando você precisa de métodos `default` para compor regras. `Comparator<T>` é o exemplo clássico: estruturalmente ele é equivalente a `ToIntBiFunction<T, T>`, mas o nome, as regras de consistência (`compare` precisa ser transitivo) e os combinadores (`thenComparing`, `reversed`) carregam um significado que `ToIntBiFunction` sozinho não passaria.

Ao criar uma interface funcional de verdade, use `@FunctionalInterface`. A anotação não muda o comportamento em runtime, mas trava a compilação se alguém adicionar um segundo método abstrato por engano, o que quebraria a interface como alvo de lambda:

```java
@FunctionalInterface
interface EstrategiaDeDesconto {
    BigDecimal calcular(Pedido pedido);
}
```

## Lambda de uma linha, método nomeado para o resto

Lambda não tem nome nem Javadoc. Toda a documentação dela é o próprio código. Enquanto ela cabe numa linha e o que faz é óbvio pelo contexto, isso não é problema:

```java
pedidos.removeIf(pedido -> pedido.isCancelado());
```

Quando a lógica cresce, a lambda deixa de comunicar e passa a esconder. Uma lambda de várias linhas dentro de um `stream().map(...)` obriga quem lê a parar o fluxo do pipeline para entender um bloco de lógica que não tem nome. Nesse ponto, vale extrair para um método privado com um nome que já explica a intenção, e usar method reference para conectar:

```java
// lambda grande demais dentro do pipeline
pedidos.stream()
    .map(pedido -> {
        BigDecimal desconto = pedido.isVip() ? new BigDecimal("0.10") : BigDecimal.ZERO;
        BigDecimal comFrete = pedido.getValor().add(pedido.getFrete());
        return comFrete.subtract(comFrete.multiply(desconto));
    })
    .toList();

// extraído: o pipeline volta a ser legível, e o método pode ser testado sozinho
pedidos.stream()
    .map(this::calcularValorFinal)
    .toList();
```

Regra prática: se a lambda cabe em uma linha e explica a si mesma, mantenha. Se ela precisa de várias linhas, comentário ou nome para ser entendida, ela já deixou de ser uma lambda e virou um método escondido, então dê a ela um nome de verdade.
