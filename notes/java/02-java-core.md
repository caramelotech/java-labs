# Java Core

## Enums

Enums representam um conjunto fixo de constantes. São mais seguros que usar inteiros ou Strings soltas para representar estados e categorias.

```java
public enum DiaSemana {
    SEGUNDA, TERCA, QUARTA, QUINTA, SEXTA, SABADO, DOMINGO
}
```

```java
DiaSemana hoje = DiaSemana.SEXTA;

if (hoje == DiaSemana.SEXTA) {
    System.out.println("Quase fim de semana!");
}
```

Enums podem ter atributos e métodos:

```java
public enum Planeta {
    MERCURIO(3.303e+23, 2.4397e6),
    VENUS(4.869e+24, 6.0518e6),
    TERRA(5.976e+24, 6.37814e6);

    private final double massa;
    private final double raio;

    Planeta(double massa, double raio) {
        this.massa = massa;
        this.raio = raio;
    }

    public double massaEmKg() {
        return massa;
    }
}
```

Uso em `switch`:

```java
switch (hoje) {
    case SABADO, DOMINGO -> System.out.println("Fim de semana");
    default -> System.out.println("Dia útil");
}
```

## Exceções

Exceções representam situações anormais na execução do programa. Java usa um sistema hierárquico para classificá-las.

### Hierarquia

```
Throwable
├── Error (erros da JVM - não tente capturar)
└── Exception
    ├── RuntimeException (unchecked)
    │   ├── NullPointerException
    │   ├── ArrayIndexOutOfBoundsException
    │   ├── ClassCastException
    │   └── NumberFormatException
    └── IOException (checked)
        ├── FileNotFoundException
        └── ...
```

**Checked exceptions** devem ser declaradas ou tratadas obrigatoriamente. **Unchecked exceptions** (subclasses de `RuntimeException`) são opcionais.

### try / catch / finally

```java
try {
    int resultado = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Erro: " + e.getMessage());
} finally {
    System.out.println("Sempre executado, com ou sem erro");
}
```

Múltiplos `catch`:

```java
try {
    String texto = null;
    System.out.println(texto.length());
} catch (NullPointerException e) {
    System.out.println("Referência nula: " + e.getMessage());
} catch (Exception e) {
    System.out.println("Erro genérico: " + e.getMessage());
}
```

Captura múltipla (Java 7+):

```java
catch (IOException | SQLException e) {
    System.out.println("Erro de I/O ou banco: " + e.getMessage());
}
```

### throw e throws

`throw` lança uma exceção manualmente. `throws` declara que um método pode lançar uma checked exception:

```java
public double dividir(double a, double b) throws IllegalArgumentException {
    if (b == 0) {
        throw new IllegalArgumentException("Divisor não pode ser zero");
    }
    return a / b;
}
```

### Exceções customizadas

```java
public class SaldoInsuficienteException extends RuntimeException {
    private final double saldoAtual;

    public SaldoInsuficienteException(double saldoAtual) {
        super("Saldo insuficiente. Saldo atual: " + saldoAtual);
        this.saldoAtual = saldoAtual;
    }

    public double getSaldoAtual() {
        return saldoAtual;
    }
}
```

```java
public void sacar(double valor) {
    if (valor > saldo) {
        throw new SaldoInsuficienteException(saldo);
    }
    saldo -= valor;
}
```

### try-with-resources

Recursos que implementam `AutoCloseable` são fechados automaticamente:

```java
try (var reader = new BufferedReader(new FileReader("arquivo.txt"))) {
    String linha;
    while ((linha = reader.readLine()) != null) {
        System.out.println(linha);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

Equivale a chamar `reader.close()` no `finally`, mas sem o código repetitivo.

## Autoboxing e Unboxing

Java converte automaticamente entre tipos primitivos e seus wrappers (`Integer`, `Double`, `Boolean`, etc.):

```java
// Autoboxing: primitivo -> wrapper
Integer obj = 42;
List<Integer> lista = new ArrayList<>();
lista.add(10); // int vira Integer automaticamente

// Unboxing: wrapper -> primitivo
int valor = obj;
int soma = lista.get(0) + 5;
```

**Cuidado com null:** unboxing de um wrapper `null` lança `NullPointerException`:

```java
Integer numero = null;
int resultado = numero; // NullPointerException em runtime!
```

## Upcasting e Downcasting

### Upcasting

Conversão de subclasse para superclasse - sempre segura e implícita:

```java
Animal animal = new Cachorro(); // upcasting implícito
animal.emitirSom();
```

### Downcasting

Conversão de superclasse para subclasse - requer cast explícito e pode lançar `ClassCastException`:

```java
Animal animal = new Cachorro();

if (animal instanceof Cachorro) {
    Cachorro cachorro = (Cachorro) animal; // downcasting
    cachorro.buscarBola();
}
```

A partir do Java 16, `instanceof` com pattern matching elimina o cast manual:

```java
if (animal instanceof Cachorro cachorro) {
    cachorro.buscarBola(); // cachorro já está tipado como Cachorro
}
```

## Collections

O framework Collections oferece estruturas de dados prontas para uso: `List`, `Set` e `Map`, cada uma com várias implementações e características próprias de ordem, duplicatas e performance. Como o tema tem profundidade própria, ele ganhou uma nota dedicada: [Java Collections Framework](/labs/java/java/04-colecoes/).

## Generics

Generics permitem criar classes, interfaces e métodos parametrizados por tipo, garantindo segurança em tempo de compilação.

### Classes genéricas

```java
public class Caixa<T> {
    private T conteudo;

