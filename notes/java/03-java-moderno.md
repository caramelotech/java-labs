# Java Moderno

A partir do Java 8, a linguagem passou por uma transformação significativa com a adição de recursos de programação funcional. Desde então, cada versão trouxe novidades que tornam o código mais expressivo e conciso.

## var: inferência de tipo local

Desde o Java 10, `var` deixa o compilador inferir o tipo de uma variável local a partir do valor atribuído, sem que você precise escrever o tipo por extenso:

```java
var nomes = new ArrayList<String>(); // o compilador infere ArrayList<String>
var total = calcularTotal(pedidos);  // infere o tipo de retorno de calcularTotal
```

`var` não torna Java uma linguagem de tipagem dinâmica, o tipo continua sendo decidido em tempo de compilação, só não precisa ser escrito duas vezes quando já está óbvio do lado direito do `=`. Ele reduz ruído visual em casos como construtores explícitos ou métodos de fábrica com nome claro:

```java
var conexao = new ConexaoBancoDeDados(host, porta, credenciais);
var usuario = Usuario.admin("Jane", "jane@x.com");
```

O cuidado é usar com moderação: se o tipo não fica óbvio pelo lado direito da atribuição, `var` esconde informação que ajudaria quem está lendo, sem ganhar nada em troca:

```java
var resultado = processar(dados); // qual é o tipo de resultado? só abrindo o método pra saber
```

O guia oficial do OpenJDK resume bem o critério: o código deveria ser compreensível por leitura direta, sem depender de recursos de IDE para descobrir um tipo escondido. `var` funciona bem quando o tipo já está evidente na própria linha (um construtor, uma fábrica com nome descritivo, uma variável extraída de um pipeline de stream complexo, onde o tipo genérico ficaria longo e repetitivo). Na dúvida, é melhor escrever o tipo por extenso do que forçar quem lê a investigar.

## Lambda Expressions

Lambdas permitem tratar funções como valores - criando implementações de interfaces funcionais (interfaces com um único método abstrato) de forma inline:

```java
// Antes do Java 8
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Executando...");
    }
};

// Com lambda
Runnable r = () -> System.out.println("Executando...");
```

A sintaxe é: `(parâmetros) -> { corpo }`. Quando o corpo tem uma única expressão, as chaves e o `return` são opcionais:

```java
// Um parâmetro, expressão simples
List<String> nomes = List.of("Ana", "Bruno", "Carlos");
nomes.forEach(nome -> System.out.println(nome));

// Dois parâmetros
Comparator<String> comp = (a, b) -> a.compareTo(b);

// Com bloco
Runnable r = () -> {
    System.out.println("Linha 1");
    System.out.println("Linha 2");
};
```

### Interfaces funcionais comuns

```java
import java.util.function.*;

Predicate<String> isEmpty = s -> s.isEmpty();          // boolean
Function<String, Integer> length = s -> s.length();    // A -> B
Consumer<String> print = s -> System.out.println(s);   // void
Supplier<String> hello = () -> "Olá";                  // -> A
BiFunction<Integer, Integer, Integer> soma = (a, b) -> a + b;
```

Essas são só as mais comuns. O pacote `java.util.function` traz o catálogo completo (`UnaryOperator`, `BinaryOperator`, as variantes de dois argumentos, as versões para primitivos) e os métodos de composição de cada uma. Tudo isso está em [Interfaces funcionais](/labs/java/java/06-interfaces-funcionais/).

## Method Reference

Method reference (`::`) é uma forma ainda mais concisa de referenciar métodos existentes quando o lambda apenas delega a chamada:

```java
// Lambda equivalente ao método existente
nomes.forEach(nome -> System.out.println(nome));

// Method reference - mais limpo
nomes.forEach(System.out::println);
```

Quatro tipos:

```java
// Método estático
Function<String, Integer> parse = Integer::parseInt;

// Método de instância de tipo arbitrário
Function<String, String> upper = String::toUpperCase;

// Método de instância de objeto específico
String prefixo = "Java: ";
Function<String, String> adicionar = prefixo::concat;

// Construtor
Supplier<ArrayList<String>> criarLista = ArrayList::new;
```

## Streams API

Streams permitem processar sequências de elementos de forma declarativa e funcional. Não armazenam dados - são pipelines de operações.

