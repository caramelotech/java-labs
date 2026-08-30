# Generics Avançado

Em Java Core você viu classes genéricas, métodos genéricos e uma primeira passada em wildcards. Essa nota pega os pontos que só aparecem quando generics encontra a vida real: um erro de compilação com nome estranho, uma regra para saber se um wildcard aceita leitura ou escrita, e o motivo de um cast "óbvio" quebrar em produção sem avisar.

## Type erasure: por que um cast não valida nada

O compilador do Java verifica tipos genéricos na hora de compilar e depois descarta essa informação. Em tempo de execução, uma `List<Order>` e uma `List<String>` são exatamente a mesma classe, `List`. Isso se chama type erasure, e é o motivo por trás de boa parte das armadilhas desta nota.

A consequência mais perigosa aparece quando você recebe algo genérico vindo de fora do seu controle, um payload de JSON, uma mensagem de fila, um cache:

```java
Object valor = payload.get("pedidos");
List<Pedido> pedidos = (List<Pedido>) valor; // compila com warning, não valida nada
```

Esse cast não olha dentro da lista. Ele só confirma, em runtime, que `valor` é alguma coisa que implementa `List`. Se dentro dela vier um `Map` em vez de um `Pedido` (coisa comum quando o JSON foi desserializado sem tipo definido), o erro só aparece na primeira linha que tentar tratar aquele elemento como `Pedido`, com um `ClassCastException` longe da causa real:

```java
Pedido primeiro = pedidos.get(0); // ClassCastException aqui, não no cast lá em cima
```

O caminho mais seguro é tratar essa borda como zona de quarentena: ler como desconhecido, validar a estrutura e os elementos, e só depois construir a coleção tipada. `List<?>` (leia adiante) é honesto sobre "não sei o tipo, só vou ler". `List` raw (sem parâmetro nenhum) desliga a proteção dos generics e não deveria aparecer em código novo.

## Wildcards, leitura e escrita

`List<?>` significa "uma lista de algum tipo, que eu não sei qual é". Você pode ler dela, mas o retorno é `Object`, porque o compilador não tem garantia nenhuma sobre o que está lá dentro:

```java
void imprimirTamanho(List<?> lista) {
    Object primeiro = lista.isEmpty() ? null : lista.get(0); // Object, não o tipo real
    System.out.println(lista.size());
}
```

Escrever nessa lista é bloqueado, com uma exceção: `null` sempre pode entrar, porque `null` é compatível com qualquer tipo. Tentar adicionar qualquer outra coisa não compila.

## O erro CAP#1: a captura de wildcard

Um erro clássico aparece ao tentar trocar dois elementos entre listas `List<?>`:

```java
void trocarPrimeiro(List<?> a, List<?> b) {
    a.set(0, b.get(0)); // erro: incompatible types: Object cannot be converted to CAP#1
}
```

A mensagem "CAP#1" não é um tipo real do seu código. Toda vez que o compilador encontra um `?`, ele não sabe o tipo concreto por trás, então inventa um tipo anônimo provisório só para raciocinar sobre aquele uso específico, chamado de variável de captura, numerado CAP#1, CAP#2 e assim por diante. O detalhe que pega muita gente de surpresa: cada aparição de `?` gera uma captura nova e independente, mesmo que seja o mesmo parâmetro escrito duas vezes no código.

No exemplo acima, `a` e `b` podem, para o compilador, ser `List<String>` e `List<Integer>` ao mesmo tempo, ele não tem como provar que as duas capturas são o mesmo tipo. Por isso `b.get(0)` (que devolve `Object`, como vimos) não pode ser passado para `a.set(0, ...)`, que exigiria o tipo exato daquela captura.

A saída é dar nome ao tipo exatamente no ponto em que leitura e escrita precisam estar amarradas à mesma coleção. Você mantém a assinatura pública com `List<?>` (que é mais flexível para quem chama), mas delega o trabalho de verdade a um método privado genérico:

```java
void trocarPrimeiro(List<?> a, List<?> b) {
    trocarPrimeiroHelper(a, b);
}

// dentro daqui, T é um único tipo nominal, então o compilador aceita a troca
private <T> void trocarPrimeiroHelper(List<T> a, List<T> b) {
    T temp = a.get(0);
    a.set(0, b.get(0));
    b.set(0, temp);
}
```

Esse padrão se chama wildcard capture helper e é a solução documentada oficialmente pela Oracle para esse tipo de erro. A pergunta a fazer quando `CAP#1` aparecer: a leitura e a escrita deveriam pertencer à mesma captura? Se sim, use um helper privado com `<T>`. Se vêm de wildcards realmente diferentes, o compilador está certo em bloquear.

## PECS: Producer Extends, Consumer Super

Generics em Java são invariantes: `List<ExpressOrder>` não é subtipo de `List<Order>`, mesmo que `ExpressOrder` estenda `Order`. Um método que recebe `List<Order>` rejeita `List<ExpressOrder>` na chamada.

```java
void processar(List<Order> pedidos) { /* ... */ }

List<ExpressOrder> expressos = new ArrayList<>();
processar(expressos); // erro de compilação, mesmo ExpressOrder sendo um Order
```