    public void guardar(T conteudo) {
        this.conteudo = conteudo;
    }

    public T abrir() {
        return conteudo;
    }
}

// Uso
Caixa<String> caixaTexto = new Caixa<>();
caixaTexto.guardar("Olá");
String texto = caixaTexto.abrir();

Caixa<Integer> caixaNumero = new Caixa<>();
caixaNumero.guardar(42);
```

### Métodos genéricos

```java
public static <T extends Comparable<T>> T maior(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}

// Funciona com qualquer tipo comparável
System.out.println(maior(10, 20));       // 20
System.out.println(maior("abc", "xyz")); // xyz
```

### Wildcards

`?` representa um tipo desconhecido. Útil para métodos que trabalham com coleções de diferentes tipos:

```java
// Aceita List<Integer>, List<Double>, List<Number>...
public double somarLista(List<? extends Number> lista) {
    double soma = 0;
    for (Number n : lista) {
        soma += n.doubleValue();
    }
    return soma;
}
```

## Classe Object

Toda classe em Java herda de `Object`. Três métodos que frequentemente se sobrescreve:

### toString()

Retorna uma representação em String do objeto. Chamado implicitamente em concatenação:

```java
public class Produto {
    private String nome;
    private double preco;

    @Override
    public String toString() {
        return nome + " - R$ " + String.format("%.2f", preco);
    }
}

Produto p = new Produto("Caneta", 2.50);
System.out.println(p); // "Caneta - R$ 2,50"
```

### equals() e hashCode()

Por padrão, `equals()` compara referências de memória. Para comparar conteúdo, sobrescreva:

```java
public class Ponto {
    private int x;
    private int y;

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Ponto outro)) return false;
        return this.x == outro.x && this.y == outro.y;
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }
}
```

**Regra:** sempre que sobrescrever `equals()`, sobrescreva `hashCode()` também. Objetos iguais devem ter o mesmo hash.

### BigDecimal e a armadilha do equals()

`BigDecimal` é o tipo certo para dinheiro e qualquer número decimal onde precisão importa. Mas o `equals()` dele tem um comportamento que pega quase todo mundo de surpresa:

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("1.00");

System.out.println(a.equals(b)); // false 😬
```

Os dois representam o mesmo número, mas o `equals()` do `BigDecimal` compara **valor e escala**. Escala é a quantidade de casas decimais: `"1.0"` tem escala 1, `"1.00"` tem escala 2. Como as escalas diferem, `equals()` diz que são diferentes.

Quem compara só o valor numérico é o `compareTo()`:

```java
System.out.println(a.compareTo(b) == 0); // true
```

A regra prática: use `equals()` quando a escala faz parte da identidade do valor (raro), e `compareTo() == 0` quando você só quer saber se os números são iguais (o caso comum).

**Cuidado:** estruturas que dependem de `equals()` herdam esse comportamento. `BigDecimal` como chave de `HashMap`, elemento de `Set`, ou passado para `List.contains()` e `Stream.distinct()` trata `1.0` e `1.00` como entradas distintas. Se você precisa que valores numericamente iguais colidam, normalize a escala antes (`valor.setScale(2, RoundingMode.HALF_UP)`) ou não use `BigDecimal` como chave.

## Strings em Profundidade

### Imutabilidade

Strings em Java são imutáveis - toda operação cria um novo objeto:

```java
String a = "Java";
String b = a.toUpperCase(); // "JAVA" - novo objeto
// a ainda é "Java"
```

### Métodos principais

```java
String texto = "  Olá, Mundo!  ";

texto.trim()                    // "Olá, Mundo!" - remove espaços nas bordas
texto.strip()                   // igual ao trim(), mas suporta Unicode
texto.length()                  // 15
texto.toUpperCase()             // "  OLÁ, MUNDO!  "
texto.toLowerCase()             // "  olá, mundo!  "
texto.contains("Mundo")         // true
texto.startsWith("  Olá")       // true
texto.endsWith("!  ")           // true
texto.replace("Mundo", "Java")  // "  Olá, Java!  "
texto.substring(2, 6)           // "Olá,"
texto.split(",")                // ["  Olá", " Mundo!  "]
texto.indexOf("M")              // 7
String.valueOf(42)              // "42"
Integer.parseInt("123")         // 123
```

### StringBuilder

Para concatenações em loop, use `StringBuilder` - é mutável e muito mais eficiente:

```java
StringBuilder sb = new StringBuilder();
for (int i = 1; i <= 100; i++) {
    sb.append(i);
    if (i < 100) sb.append(", ");
}
String resultado = sb.toString();
```

## Manipulação de Arquivos

### Leitura

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

try (var reader = new BufferedReader(new FileReader("dados.txt"))) {
    String linha;
    while ((linha = reader.readLine()) != null) {
        System.out.println(linha);
    }
} catch (IOException e) {
    System.err.println("Erro ao ler arquivo: " + e.getMessage());
}
```

### Escrita

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

try (var writer = new BufferedWriter(new FileWriter("saida.txt"))) {
    writer.write("Primeira linha");
    writer.newLine();
    writer.write("Segunda linha");
} catch (IOException e) {
    System.err.println("Erro ao escrever arquivo: " + e.getMessage());
}
```

### java.nio.file (moderno)

```java
import java.nio.file.Files;
import java.nio.file.Path;

Path caminho = Path.of("dados.txt");

// Ler todas as linhas de uma vez
List<String> linhas = Files.readAllLines(caminho);

// Escrever todas as linhas de uma vez
Files.writeString(caminho, "conteúdo do arquivo");

// Verificar existência
boolean existe = Files.exists(caminho);
```
