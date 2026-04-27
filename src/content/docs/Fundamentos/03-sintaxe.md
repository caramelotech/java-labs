---
title: "Sintaxe Java"
description: "Regras de escrita, tipos de dados primitivos, variáveis, strings e convenções de nomenclatura em Java"
lastUpdated: 2026-04-26
sidebar:
  order: 3
tags: ["java", "sintaxe", "variáveis", "tipos-de-dados"]
---

A sintaxe Java é o conjunto de regras que define como um programa Java deve ser escrito. Compreender a sintaxe é fundamental para escrever código que o compilador Java possa entender e executar corretamente.

Neste guia, você aprenderá os fundamentos da sintaxe Java de forma progressiva, começando pelos conceitos mais básicos até estruturas mais complexas.

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

## 1. Estrutura Básica de um Programa Java

Todo programa Java começa com uma estrutura básica. Vamos entender cada parte:

```java
public class MeuPrimeiroPrograma {
    public static void main(String[] args) {
        System.out.println("Olá, Mundo!");
    }
}
```

### Componentes da Estrutura:

- **`public class MeuPrimeiroPrograma`**: Declaração da classe pública
- **`public static void main(String[] args)`**: Método principal (ponto de entrada do programa)
- **`System.out.println()`**: Comando para imprimir texto na tela
- **Chaves `{}`**: Delimitam blocos de código
- **Ponto e vírgula `;`**: Marca o fim de uma instrução

> **Importante**: O nome do arquivo Java deve ser idêntico ao nome da classe pública, incluindo maiúsculas e minúsculas. Por exemplo, `MeuPrimeiroPrograma.java`.

## 2. Comentários

Comentários são textos ignorados pelo compilador, usados para documentar o código e facilitar o entendimento.

### Tipos de Comentários:

```java
// Comentário de linha única
// Use para explicações breves

/*
 * Comentário de múltiplas linhas
 * Use para explicações mais longas
 * ou para desabilitar temporariamente blocos de código
 */

/**
 * Comentário de documentação (JavaDoc)
 * Usado para gerar documentação automática
 * @author Seu Nome
 * @version 1.0
 */
```

### Boas Práticas com Comentários:

- Explique o "porquê", não o "o quê" (o código deve ser autoexplicativo)
- Mantenha os comentários atualizados com o código
- Use JavaDoc para documentar classes e métodos públicos
- Evite comentários óbvios que apenas repetem o código

## 3. Tipos de Dados Primitivos

Java possui 8 tipos primitivos, mas no dia a dia você vai usar principalmente 4:

| Tipo      | Para quê                          | Exemplo             |
| --------- | --------------------------------- | ------------------- |
| `int`     | Números inteiros                  | `int idade = 25;`   |
| `double`  | Números decimais                  | `double preco = 9.99;` |
| `boolean` | Verdadeiro ou falso               | `boolean ativo = true;` |
| `char`    | Um único caractere                | `char letra = 'A';` |

Os outros quatro (`byte`, `short`, `long`, `float`) existem para casos específicos de performance ou precisão - você vai encontrá-los eventualmente, mas não precisa memorizá-los agora.

### Exemplos Práticos:

```java
// Números inteiros
int idade = 25;
long populacaoMundial = 7800000000L; // Note o 'L' no final

// Números decimais
double preco = 19.99;
float temperatura = 36.5f; // Note o 'f' no final

// Booleanos
boolean estaAtivo = true;
boolean temDesconto = false;

// Caracteres
char inicial = 'A';
char simbolo = '@';
```

## 4. Variáveis

Variáveis são contêineres para armazenar valores de dados. Em Java, cada variável deve ter um tipo específico.

### Declaração e Inicialização:

```java
// Declaração simples
int numero;

// Declaração com inicialização
int quantidade = 10;

// Múltiplas declarações do mesmo tipo
int x = 5, y = 10, z = 15;

// Constantes (valores que não mudam)
final double PI = 3.14159;
final int DIAS_DA_SEMANA = 7;
```

### Convenções de Nomenclatura para Variáveis:

#### Regras Obrigatórias:

1. Deve começar com letra, `$` ou `_` (underscore)
2. Não pode começar com número
3. Não pode usar palavras reservadas do Java
4. É case-sensitive (`idade` ≠ `Idade`)

#### Boas Práticas (Convenção camelCase):

