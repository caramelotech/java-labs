# 02 - Sintaxe Java

## Introdução

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

---

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

---

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

- ✅ Explique o "porquê", não o "o quê" (o código deve ser autoexplicativo)
- ✅ Mantenha os comentários atualizados com o código
- ✅ Use JavaDoc para documentar classes e métodos públicos
- ❌ Evite comentários óbvios que apenas repetem o código

---

## 3. Tipos de Dados Primitivos

Java possui 8 tipos de dados primitivos, divididos em categorias:

### Números Inteiros:

| Tipo    | Tamanho | Intervalo                                    | Uso Comum                    |
|---------|---------|----------------------------------------------|------------------------------|
| `byte`  | 8 bits  | -128 a 127                                   | Economizar memória           |
| `short` | 16 bits | -32.768 a 32.767                             | Valores inteiros pequenos    |
| `int`   | 32 bits | -2.147.483.648 a 2.147.483.647               | **Tipo padrão para inteiros**|
| `long`  | 64 bits | -9.223.372.036.854.775.808 a ...             | Valores muito grandes        |

### Números de Ponto Flutuante:

| Tipo     | Tamanho | Precisão        | Uso Comum                       |
|----------|---------|-----------------|----------------------------------|
| `float`  | 32 bits | 6-7 dígitos     | Valores decimais com menos precisão |
| `double` | 64 bits | 15 dígitos      | **Tipo padrão para decimais**   |

### Outros Tipos:

| Tipo      | Tamanho | Valores          | Uso                              |
|-----------|---------|------------------|----------------------------------|
| `boolean` | 1 bit   | `true` ou `false`| Lógica e condições               |
| `char`    | 16 bits | 0 a 65.535       | Caractere Unicode único          |

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

---

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

#### ✅ **Regras Obrigatórias:**

1. Deve começar com letra, `$` ou `_` (underscore)
2. Não pode começar com número
3. Não pode usar palavras reservadas do Java
4. É case-sensitive (`idade` ≠ `Idade`)

#### ✅ **Boas Práticas (Convenção camelCase):**

```java
// ✅ BOM - Use camelCase (primeira palavra minúscula, demais iniciam com maiúscula)
int idadeDoUsuario;
String nomeCompleto;
double salarioMensal;
boolean estaCadastrado;

// ✅ BOM - Nomes descritivos e significativos
int quantidadeDeAlunos;
double precoComDesconto;

// ✅ BOM - Constantes em MAIÚSCULAS com underscore
final int IDADE_MINIMA = 18;
final double TAXA_DE_JUROS = 0.05;

// ❌ RUIM - Nomes muito curtos ou sem significado
int a, b, c;
String s;

// ❌ RUIM - Não usar underscores em variáveis normais
int nome_do_usuario; // Evite, use camelCase

// ❌ RUIM - Começar com letra maiúscula (reservado para classes)
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

---

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
// ✅ BOM - Use aspas duplas para Strings
String texto = "Olá, Mundo!";

// ✅ BOM - Use StringBuilder para concatenações em loops
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}

// ❌ RUIM - Concatenação em loops (ineficiente)
String resultado = "";
for (int i = 0; i < 1000; i++) {
    resultado += i; // Cria muitos objetos temporários
}
```

---

## 6. Classes e Objetos

Classes são modelos (blueprints) para criar objetos. Objetos são instâncias de classes.

### Estrutura de uma Classe:

```java
/**
 * Classe que representa um Estudante
 * @author Seu Nome
 */
public class Estudante {
    // Atributos (características)
    private String nome;
    private int idade;
    private double nota;
    
    // Construtor (cria novos objetos)
    public Estudante(String nome, int idade, double nota) {
        this.nome = nome;
        this.idade = idade;
        this.nota = nota;
    }
    
    // Métodos (comportamentos)
    public void estudar() {
        System.out.println(nome + " está estudando.");
    }
    
    public boolean estaAprovado() {
        return nota >= 7.0;
    }
    
    // Getters e Setters
    public String getNome() {
        return nome;
    }
    
    public void setNome(String nome) {
        this.nome = nome;
    }
}
```

### Criando e Usando Objetos:

```java
public class TesteEstudante {
    public static void main(String[] args) {
        // Criando objetos
        Estudante aluno1 = new Estudante("Carlos", 20, 8.5);
        Estudante aluno2 = new Estudante("Ana", 19, 6.5);
        
        // Usando métodos
        aluno1.estudar();
        
        if (aluno1.estaAprovado()) {
            System.out.println(aluno1.getNome() + " foi aprovado!");
        }
    }
}
```

---

## 7. Convenções de Nomenclatura para Classes

As convenções de nomenclatura tornam o código mais legível e profissional.

### ✅ **Regras para Classes:**

