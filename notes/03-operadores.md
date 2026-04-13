# Operadores em Java e Boas Práticas de Código

Neste capítulo você aprenderá:

- Operadores aritméticos e de atribuição
- Operadores relacionais
- Operadores unários
- Operadores ternários e lógicos
- Comentários
- JavaDoc
- JavaBeans

Esses conceitos são fundamentais para escrever código Java de forma correta e profissional.

# Operadores de Atribuição e Aritméticos

## Operador de Atribuição

O operador de atribuição é usado para armazenar um valor em uma variável.

```java
int idade = 25;
```

O símbolo `=` significa:

> Receba o valor da direita e armazene na variável da esquerda.

## Operadores Aritméticos

São usados para realizar cálculos matemáticos.

| Operador | Significado               | Exemplo |
| -------- | ------------------------- | ------- |
| `+`      | Soma                      | a + b   |
| `-`      | Subtração                 | a - b   |
| `*`      | Multiplicação             | a \* b  |
| `/`      | Divisão                   | a / b   |
| `%`      | Resto da divisão (módulo) | a % b   |

### Exemplo

```java
int a = 10;
int b = 3;

int soma = a + b;       // 13
int sub = a - b;        // 7
int mult = a * b;       // 30
int div = a / b;        // 3
int resto = a % b;      // 1
```

## Operadores de Atribuição Combinados

Java permite simplificar operações com atribuição.

| Operador | Equivalente |
| -------- | ----------- |
| `+=`     | a = a + b   |
| `-=`     | a = a - b   |
| `*=`     | a = a \* b  |
| `/=`     | a = a / b   |
| `%=`     | a = a % b   |

### Exemplo

```java
int x = 10;

x += 5;  // x = 15
x -= 3;  // x = 12
x *= 2;  // x = 24
```

# Operadores Relacionais

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

# Operadores Unários

Operam sobre apenas **um operando**.

## Principais Operadores Unários

| Operador | Função         |
| -------- | -------------- |
| `+`      | Valor positivo |
| `-`      | Negativo       |
| `++`     | Incremento     |
| `--`     | Decremento     |
| `!`      | Negação lógica |

## Incremento e Decremento

### Pré-incremento

Incrementa antes de usar.

```java
int x = 5;
int y = ++x; // x = 6, y = 6
```

### Pós-incremento

Incrementa depois de usar.

```java
int x = 5;
int y = x++; // x = 6, y = 5
```

## Negação Lógica

```java
boolean ativo = true;

System.out.println(!ativo); // false
```

# Operadores Lógicos

Usados para combinar expressões booleanas.

| Operador | Nome | Significado |     |           |
| -------- | ---- | ----------- | --- | --------- |
| `&&`     | AND  | E lógico    |     |           |
| `        |      | `           | OR  | OU lógico |
| `!`      | NOT  | Negação     |     |           |

## Exemplo

```java
int idade = 20;
boolean temCarteira = true;

if (idade >= 18 && temCarteira) {
    System.out.println("Pode dirigir");
}
```

# Operador Ternário

O operador ternário é uma forma reduzida de escrever um `if-else`.

## Sintaxe

```java
condicao ? valorSeVerdadeiro : valorSeFalso;
```

## Exemplo

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

# Comentários em Java

Comentários servem para:

- Explicar código
- Documentar decisões
- Melhorar manutenção
- Ajudar outros desenvolvedores

Java possui três tipos de comentários.

## Comentário de Linha

```java
// Este é um comentário de linha
int idade = 25;
```

## Comentário de Bloco

```java
/*
Este é um comentário
de múltiplas linhas
*/
int idade = 25;
```

## Comentário de Documentação (JavaDoc)

```java
/**
 * Este é um comentário JavaDoc
 */
```

Esse tipo gera documentação automática.

# JavaDoc

JavaDoc é uma ferramenta oficial do Java para gerar documentação a partir do código.

Muito usado em projetos profissionais e bibliotecas.

## Exemplo de JavaDoc em Método

```java
/**
 * Calcula a soma de dois números.
 *
 * @param a Primeiro número
 * @param b Segundo número
 * @return Resultado da soma
 */
public int somar(int a, int b) {
    return a + b;
}
```

## Principais Tags JavaDoc

| Tag        | Função       |
| ---------- | ------------ |
| `@author`  | Autor        |
| `@version` | Versão       |
| `@param`   | Parâmetro    |
| `@return`  | Retorno      |
| `@throws`  | Exceções     |
| `@see`     | Referência   |
| `@since`   | Desde versão |

## Gerar Documentação

Comando:

```bash
javadoc NomeArquivo.java
```

# JavaBeans

JavaBeans é um padrão de classe Java usado principalmente para:

- Representar entidades
- Transferir dados
- Modelar objetos de domínio
- Frameworks (Spring, Hibernate, etc.)

## Características de um JavaBean

Um JavaBean deve:

1. Possuir construtor vazio
2. Ter atributos privados (`private`)
3. Possuir métodos getters e setters públicos
4. Ser serializável (opcional, mas comum)

## Exemplo de JavaBean

```java
import java.io.Serializable;

public class Pessoa implements Serializable {

    private String nome;
    private int idade;

    public Pessoa() {
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public int getIdade() {
        return idade;
    }

    public void setIdade(int idade) {
        this.idade = idade;
    }
}
```

## Por que JavaBeans são importantes?

Porque muitos frameworks Java dependem desse padrão:

- Spring Boot
- Hibernate / JPA
- JSF
- Bibliotecas de serialização
- APIs REST

# Boas Práticas Importantes

✅ Use nomes claros para variáveis
✅ Evite comentários óbvios
✅ Prefira JavaDoc em APIs públicas
✅ Use operadores com clareza
✅ Evite ternário complexo

# Resumo

Neste capítulo você aprendeu:

- Operadores aritméticos e de atribuição
- Operadores relacionais
- Operadores unários
- Operadores lógicos
- Operador ternário
- Comentários em Java
- JavaDoc
- JavaBeans

Esses conceitos são fundamentais para escrever programas Java profissionais.