```java
import java.util.stream.*;

List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Pipeline: filtrar pares, elevar ao quadrado, coletar
List<Integer> resultado = numeros.stream()
    .filter(n -> n % 2 == 0)       // intermediária
    .map(n -> n * n)                // intermediária
    .collect(Collectors.toList());  // terminal
// [4, 16, 36, 64, 100]
```

### Operações intermediárias (lazy - executam só quando há uma terminal)

```java
stream.filter(Predicate)        // filtra elementos
stream.map(Function)            // transforma cada elemento
stream.flatMap(Function)        // transforma e achata coleções aninhadas
stream.sorted()                 // ordena
stream.sorted(Comparator)       // ordena com critério
stream.distinct()               // remove duplicatas
stream.limit(n)                 // limita a n elementos
stream.skip(n)                  // pula n elementos
stream.peek(Consumer)           // inspeciona sem alterar (útil para debug)
```

### Operações terminais (disparam o pipeline)

```java
stream.collect(Collectors.toList())         // coleta em lista
stream.collect(Collectors.toSet())          // coleta em set
stream.collect(Collectors.joining(", "))    // une strings
stream.forEach(Consumer)                    // executa ação para cada elemento
stream.count()                              // conta elementos
stream.findFirst()                          // retorna Optional com primeiro
stream.anyMatch(Predicate)                  // true se algum satisfaz
stream.allMatch(Predicate)                  // true se todos satisfazem
stream.noneMatch(Predicate)                 // true se nenhum satisfaz
stream.min(Comparator)                      // retorna Optional com menor
stream.max(Comparator)                      // retorna Optional com maior
stream.reduce(identity, BinaryOperator)     // reduz a um único valor
```

### Exemplos práticos

```java
List<String> nomes = List.of("Ana", "Bruno", "Carlos", "Diana", "Eduardo");

// Nomes com mais de 4 letras, em maiúsculas, ordenados
List<String> resultado = nomes.stream()
    .filter(n -> n.length() > 4)
    .map(String::toUpperCase)
    .sorted()
    .collect(Collectors.toList());
// [BRUNO, CARLOS, DIANA, EDUARDO]

// Soma de todos os tamanhos
int totalLetras = nomes.stream()
    .mapToInt(String::length)
    .sum();

// Agrupar por tamanho
Map<Integer, List<String>> porTamanho = nomes.stream()
    .collect(Collectors.groupingBy(String::length));
```

### Streams de tipos primitivos

`IntStream`, `LongStream` e `DoubleStream` evitam boxing/unboxing:

```java
IntStream.range(1, 6).forEach(System.out::println); // 1 a 5
IntStream.rangeClosed(1, 5).sum();                  // 15

int[] numeros = {1, 2, 3, 4, 5};
Arrays.stream(numeros).average().ifPresent(System.out::println); // 3.0
```

## Optional

`Optional<T>` é um container que pode ou não conter um valor. Resolve o problema de retornar `null` de métodos:

```java
Optional<String> nome = Optional.of("João");
Optional<String> vazio = Optional.empty();
Optional<String> talvez = Optional.ofNullable(null); // empty

// Verificar e usar
if (nome.isPresent()) {
    System.out.println(nome.get());
}

// Forma mais elegante
nome.ifPresent(System.out::println);

// Valor padrão se vazio
String resultado = vazio.orElse("Anônimo");

// Lançar exceção se vazio
String valor = nome.orElseThrow(() -> new RuntimeException("Sem nome"));

// Transformar se presente
Optional<Integer> tamanho = nome.map(String::length); // Optional[4]
```

Uso típico - busca que pode não encontrar resultado:

```java
public Optional<Usuario> buscarPorEmail(String email) {
    return usuarios.stream()
        .filter(u -> u.getEmail().equals(email))
        .findFirst();
}

// Uso
buscarPorEmail("ana@exemplo.com")
    .ifPresentOrElse(
        u -> System.out.println("Encontrado: " + u.getNome()),
        () -> System.out.println("Usuário não encontrado")
    );
```

## Java Time API

A API de data/hora do Java 8 (pacote `java.time`) substituiu os problemáticos `Date` e `Calendar`.

### Tipos principais

```java
import java.time.*;

LocalDate hoje = LocalDate.now();          // só data: 2026-04-26
LocalTime agora = LocalTime.now();         // só hora: 14:30:00
LocalDateTime dataHora = LocalDateTime.now(); // data e hora

ZonedDateTime comFuso = ZonedDateTime.now(ZoneId.of("America/Sao_Paulo"));

Instant instant = Instant.now(); // timestamp Unix
```

