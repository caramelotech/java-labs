# Sintaxe Java

Este documento cobre os conceitos fundamentais de sintaxe em Java, essenciais para iniciantes. Você aprenderá desde a estrutura básica de um programa até boas práticas de codificação.

### Sintaxe vs Semântica

**Sintaxe** refere-se às regras de escrita do código (como escrever comandos). **Semântica** refere-se ao significado (o que o código faz).

```java
// Sintaxe correta, semântica errada
int numero = "texto";  // Erro: tentando atribuir texto a um inteiro

// Sintaxe e semântica corretas
int numero = 42;
```

### Palavras Reservadas

Java possui palavras reservadas que não podem ser usadas como nomes de variáveis ou classes:

`public`, `private`, `class`, `int`, `if`, `else`, `while`, `for`, `return`, `final`, `static`, `void`, `true`, `false`, `null`

### Escopos

- **Escopo Local:** Variáveis declaradas dentro de métodos
- **Escopo de Classe:** Variáveis declaradas dentro da classe (atributos)
- **Escopo Global:** Variáveis `static` acessíveis em todo o programa

```java
public class Exemplo {
    static int global = 10;      // Escopo global
    int classico = 20;           // Escopo de classe

    void metodo() {
        int local = 30;          // Escopo local
    }
}
```

### Tipos Wrapper e Classes Externas

Tipos primitivos têm equivalentes em classes (Wrapper types): `Integer`, `Double`, `Boolean`, `String`

```java
Integer numero = 42;            // Wrapper type
int primitivo = numero;         // Conversão automática
```

### Estrutura de Métodos

Um método possui assinatura (nome, parâmetros, tipo de retorno):

```java
public static int somar(int a, int b) {
    return a + b;
}
// Modificador | Tipo de retorno | Nome | Parâmetros
```

## 1. Estrutura Básica de um Programa Java

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Olá, Mundo!");
    }
}
```

**Pontos importantes:**

- Toda classe deve ter um nome que corresponde ao arquivo (ex: `HelloWorld.java`)
- O método `main` é o ponto de entrada do programa
- Java é case-sensitive (maiúsculas/minúsculas importam)

## 2. Variáveis e Tipos de Dados

```java
int idade = 25;           // Número inteiro
double altura = 1.75;     // Número decimal
String nome = "João";     // Texto
boolean ativo = true;     // Booleano
```

**Boa prática:** Use nomes descritivos e em camelCase para variáveis.

## 3. Operadores

```java
// Aritméticos
int soma = 10 + 5;
int diferenca = 10 - 5;

// Comparação
boolean resultado = (10 > 5);  // true

// Lógicos
boolean condicao = (idade > 18) && (nome != null);
```

## 4. Estruturas de Controle

### If-Else

```java
if (idade >= 18) {
    System.out.println("Maior de idade");
} else {
    System.out.println("Menor de idade");
}
```

### Switch

```java
switch (dia) {
    case 1:
        System.out.println("Segunda");
        break;
    default:
        System.out.println("Outro dia");
}
```

## 5. Loops

```java
// For
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}

// While
while (contador < 10) {
    contador++;
}
```

## 6. Boas Práticas de Sintaxe

✓ Use indentação consistente (2 ou 4 espaços)  
✓ Declare constantes em UPPERCASE: `final int MAX_USUARIOS = 100;`  
✓ Evite nomes genéricos: `x`, `temp` ❌  
✓ Use comentários para lógica complexa  
✓ Mantenha linhas com máximo 100 caracteres