```java
// BOM - Use camelCase (primeira palavra minúscula, demais iniciam com maiúscula)
int idadeDoUsuario;
String nomeCompleto;
double salarioMensal;
boolean estaCadastrado;

// BOM - Nomes descritivos e significativos
int quantidadeDeAlunos;
double precoComDesconto;

// BOM - Constantes em MAIÚSCULAS com underscore
final int IDADE_MINIMA = 18;
final double TAXA_DE_JUROS = 0.05;

// RUIM - Nomes muito curtos ou sem significado
int a, b, c;
String s;

// RUIM - Não usar underscores em variáveis normais
int nome_do_usuario; // Evite, use camelCase

// RUIM - Começar com letra maiúscula (reservado para classes)
int NomeDoUsuario;
```

### Escopo de Variáveis:

```java
public class ExemploEscopo {
    // Variável de instância (pertence à classe)
    private int valorGlobal = 100;

    public void metodoExemplo() {
        // Variável local (existe apenas dentro deste método)
        int valorLocal = 50;

        if (valorLocal > 0) {
            // Variável de bloco (existe apenas dentro deste bloco if)
            int valorTemporario = 10;
            System.out.println(valorTemporario);
        }
        // valorTemporario não existe aqui!
    }
}
```

## 5. Strings

String é um tipo de referência (não primitivo) usado para armazenar texto.

```java
// Declaração de Strings
String nome = "Maria Silva";
String mensagem = "Bem-vindo ao Java!";

// Concatenação
String primeiroNome = "João";
String sobrenome = "Santos";
String nomeCompleto = primeiroNome + " " + sobrenome;

// Métodos úteis de String
int tamanho = nome.length();              // Retorna o tamanho
String maiuscula = nome.toUpperCase();    // Converte para maiúsculas
String minuscula = nome.toLowerCase();    // Converte para minúsculas
boolean contem = nome.contains("Maria");  // Verifica se contém um texto
```

### Boas Práticas com Strings:

```java
// Use aspas duplas para Strings
String texto = "Olá, Mundo!";

// Concatene com + para casos simples
String saudacao = "Olá, " + nome + "!";

// Não confunda aspas simples (char) com aspas duplas (String)
char letra = 'A';    // char - um único caractere
String nome = "Ana"; // String - qualquer texto
```

## 6. Resumo das Boas Práticas

### Nomenclatura:

| Elemento  | Convenção  | Exemplo               |
| --------- | ---------- | --------------------- |
| Classe    | PascalCase | `ContaBancaria`       |
| Método    | camelCase  | `calcularSaldo()`     |
| Variável  | camelCase  | `saldoAtual`          |
| Constante | MAIÚSCULAS | `TAXA_JUROS`          |
| Pacote    | minúsculas | `com.empresa.projeto` |

### Princípios Gerais:

1. **Legibilidade**: Escreva código que outros possam entender facilmente
2. **Nomenclatura Clara**: Use nomes descritivos e significativos
3. **Consistência**: Siga as mesmas convenções em todo o projeto
4. **Comentários Úteis**: Documente o "porquê", não o "o quê"

## Verifique seu Entendimento

Antes de avançar, tente responder mentalmente:

1. Qual a diferença entre `int` e `double`? Quando usar cada um?
2. O que acontece se você tentar usar uma palavra reservada como nome de variável?
3. Qual o nome correto para uma variável que guarda o preço de um produto - `p`, `preco_produto` ou `precoProduto`?
4. `String` é um tipo primitivo? Por que não?

<details>
<summary>Ver respostas</summary>

1. `int` armazena números inteiros (sem casas decimais); `double` armazena números decimais. Use `int` para idade, quantidade, contadores - e `double` para preço, peso, temperatura.
2. O compilador retorna um erro de sintaxe - palavras reservadas têm significado especial para o Java e não podem ser redefinidas.
3. `precoProduto` - camelCase é a convenção Java para variáveis. `p` é pouco descritivo e `preco_produto` usa underscore (convenção de outras linguagens, não de Java).
4. Não. `String` é uma classe (tipo de referência), não um primitivo. Por isso começa com maiúscula e tem métodos como `.length()` e `.toUpperCase()`.

</details>

## Exercícios Práticos

1. Declare variáveis dos 4 tipos principais (`int`, `double`, `boolean`, `char`) com valores e imprima cada uma com `System.out.println()`
2. Crie uma variável `String` com seu nome completo e imprima quantos caracteres ela tem (dica: `.length()`)
3. Declare uma constante `VELOCIDADE_LUZ` com o valor `299792458` e imprima uma mensagem usando ela
4. Corrija os nomes das variáveis abaixo para seguir as convenções Java:
   ```java
   int Idade_usuario = 20;
   String nome_completo = "João";
   final double taxajuros = 0.05;
   ```

## Próximos Passos

Com a sintaxe básica dominada, o próximo passo é aprender os **operadores** - como fazer cálculos, comparações e combinar condições lógicas.