### Criação e manipulação

```java
LocalDate natal = LocalDate.of(2026, 12, 25);
LocalDate amanha = hoje.plusDays(1);
LocalDate semanaPassada = hoje.minusWeeks(1);

// Comparação
boolean eAntes = natal.isBefore(LocalDate.of(2027, 1, 1));

// Diferença entre datas
Period periodo = Period.between(hoje, natal);
System.out.println(periodo.getMonths() + " meses e " + periodo.getDays() + " dias");

// Formatação
DateTimeFormatter fmt = DateTimeFormatter.ofPattern("dd/MM/yyyy");
String formatado = hoje.format(fmt); // "26/04/2026"
LocalDate parseado = LocalDate.parse("25/12/2026", fmt);
```

### Atravessando fusos horários sem se perder

`LocalDateTime` guarda data e hora, mas nenhuma informação de fuso, ele não representa um instante único no tempo, só uma combinação de números de calendário. Isso é perigoso justamente quando o sistema precisa comparar horários de lugares diferentes, porque "14h" sozinho não diz *quando* isso é de verdade sem saber em qual fuso.

`ZonedDateTime` e `Instant` resolvem isso porque representam um instante absoluto, o mesmo ponto exato na linha do tempo, só exibido de formas diferentes dependendo do fuso:

```java
ZonedDateTime emNovaYork = ZonedDateTime.now(ZoneId.of("America/New_York"));
ZonedDateTime emSaoPaulo = emNovaYork.withZoneSameInstant(ZoneId.of("America/Sao_Paulo"));
```

`withZoneSameInstant` não muda o instante, só a forma de exibir ele: se são 18h em Nova York, o resultado em São Paulo pode mostrar 19h, mas os dois `ZonedDateTime` apontam exatamente para o mesmo nanossegundo na linha do tempo universal. Essa garantia é o que permite comparar ou calcular diferença entre horários de fusos diferentes com segurança, algo que `LocalDateTime` sozinho não oferece, porque duas instâncias de `LocalDateTime` com os mesmos números podem representar momentos completamente diferentes se vierem de fusos diferentes.

### Duration mede tempo físico, Period mede tempo de calendário

`Duration` e `Period` respondem perguntas parecidas ("quanto tempo entre duas datas?"), mas não são intercambiáveis. `Duration` mede tempo físico, em segundos e nanossegundos, e opera sobre instantes absolutos (`Instant`, `ZonedDateTime`). `Period` mede tempo de calendário civil, em anos, meses e dias, e só faz sentido para tipos sem fuso, como `LocalDate`.

```java
Duration duracao = Duration.between(inicio, fim); // segundos entre dois instantes
Period periodo = Period.between(dataInicio, dataFim); // "x anos, y meses, z dias"
```

A pegadinha mais comum é chamar `Period.between(...).getDays()` esperando o total de dias corridos entre duas datas. `getDays()` não devolve isso, devolve só o componente "dias" depois de já ter extraído os anos e os meses. Para um intervalo de 60 dias entre 1º de setembro e 31 de outubro, `Period.between` calcula algo como "1 mês e 30 dias", e `getDays()` devolve `30`, não `60`:

```java
Period periodo = Period.between(LocalDate.of(2026, 9, 1), LocalDate.of(2026, 10, 31));
periodo.getDays();   // 30, não o total de dias do intervalo
periodo.getMonths(); // 1
```

Quando o que você precisa é o total de dias corridos (para calcular um SLA, um prazo de validade), `ChronoUnit.DAYS.between(...)` é a ferramenta certa, não `Period`:

```java
long diasTotais = ChronoUnit.DAYS.between(LocalDate.of(2026, 9, 1), LocalDate.of(2026, 10, 31)); // 60
```

Regra prática: `Duration` para medir tempo físico entre instantes (latência, prazo em horas), `Period` só quando o resultado precisa ser expresso em unidades de calendário ("2 meses e 5 dias"), e `ChronoUnit.between` quando você só quer um número total numa única unidade.

### nanoTime para medir duração, currentTimeMillis para saber a hora

`System.currentTimeMillis()` representa tempo de calendário, o "relógio de parede" do sistema operacional, que pode avançar ou retroceder por ajuste manual, sincronização de NTP ou mudança de fuso. Usar essa diferença para medir quanto tempo uma operação levou é uma fonte clássica de bug sutil:

```java
long inicio = System.currentTimeMillis();
operacaoDemorada();
long duracao = System.currentTimeMillis() - inicio; // pode dar negativo se o relógio ajustar no meio
```

Se o relógio do sistema sofrer um ajuste (por sincronização NTP, por exemplo) durante essa operação, a duração calculada pode sair errada, e em casos raros até negativa, o que não faz sentido nenhum para uma medição de tempo decorrido.

`System.nanoTime()` existe justamente para isso: funciona como um cronômetro monotônico da própria JVM, que só avança, nunca retrocede, baseado num ponto de referência arbitrário definido na inicialização do processo. O valor absoluto dele não representa uma data e não deveria ser comparado entre execuções diferentes do programa, mas a diferença entre duas leituras dentro da mesma execução é estável e imune a ajustes do relógio do sistema:

```java
long inicio = System.nanoTime();
operacaoDemorada();
long duracaoNanos = System.nanoTime() - inicio; // sempre correto, imune a ajuste de relógio
```

Regra prática: tempo de calendário, timestamp de evento e log usam `currentTimeMillis()` ou `Instant`. Para medir tempo decorrido, latência ou desempenho, a escolha correta é sempre `nanoTime()`.

## Text Blocks (Java 15+)

Text blocks simplificam strings multilinha, eliminando escape de aspas e concatenações:

```java
// Antes
String json = "{\n" +
              "  \"nome\": \"Ana\",\n" +
              "  \"idade\": 28\n" +
              "}";

// Com text block
String json = """
        {
          "nome": "Ana",
          "idade": 28
        }
        """;
```

A indentação comum é removida automaticamente com base na posição do `"""` de fechamento.

```java
String html = """
        <html>
            <body>
                <p>Olá, Mundo!</p>
            </body>
        </html>
        """;
```

## Records (Java 16+)

Records são classes imutáveis e concisas para transportar dados. O compilador gera automaticamente construtor, getters, `equals()`, `hashCode()` e `toString()`:

```java
// Antes - muito boilerplate
public class Ponto {
    private final int x;
    private final int y;

    public Ponto(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int x() { return x; }
    public int y() { return y; }

    @Override
    public boolean equals(Object o) { ... }
    @Override
    public int hashCode() { ... }
    @Override
    public String toString() { ... }
}

// Com record - equivalente ao código acima
public record Ponto(int x, int y) {}
```

```java
Ponto p = new Ponto(3, 7);
System.out.println(p.x());   // 3
System.out.println(p);       // Ponto[x=3, y=7]

// Validação no construtor compacto
public record Temperatura(double celsius) {
    public Temperatura {
        if (celsius < -273.15) {
            throw new IllegalArgumentException("Temperatura abaixo do zero absoluto");
        }
    }
}
```

Records são ideais para DTOs, respostas de API e Value Objects.

### Imutabilidade como proteção de domínio, não só estilo

Boa parte dos bugs difíceis de rastrear não vem de algoritmo errado, vem de estado que mudou sem que ninguém percebesse. Conceitos como preço, desconto ou percentual costumam atravessar vários métodos e camadas, e se o objeto que os representa for mutável, uma referência reaproveitada em outro lugar pode alterar um valor que uma parte do código já assumia como fixo.

```java
// mutável: nada impede outro trecho do código de alterar esse preço "por baixo"
class Preco {
    private BigDecimal valor;
    public void setValor(BigDecimal valor) { this.valor = valor; }
}
```

Modelar esse tipo de conceito como um `record` remove essa possibilidade pela raiz: os campos já nascem `final`, sem setter, e aplicar um desconto ou somar um imposto deixa de ser "alterar o preço" e passa a ser "criar um preço novo a partir do anterior":

```java
record Preco(BigDecimal valor) {
    Preco aplicarDesconto(BigDecimal percentual) {
        return new Preco(valor.subtract(valor.multiply(percentual)));
    }
}
```

Isso elimina efeito colateral escondido, deixa o objeto seguro para compartilhar entre threads sem sincronização extra, e reduz o tipo de bug em que "ninguém mexeu, mas o valor mudou sozinho" (porque, na prática, alguém mexeu, só que num objeto que dava a falsa impressão de ser fixo). O custo é criar mais objetos ao longo do caminho, um custo pequeno perto do tempo que se gasta depurando estado compartilhado corrompido.