Essa rigidez existe para evitar corrupção em runtime. Se `List<ExpressOrder>` pudesse ser passada direto como `List<Order>`, o método `processar` poderia inserir ali um `Order` genérico (ou outro subtipo, como `VipOrder`), e quem segura a lista original como `List<ExpressOrder>` receberia algo que não é `ExpressOrder` no próximo `get()`, um `ClassCastException` esperando para acontecer.

PECS resolve essa rigidez sem abrir mão da segurança: **P**roducer **E**xtends, **C**onsumer **S**uper.

- Quando o método só **lê** da coleção (ela produz valores para você), declare `List<? extends T>`.
- Quando o método só **escreve** na coleção (ela consome valores que você fornece), declare `List<? super T>`.

```java
// Producer: só lê, aceita Order ou qualquer subtipo dele
double somarValores(List<? extends Order> pedidos) {
    double total = 0;
    for (Order pedido : pedidos) total += pedido.getValor(); // leitura segura, tudo é Order
    return total;
}

// Consumer: só escreve, aceita List<Order> ou qualquer lista de um supertipo de Order
void adicionarExpresso(List<? super ExpressOrder> destino, ExpressOrder pedido) {
    destino.add(pedido); // escrita segura, o destino aceita ExpressOrder garantidamente
}
```

Com `? extends T`, o compilador sabe que todo elemento lido pode ser tratado como `T`, então a leitura é segura, mas a escrita é bloqueada (menos `null`), porque o subtipo exato da lista recebida é desconhecido. Com `? super T`, o compilador sabe que a lista aceita `T` ou qualquer supertipo dele, então adicionar um `T` é seguro, mas a leitura só pode devolver `Object`, o único tipo que todo supertipo possível garante ter em comum.

Se o método precisa ler e escrever na mesma coleção, nenhum dos dois wildcards resolve sozinho, esse é o sinal de que o método deveria ser genérico com `<T>` no lugar de usar wildcard.

## Type erasure em APIs REST: o super type token

Quando você chama uma API de HTTP genérica pedindo uma lista, o type erasure ataca de novo:

```java
List<Order> pedidos = restTemplate.getForObject(url, List.class); // "vai ser uma lista", só isso
```

`List.class` só diz "isso vai ser uma lista". O que tem dentro, `Order`, se perde, porque em runtime só existe a classe `List`, sem o `<Order>`. Sem saber o tipo dos elementos, a biblioteca de JSON por trás (normalmente Jackson) cria cada item como um mapa genérico. O código compila e roda até a primeira linha que tenta tratar um item como `Order` de verdade, aí estoura um `ClassCastException` bem longe da causa.

A saída usada pelo Spring é `ParameterizedTypeReference`:

```java
ParameterizedTypeReference<List<Order>> tipo = new ParameterizedTypeReference<>() {};
List<Order> pedidos = restTemplate.exchange(url, HttpMethod.GET, HttpEntity.EMPTY, tipo).getBody();
```

As chaves `{}` no final criam uma subclasse anônima de `ParameterizedTypeReference<List<Order>>`. Isso importa porque o Java apaga genéricos de variáveis, mas preserva os genéricos que fazem parte da hierarquia de uma classe. Como `List<Order>` foi declarado na herança dessa subclasse anônima (e não numa variável comum), ele fica registrado nos metadados da classe e pode ser lido de volta em runtime via reflection. O Spring lê essa informação e repassa o tipo correto para o Jackson. Esse padrão se chama super type token, e vale tanto para `RestTemplate` quanto para `WebClient`.

A regra mental: `.class` serve para tipos simples, sem parâmetro genérico. Para tipos parametrizados, o caminho é sempre um super type token.

## Class<T> como chave de um container heterogêneo

Todo sistema em algum momento precisa de um container que guarde valores de tipos diferentes, um contexto de execução, um registro de configuração, um cache de instâncias por tipo. A solução mais óbvia, `Map<String, Object>`, funciona, mas deixa o controle de tipo por conta de quem usa: um erro de digitação na chave devolve `null` silenciosamente, e toda leitura precisa de um cast manual.

O padrão Typesafe Heterogeneous Container resolve isso invertendo onde o generics entra: em vez de parametrizar o container, você parametriza a chave. `Class<T>` é perfeito para esse papel, porque `String.class` já é do tipo `Class<String>`, então a própria chave carrega a informação de tipo em tempo de compilação e em tempo de execução:

```java
class Contexto {
    private final Map<Class<?>, Object> valores = new HashMap<>();

    public <T> void guardar(Class<T> tipo, T valor) {
        valores.put(tipo, valor);
    }

    public <T> T obter(Class<T> tipo) {
        return tipo.cast(valores.get(tipo)); // cast dinâmico verificado, sem warning
    }
}
```

Por dentro, o container continua sendo um `Map<Class<?>, Object>` comum. A segurança vem da API pública: `guardar` recebe `Class<T>` e um valor `T`, então o compilador já garante que a chave e o valor combinam. `obter` devolve `T` usando `tipo.cast(...)`, um cast verificado pela própria classe em runtime, sem warning e sem cast manual do lado de quem chama.

Esse padrão não é exótico, você já usa a mecânica dele todos os dias: é assim que `applicationContext.getBean(MeuServico.class)` do Spring e `objectMapper.readValue(json, MeuDto.class)` do Jackson sabem devolver o tipo certo sem forçar um cast visível no seu código.
