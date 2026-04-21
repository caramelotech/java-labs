---
title: "Operadores em Java"
description: "Operadores aritméticos, relacionais, lógicos, unários e ternário em Java"
lastUpdated: 2026-04-21
sidebar:
  order: 3
tags: ["java", "operadores", "iniciante"]
---

Neste capítulo você aprenderá:

- Operadores aritméticos e de atribuição
- Operadores relacionais
- Operadores unários
- Operadores lógicos
- Operador ternário

Esses conceitos são fundamentais para escrever condições, cálculos e lógica em qualquer programa Java.

## Operadores de Atribuição e Aritméticos

### Operador de Atribuição

O operador de atribuição é usado para armazenar um valor em uma variável.

```java
int idade = 25;
```

O símbolo `=` significa:

> Receba o valor da direita e armazene na variável da esquerda.

### Operadores Aritméticos

São usados para realizar cálculos matemáticos.

| Operador | Significado               | Exemplo |
| -------- | ------------------------- | ------- |
| `+`      | Soma                      | a + b   |
| `-`      | Subtração                 | a - b   |
| `*`      | Multiplicação             | a \* b  |
| `/`      | Divisão                   | a / b   |
| `%`      | Resto da divisão (módulo) | a % b   |

#### Exemplo

```java
int a = 10;
int b = 3;

int soma = a + b;       // 13
int sub = a - b;        // 7
int mult = a * b;       // 30
int div = a / b;        // 3
int resto = a % b;      // 1
```

> ⚠️ **Armadilha: divisão inteira**
>
> Quando os dois operandos são `int`, o resultado é sempre inteiro — a parte decimal é descartada, sem arredondamento.
>
> ```java
> int resultado = 10 / 3;      // 3, não 3.33
> double correto = 10.0 / 3;   // 3.3333... (um dos lados deve ser double)
> ```
>
> Esse é um dos erros mais comuns para quem está começando.

### Operadores de Atribuição Combinados

Java permite simplificar operações com atribuição.

| Operador | Equivalente |
| -------- | ----------- |
| `+=`     | a = a + b   |
| `-=`     | a = a - b   |
| `*=`     | a = a \* b  |
| `/=`     | a = a / b   |
| `%=`     | a = a % b   |

#### Exemplo

```java
int x = 10;

x += 5;  // x = 15
x -= 3;  // x = 12
x *= 2;  // x = 24
```

## Operadores Relacionais

São usados para comparar valores.

O resultado sempre será um **boolean** (`true` ou `false`).

| Operador | Significado    |
| -------- | -------------- |
| `==`     | Igual          |
| `!=`     | Diferente      |
| `>`      | Maior          |
| `<`      | Menor          |
| `>=`     | Maior ou igual |
| `<=`     | Menor ou igual |

### Exemplo

```java
int idade = 18;

System.out.println(idade >= 18); // true
System.out.println(idade < 18);  // false
```

⚠️ Atenção importante:

```java
==   → comparação
=    → atribuição
```

Erro comum de iniciantes!

## Operadores Unários

Operam sobre apenas **um operando**.

### Principais Operadores Unários

| Operador | Função         |
| -------- | -------------- |
| `+`      | Valor positivo |
| `-`      | Negativo       |
| `++`     | Incremento     |
| `--`     | Decremento     |
| `!`      | Negação lógica |

### Incremento e Decremento

#### Pré-incremento

Incrementa antes de usar.

```java
int x = 5;
int y = ++x; // x = 6, y = 6
```

#### Pós-incremento

Incrementa depois de usar.

```java
int x = 5;
int y = x++; // x = 6, y = 5
```

### Negação Lógica

```java
boolean ativo = true;

System.out.println(!ativo); // false
```

## Operadores Lógicos

Usados para combinar expressões booleanas.

| Operador | Nome | Significado |
| -------- | ---- | ----------- |
| `&&`     | AND  | E lógico    |
| `\|\|`   | OR   | OU lógico   |
| `!`      | NOT  | Negação     |

### Exemplo

```java
int idade = 20;
boolean temCarteira = true;

if (idade >= 18 && temCarteira) {
    System.out.println("Pode dirigir");
}
```

## Operador Ternário

O operador ternário é uma forma reduzida de escrever um `if-else`.

### Sintaxe

```java
condicao ? valorSeVerdadeiro : valorSeFalso;
```

### Exemplo

```java
int idade = 18;

String resultado = (idade >= 18) ? "Maior de idade" : "Menor de idade";

System.out.println(resultado);
```

Equivalente ao:

```java
String resultado;

if (idade >= 18) {
    resultado = "Maior de idade";
} else {
    resultado = "Menor de idade";
}
```

## Boas Práticas Importantes

✅ Use nomes claros para variáveis
✅ Evite comentários óbvios
✅ Prefira JavaDoc em APIs públicas
✅ Use operadores com clareza
✅ Evite ternário complexo

## Verifique seu Entendimento

Antes de avançar, tente responder mentalmente:

1. Qual o resultado de `7 / 2` em Java? E de `7.0 / 2`?
2. Qual a diferença entre `==` e `=`?
3. O que `++x` faz diferente de `x++` quando usado dentro de uma expressão?
4. Reescreva o código abaixo sem usar o operador ternário:
   ```java
   String msg = (nota >= 7) ? "Aprovado" : "Reprovado";
   ```

<details>
<summary>Ver respostas</summary>

1. `7 / 2` resulta em `3` (divisão inteira, parte decimal descartada). `7.0 / 2` resulta em `3.5` (um dos operandos é `double`).
2. `==` **compara** dois valores (retorna `true` ou `false`). `=` **atribui** um valor a uma variável. Confundir os dois é um erro clássico de iniciante.
3. `++x` incrementa primeiro e depois usa o valor. `x++` usa o valor primeiro e incrementa depois. Ex: se `x = 5`, então `y = ++x` resulta em `x=6, y=6`; mas `y = x++` resulta em `x=6, y=5`.
4.
```java
String msg;
if (nota >= 7) {
    msg = "Aprovado";
} else {
    msg = "Reprovado";
}
```

</details>

## Resumo

Neste capítulo você aprendeu:

- Operadores aritméticos e de atribuição (incluindo a armadilha da divisão inteira)
- Operadores relacionais e a diferença entre `==` e `=`
- Operadores unários e o comportamento de pré vs pós-incremento
- Operadores lógicos (`&&`, `||`, `!`) para combinar condições
- Operador ternário como forma compacta de `if-else`

## Próximos Passos

Com operadores dominados, você tem o necessário para escrever condições reais. O próximo passo é aprender as **estruturas de controle** — `if`, `else` e `switch` — para que seu programa tome decisões com base nesses operadores.
