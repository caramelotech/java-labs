---
title: "Controle de Fluxo"
description: "Entrada e saída de dados, condicionais e estruturas de repetição em Java"
lastUpdated: 2026-04-26
sidebar:
  order: 5
tags: ["java", "controle-de-fluxo", "condicionais", "loops"]
---

## Entrada e Saída de Dados

### Saída com System.out

Para exibir informações na tela, Java usa `System.out`. Há três variações:

```java
System.out.println("Com quebra de linha");
System.out.print("Sem quebra de linha");
System.out.printf("Formatado: %s tem %d anos%n", "Ana", 25);
```

O `printf` aceita especificadores de formato como `%s` (String), `%d` (inteiro), `%f` (decimal) e `%n` (nova linha).

### Leitura com Scanner

Para ler dados do teclado, usa-se a classe `Scanner` do pacote `java.util`:

```java
import java.util.Scanner;

public class EntradaDados {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Digite seu nome: ");
        String nome = scanner.nextLine();

        System.out.print("Digite sua idade: ");
        int idade = scanner.nextInt();

        System.out.println("Olá, " + nome + "! Você tem " + idade + " anos.");

        scanner.close();
    }
}
```

#### Métodos de leitura

| Método          | Tipo lido        |
| --------------- | ---------------- |
| `nextLine()`    | String (linha)   |
| `next()`        | String (palavra) |
| `nextInt()`     | int              |
| `nextDouble()`  | double           |
| `nextBoolean()` | boolean          |

> **Atenção:** `nextInt()` e similares não consomem a quebra de linha. Se quiser ler uma String depois, chame `scanner.nextLine()` uma vez antes para limpar o buffer.

```java
int numero = scanner.nextInt();
scanner.nextLine(); // limpa o \n residual
String texto = scanner.nextLine(); // agora funciona
```

## Condicionais

### if / else if / else

Executa blocos de código com base em condições:

```java
int nota = 75;

if (nota >= 90) {
    System.out.println("Aprovado com distinção");
} else if (nota >= 60) {
    System.out.println("Aprovado");
} else {
    System.out.println("Reprovado");
}
```

As condições são expressões booleanas - qualquer combinação de operadores relacionais e lógicos é válida:

```java
int idade = 20;
boolean temCarteira = true;

if (idade >= 18 && temCarteira) {
    System.out.println("Pode dirigir");
}
```

### switch

Útil quando há múltiplos casos baseados em um único valor:

```java
int diaSemana = 3;

switch (diaSemana) {
    case 1:
        System.out.println("Segunda-feira");
        break;
    case 2:
        System.out.println("Terça-feira");
        break;
    case 3:
        System.out.println("Quarta-feira");
        break;
    default:
        System.out.println("Outro dia");
}
```

O `break` é obrigatório para evitar que a execução "caia" no próximo case (fall-through). O `default` é executado quando nenhum case corresponde.

#### switch com Strings

A partir do Java 7, `switch` aceita Strings:

```java
String cor = "vermelho";

switch (cor) {
    case "vermelho":
        System.out.println("Pare");
        break;
    case "verde":
        System.out.println("Siga");
        break;
    default:
        System.out.println("Atenção");
}
```

#### switch expression (Java 14+)

A sintaxe moderna com `->` elimina o `break` e pode retornar um valor:

```java
String resultado = switch (diaSemana) {
    case 1 -> "Segunda-feira";
    case 2 -> "Terça-feira";
    case 3 -> "Quarta-feira";
    case 4 -> "Quinta-feira";
    case 5 -> "Sexta-feira";
    default -> "Final de semana";
};
```

## Estruturas de Repetição

### for

Ideal quando o número de iterações é conhecido:

```java
for (int i = 0; i < 5; i++) {
    System.out.println("Iteração: " + i);
}
```

A estrutura tem três partes: inicialização, condição e incremento. Qualquer uma delas pode ser omitida:

```java
// Loop infinito (cuidado)
for (;;) {
    // ...
}
```

### for-each

Forma simplificada para iterar sobre arrays e coleções:

```java
int[] numeros = {10, 20, 30, 40, 50};

for (int numero : numeros) {
    System.out.println(numero);
}
```

Não dá acesso ao índice, mas é mais legível quando ele não é necessário.

### while

Repete enquanto a condição for verdadeira. A condição é verificada antes de cada iteração:

```java
int contador = 0;

while (contador < 5) {
    System.out.println("Contador: " + contador);
    contador++;
}
```

Útil quando não se sabe de antemão quantas iterações serão necessárias:

```java
Scanner scanner = new Scanner(System.in);
String entrada = "";

while (!entrada.equals("sair")) {
    System.out.print("Digite algo (ou 'sair' para encerrar): ");
    entrada = scanner.nextLine();
    System.out.println("Você digitou: " + entrada);
}
```

### do-while

Garante que o bloco seja executado pelo menos uma vez, pois a condição é verificada após a execução:

```java
int numero;
Scanner scanner = new Scanner(System.in);

do {
    System.out.print("Digite um número entre 1 e 10: ");
    numero = scanner.nextInt();
} while (numero < 1 || numero > 10);

System.out.println("Número válido: " + numero);
```

### break e continue

`break` interrompe o loop imediatamente:

```java
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break; // para ao chegar no 5
    }
    System.out.println(i);
}
```

`continue` pula para a próxima iteração sem executar o resto do bloco:

```java
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) {
        continue; // pula os números pares
    }
    System.out.println(i); // imprime só os ímpares
}
```

## Exemplo Completo

Um programa que lê números do usuário até que ele digite zero, e ao final exibe a soma e a média:

```java
import java.util.Scanner;

public class Estatisticas {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int soma = 0;
        int quantidade = 0;

        System.out.println("Digite números (0 para encerrar):");

        while (true) {
            int numero = scanner.nextInt();

            if (numero == 0) {
                break;
            }

            soma += numero;
            quantidade++;
        }

        if (quantidade > 0) {
            double media = (double) soma / quantidade;
            System.out.println("Soma: " + soma);
            System.out.printf("Média: %.2f%n", media);
        } else {
            System.out.println("Nenhum número informado.");
        }

        scanner.close();
    }
}
```