### Onde Records não servem: entidades JPA

Um Record parece uma boa forma de modernizar uma entidade JPA, até a aplicação simplesmente não subir. A Jakarta Persistence exige que uma entidade tenha um construtor sem argumentos, não seja `final` e não declare os campos persistidos como `final`, justamente para o provedor (Hibernate, na maioria dos projetos Spring) conseguir instanciar e popular o objeto via reflection, e para gerar proxies de carregamento tardio (lazy loading) quando necessário.

Um `record` viola as três condições ao mesmo tempo: os componentes são implicitamente `final`, não existe construtor vazio, e a própria classe gerada é `final`, o que impede o Hibernate de criar uma proxy por herança. O resultado costuma aparecer ainda no `startup` da aplicação, antes de qualquer requisição ser aceita.

Isso não invalida o Record como ferramenta, só marca onde ele não se aplica. Records continuam sendo a escolha certa para DTOs e projeções de leitura (inclusive em consultas JPQL com `constructor expression`), e, desde versões mais recentes do Hibernate, também servem como `@Embeddable`. Para entidades com ciclo de vida gerenciado pelo JPA, relacionamento lazy e mutação de estado ao longo do tempo, uma classe tradicional continua sendo o caminho correto (Spring Data detalha esse mapeamento).

## Sealed Classes (Java 17+)

Sealed classes restringem quais classes podem estender ou implementar um tipo, tornando a hierarquia explícita e fechada:

```java
public sealed class Forma permits Circulo, Retangulo, Triangulo {}

public final class Circulo extends Forma {
    private final double raio;
    public Circulo(double raio) { this.raio = raio; }
    public double raio() { return raio; }
}

public final class Retangulo extends Forma {
    private final double largura, altura;
    public Retangulo(double largura, double altura) {
        this.largura = largura;
        this.altura = altura;
    }
}

public non-sealed class Triangulo extends Forma {} // permite extensão livre
```

O compilador sabe que só existem esses subtipos, então `switch` exhaustivo não precisa de `default`:

```java
double area = switch (forma) {
    case Circulo c -> Math.PI * c.raio() * c.raio();
    case Retangulo r -> r.largura() * r.altura();
    case Triangulo t -> calcularAreaTriangulo(t);
};
```

## Pattern Matching para switch (Java 21+)

Combina type checking, cast e condições em uma única expressão:

```java
Object obj = "Olá, Java!";

String resultado = switch (obj) {
    case Integer i -> "Inteiro: " + i;
    case String s when s.length() > 5 -> "String longa: " + s;
    case String s -> "String curta: " + s;
    case null -> "Nulo";
    default -> "Outro: " + obj;
};
```

## Virtual Threads (Java 21+)

Virtual threads (Project Loom) são threads leves gerenciadas pela JVM, não pelo SO. Permitem criar milhares de threads sem o overhead das threads do sistema:

```java
// Thread tradicional (1:1 com thread do SO)
Thread t1 = new Thread(() -> System.out.println("Thread tradicional"));
t1.start();

// Virtual thread - muito mais leve
Thread vt = Thread.ofVirtual().start(() -> System.out.println("Virtual thread"));

// Criar muitas virtual threads sem problema
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 10_000; i++) {
        executor.submit(() -> {
            Thread.sleep(Duration.ofMillis(100));
            return "ok";
        });
    }
}
```

Virtual threads são ideais para operações de I/O (chamadas de API, banco de dados), onde a thread fica bloqueada esperando resposta. Com threads tradicionais, isso desperdiça recursos; com virtual threads, a JVM reutiliza o carrier thread enquanto espera.

## Concurrency API

Para além de virtual threads, o pacote `java.util.concurrent` oferece ferramentas de alto nível:

```java
import java.util.concurrent.*;

// Pool de threads
ExecutorService executor = Executors.newFixedThreadPool(4);

// Future: resultado assíncrono
Future<Integer> futuro = executor.submit(() -> {
    Thread.sleep(1000);
    return 42;
});

System.out.println("Fazendo outra coisa...");
int resultado = futuro.get(); // bloqueia até ter resultado

executor.shutdown();
```

```java
// CompletableFuture - mais flexível
CompletableFuture<String> cf = CompletableFuture
    .supplyAsync(() -> buscarDados())
    .thenApply(dados -> processar(dados))
    .thenApply(String::toUpperCase);

cf.thenAccept(System.out::println);
```