```java
// ✅ BOM - PascalCase (cada palavra começa com maiúscula)
public class ContaBancaria { }
public class UsuarioSistema { }
public class RelatorioVendas { }

// ✅ BOM - Substantivos descritivos
public class Pessoa { }
public class Produto { }
public class Cliente { }

// ✅ BOM - Nomes específicos e significativos
public class GerenciadorDeArquivos { }
public class ProcessadorDePagamentos { }

// ❌ RUIM - Começar com minúscula
public class contaBancaria { }

// ❌ RUIM - Usar underscores
public class Conta_Bancaria { }

// ❌ RUIM - Nomes genéricos ou sem significado
public class Classe1 { }
public class Dados { }
public class Teste { }
```

### ✅ **Regras para Pacotes:**

```java
// ✅ BOM - Tudo em minúsculas
package com.empresa.projeto.modulo;
package br.com.meusite.utils;

// ❌ RUIM - Usar maiúsculas
package Com.Empresa.Projeto;
```

### ✅ **Regras para Métodos:**

```java
// ✅ BOM - camelCase (começa com minúscula)
public void calcularTotal() { }
public void enviarEmail() { }
public boolean verificarDisponibilidade() { }

// ✅ BOM - Verbos que descrevem a ação
public void salvar() { }
public void atualizar() { }
public String obterNome() { }
public boolean isAtivo() { } // Prefixo 'is' para métodos booleanos

// ❌ RUIM - Começar com maiúscula
public void CalcularTotal() { }

// ❌ RUIM - Nomes não descritivos
public void fazer() { }
public void processar() { } // Muito genérico
```

---

## 8. Modificadores de Acesso

Controlam a visibilidade de classes, atributos e métodos.

| Modificador   | Classe | Pacote | Subclasse | Todos |
|---------------|--------|--------|-----------|-------|
| `public`      | ✅     | ✅     | ✅        | ✅    |
| `protected`   | ✅     | ✅     | ✅        | ❌    |
| `default`     | ✅     | ✅     | ❌        | ❌    |
| `private`     | ✅     | ❌     | ❌        | ❌    |

### Boas Práticas:

```java
public class Exemplo {
    // ✅ BOM - Atributos privados (encapsulamento)
    private String nome;
    private int idade;
    
    // ✅ BOM - Métodos públicos para acesso controlado
    public String getNome() {
        return nome;
    }
    
    public void setNome(String nome) {
        // Validação antes de atribuir
        if (nome != null && !nome.isEmpty()) {
            this.nome = nome;
        }
    }
    
    // ✅ BOM - Métodos auxiliares privados
    private void validarDados() {
        // Lógica interna da classe
    }
}
```

---

## 9. Operadores Básicos

### Operadores Aritméticos:

```java
int a = 10, b = 3;

int soma = a + b;        // 13
int subtracao = a - b;   // 7
int multiplicacao = a * b; // 30
int divisao = a / b;     // 3 (divisão inteira)
int resto = a % b;       // 1 (módulo/resto)

// Incremento e Decremento
int contador = 0;
contador++;  // contador = 1
contador--;  // contador = 0
```

### Operadores de Comparação:

```java
int x = 5, y = 10;

boolean igual = (x == y);           // false
boolean diferente = (x != y);       // true
boolean maior = (x > y);            // false
boolean menor = (x < y);            // true
boolean maiorIgual = (x >= y);      // false
boolean menorIgual = (x <= y);      // true
```

### Operadores Lógicos:

```java
boolean a = true, b = false;

boolean e = a && b;      // false (E lógico - AND)
boolean ou = a || b;     // true  (OU lógico - OR)
boolean nao = !a;        // false (NÃO lógico - NOT)
```

---

## 10. Resumo das Boas Práticas

### Nomenclatura:

| Elemento   | Convenção    | Exemplo                    |
|------------|--------------|----------------------------|
| Classe     | PascalCase   | `ContaBancaria`            |
| Método     | camelCase    | `calcularSaldo()`          |
| Variável   | camelCase    | `saldoAtual`               |
| Constante  | MAIÚSCULAS   | `TAXA_JUROS`               |
| Pacote     | minúsculas   | `com.empresa.projeto`      |

### Princípios Gerais:

1. **Legibilidade**: Escreva código que outros possam entender facilmente
2. **Nomenclatura Clara**: Use nomes descritivos e significativos
3. **Consistência**: Siga as mesmas convenções em todo o projeto
4. **Encapsulamento**: Mantenha atributos privados e forneça métodos públicos
5. **Comentários Úteis**: Documente o "porquê", não o "o quê"
6. **Uma Responsabilidade**: Cada classe/método deve ter uma única responsabilidade bem definida

---

## Exercícios Práticos

Para fixar o aprendizado, tente criar:

1. Uma classe `Livro` com atributos título, autor e número de páginas
2. Uma classe `Calculadora` com métodos para operações básicas
3. Um programa que declare variáveis de diferentes tipos e imprima seus valores
4. Pratique a nomenclatura correta em todos os seus exercícios

---

## Próximos Passos

Após dominar a sintaxe básica, você estará pronto para:

- Estruturas de controle (if, else, switch)
- Laços de repetição (for, while, do-while)
- Arrays e Collections
- Orientação a Objetos avançada
- Tratamento de exceções

Continue praticando! A sintaxe se torna natural com a prática constante. 🚀
