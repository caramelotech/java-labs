# Enums Avançado

Em Java Core você viu como declarar um enum, dar construtor e métodos a ele, e usar em `switch`. Essa nota é sobre os erros que só aparecem quando o enum sai do exemplo didático e vai para produção: persistência que corrompe dado sem avisar, switch que esconde um caso novo, e as estruturas de coleção feitas sob medida para enum.

## static final solto não é enum

Um sintoma comum em código mais antigo é ver constantes soltas fazendo o papel de um enum:

```java
public static final int STATUS_PENDENTE = 0;
public static final int STATUS_APROVADO = 1;
public static final int STATUS_CANCELADO = 2;
```

Isso compila, mas não protege nada. `int` aceita qualquer valor, então `pedido.setStatus(99)` passa sem erro de compilação, mesmo `99` não representando estado nenhum do domínio. Refatorações também não enxergam essas constantes como um conjunto fechado, então é fácil esquecer um caso ao adicionar um novo status.

Um `enum` transforma esse conjunto num tipo de verdade, fechado e verificado pelo compilador:

```java
public enum StatusPedido {
    PENDENTE, APROVADO, CANCELADO
}
```

Agora `pedido.setStatus(algumValorInvalido)` nem compila se `algumValorInvalido` não for um `StatusPedido`. A diferença não é só sintática: `static final` só nomeia um dado, `enum` dá significado e limite a esse dado.

## Switch exaustivo: deixe o compilador fiscalizar

Desde as switch expressions (Java 14+), quando um `switch` sobre enum cobre todos os valores existentes, o compilador consegue garantir a exaustividade sem precisar de um `default`:

```java
String descricao(StatusPedido status) {
    return switch (status) {
        case PENDENTE -> "aguardando pagamento";
        case APROVADO -> "pagamento confirmado";
        case CANCELADO -> "pedido cancelado";
    };
}
```

Se alguém adicionar `ESTORNADO` ao enum depois, esse `switch` para de compilar em todo lugar que precisa decidir o que fazer com o novo caso. Isso parece incômodo no momento, mas é exatamente a rede de segurança que você quer: o build quebra no lugar certo, antes do deploy.

Colocar um `default` genérico destrói essa proteção:

```java
String descricao(StatusPedido status) {
    return switch (status) {
        case PENDENTE -> "aguardando pagamento";
        case APROVADO -> "pagamento confirmado";
        default -> "cancelado"; // ESTORNADO cai aqui em silêncio, tratado como cancelado
    };
}
```

Com esse `default`, um valor novo do enum cai silenciosamente nesse ramo e o sistema passa a se comportar de forma errada sem que o build acuse nada. Regra prática: em `switch` sobre enum, evite `default`. Se um caso realmente não deveria existir naquele ponto, lance uma exceção explícita ali (`throw new IllegalStateException(...)`) em vez de mascarar com um valor padrão.

## Persistir enum: STRING, nunca ORDINAL

`Enum.ordinal()` devolve a posição da constante na declaração, começando em zero. Usar esse número para persistir o enum no banco parece prático, mas é uma armadilha silenciosa:

```java
public enum StatusPedido {
    PENDENTE,   // ordinal 0
    APROVADO,   // ordinal 1
    CANCELADO   // ordinal 2
}
```

Se alguém inserir um novo valor no meio da lista (bem comum, já que a ordem "natural" de leitura tende a ser cronológica), todo dado já persistido muda de significado sem nenhum aviso:

```java
public enum StatusPedido {
    PENDENTE,   // ordinal 0
    ESTORNADO,  // ordinal 1, empurrou os de baixo
    APROVADO,   // ordinal 2 (era 1!)
    CANCELADO   // ordinal 3 (era 2!)
}
```

Todo pedido que estava gravado com `1` no banco, achando que era `APROVADO`, agora é lido como `ESTORNADO`. Não há erro de compilação, não há exceção em runtime, só dado errado sendo lido como se fosse válido. Em JPA, isso é exatamente o que `@Enumerated(EnumType.ORDINAL)` faz.

A defesa é persistir algo que não dependa de posição: `@Enumerated(EnumType.STRING)` grava o nome da constante, que só muda se alguém renomear a constante (mudança bem mais visível e rastreável num code review):

```java
@Enumerated(EnumType.STRING)
private StatusPedido status;
```

O mesmo cuidado vale para serialização em JSON. Por padrão, bibliotecas como o Jackson serializam o `name()` da constante. Isso é frágil do mesmo jeito: renomear a constante muda o contrato da API sem gerar erro de compilação. Quando o valor exposto na API precisa ser estável mesmo que o nome interno mude, trate-o como um contrato explícito, com um campo `code` próprio marcado com `@JsonValue`:

```java
public enum StatusPedido {
    PENDENTE("PENDING"), APROVADO("APPROVED"), CANCELADO("CANCELLED");

    private final String code;

    StatusPedido(String code) { this.code = code; }

    @JsonValue
    public String getCode() { return code; }
}
```

Regra geral: posição muda, nome do Java muda, mas um `code` declarado explicitamente só muda quando alguém decide mudar o contrato de propósito.

## EnumSet: conjuntos de constantes de um mesmo enum

Quando um `Set` só vai guardar valores de um único enum, `EnumSet` é a escolha idiomática, não `HashSet`. Pense num sistema de permissões, em que cada papel do usuário concede um conjunto de permissões:

```java
public enum Permissao { LER, CRIAR, EDITAR, EXCLUIR, ADMINISTRAR }

EnumSet<Permissao> permissoesEditor = EnumSet.of(Permissao.LER, Permissao.CRIAR, Permissao.EDITAR);
```

Por dentro, `EnumSet` guarda os valores como bits num vetor indexado pela posição do enum, sem hashing, o que o torna mais rápido e mais compacto que `HashSet` nesse caso específico. O ganho não é só de performance: as operações de conjunto ficam diretas. União de permissões de vários papéis vira `addAll`, diferença vira `removeAll`, verificar se um conjunto cobre todas as permissões exigidas vira `containsAll`. As fábricas completam o vocabulário: `EnumSet.allOf(Permissao.class)` para um perfil administrador com tudo liberado, `EnumSet.noneOf(Permissao.class)` como ponto de partida vazio, `EnumSet.complementOf(conjunto)` para expressar exatamente o que um papel não pode fazer.

```java
EnumSet<Permissao> apenasLeitura = EnumSet.complementOf(EnumSet.of(Permissao.LER));
// apenasLeitura = tudo, menos LER
```

Uma armadilha comum: `EnumSet` é mutável. Se uma classe expõe a referência interna direto num getter, quem chama pode adicionar ou remover permissões e corromper o estado do objeto por fora:

```java
public EnumSet<Permissao> getPermissoes() {
    return this.permissoes; // vazamento: quem recebe pode fazer add/remove
}
```

A defesa é copiar na entrada com `EnumSet.copyOf(...)` e devolver algo não modificável na saída:

```java
public Set<Permissao> getPermissoes() {
    return Collections.unmodifiableSet(this.permissoes);
}
```

Vale lembrar também que `EnumSet` rejeita `null`: tentar adicionar `null` lança `NullPointerException` na hora.

## EnumMap: máquinas de estado sem if espalhado

`EnumMap` é a versão de `Map` otimizada para chaves de um mesmo enum, guardada internamente como um array indexado pela posição, sem hashing.

Quando o domínio tem estados bem definidos, como o ciclo de vida de um pedido, é natural modelar as transições permitidas com `EnumMap<StatusPedido, Set<StatusPedido>>`. Cada entrada mapeia um estado de origem para o conjunto de estados de destino válidos a partir dali:

```java
private static final Map<StatusPedido, EnumSet<StatusPedido>> TRANSICOES = new EnumMap<>(StatusPedido.class);
static {
    TRANSICOES.put(StatusPedido.PENDENTE, EnumSet.of(StatusPedido.APROVADO, StatusPedido.CANCELADO));
    TRANSICOES.put(StatusPedido.APROVADO, EnumSet.of(StatusPedido.CANCELADO));
    TRANSICOES.put(StatusPedido.CANCELADO, EnumSet.noneOf(StatusPedido.class)); // estado final, sem saída
}

boolean podeTransicionar(StatusPedido de, StatusPedido para) {
    return TRANSICOES.get(de).contains(para);
}
```

Usar `EnumSet` como valor reforça a coerência, já que as duas estruturas são pensadas para enum e comunicam a mesma intenção. Estados terminais recebem `EnumSet.noneOf(...)` como valor em vez de ficarem sem entrada no mapa, o que evita precisar tratar `null` em `podeTransicionar`. Com isso, o grafo inteiro de transições fica visível em um único bloco estático, fácil de revisar quando uma regra de negócio muda.

## Enum não é extensível, e não precisa ser

Todo `enum` herda implicitamente de `java.lang.Enum`, e Java não permite herança múltipla de classes, então "estender um enum" não é uma opção da linguagem. Isso é proposital: um enum representa um conjunto fechado de constantes conhecidas em tempo de compilação, e permitir extensão quebraria justamente essa garantia (a exaustividade do `switch` que vimos antes deixaria de existir).

Quando surge a vontade de estender um enum, o problema geralmente pede uma destas alternativas:

- **Interface**: o enum continua fechado, mas passa a implementar um contrato comum, então outros enums (ou até classes comuns) podem ser usados no mesmo ponto de extensão, desde que respeitem essa interface.

```java
interface MetodoDePagamento {
    BigDecimal calcularTaxa(BigDecimal valor);
}

enum MetodoPadrao implements MetodoDePagamento {
    PIX, BOLETO;
    public BigDecimal calcularTaxa(BigDecimal valor) { /* ... */ return valor; }
}
```

- **Composição**: quando a intenção é reaproveitar dado ou lógica entre enums, em vez de tentar herdar campos, extraia a parte comum para uma classe ou record auxiliar que o enum guarda internamente.
- **Sealed interface**: útil quando o domínio precisa de mais de uma implementação, mas ainda controlada, permitindo declarar explicitamente quais tipos podem participar daquele contrato, sem abrir para qualquer implementação externa.
- **Uma classe ou record comum**: quando os valores precisam ser criados dinamicamente (configuração, banco de dados, plugin), enum simplesmente não é a abstração certa, porque o conjunto de valores dele é fixo em tempo de compilação por definição.

A régua prática: não tente contornar o fechamento do enum, modele o contrato com uma interface e implemente o enum contra essa interface. Assim, outros tipos podem entrar no mesmo modelo sem forçar uma extensão que a linguagem não oferece.
