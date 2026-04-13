# 01 - Hello World

Este é o programa mais básico em Java - o famoso "Olá, Mundo!"

## 📝 Sobre

Todo programador começa sua jornada com um programa Hello World. É uma tradição na computação e serve para:

- Verificar se o ambiente de desenvolvimento está configurado
- Entender a estrutura básica de um programa
- Fazer seu primeiro código funcionar!

## 📂 Arquivos

- `HelloWorld.java` - Código fonte do programa

## 🚀 Como Executar

### 1. Compile o programa

```bash
javac HelloWorld.java
```

Isso vai gerar um arquivo `HelloWorld.class` (o bytecode).

### 2. Execute o programa

```bash
java HelloWorld
```

### 3. Saída Esperada

```
Olá, Mundo!
Bem-vindo ao Java!
Este é meu primeiro programa.
```

## 🔍 Entendendo o Código

### Linha por Linha

```java
public class HelloWorld {
```

- `public` - modificador de acesso (classe pode ser acessada de qualquer lugar)
- `class` - palavra-chave para definir uma classe
- `HelloWorld` - nome da classe (deve ser igual ao nome do arquivo)

```java
public static void main(String[] args) {
```

- `public` - método pode ser chamado de qualquer lugar
- `static` - método pertence à classe, não a uma instância
- `void` - método não retorna nenhum valor
- `main` - nome do método (ponto de entrada do programa)
- `String[] args` - parâmetro que recebe argumentos da linha de comando

```java
System.out.println("Olá, Mundo!");
```

- `System` - classe do sistema Java
- `out` - objeto de saída padrão (console)
- `println` - método que imprime e pula linha
- `"Olá, Mundo!"` - string (texto) a ser impressa

## 💡 Conceitos Importantes

### 1. Nome do Arquivo = Nome da Classe

O arquivo **deve** se chamar `HelloWorld.java` porque a classe se chama `HelloWorld`.

### 2. Ponto e Vírgula

Toda instrução em Java termina com `;`

### 3. Chaves { }

Delimitam blocos de código (classes, métodos, etc.)

### 4. Case Sensitive

Java diferencia maiúsculas de minúsculas:

- `HelloWorld` ≠ `helloworld`
- `System` ≠ `system`

## 🎯 Experimente

Tente modificar o programa:

1. **Mude a mensagem:**

   ```java
   System.out.println("Seu nome aqui!");
   ```

2. **Adicione mais linhas:**

   ```java
   System.out.println("Linha 1");
   System.out.println("Linha 2");
   System.out.println("Linha 3");
   ```

3. **Use `print` em vez de `println`:**
   ```java
   System.out.print("Não pula linha ");
   System.out.print("fica na mesma linha");
   ```

## 🔗 Conceitos Relacionados

- [📖 Introdução ao Java](../../docs/01-introducao-java.md)
- [📖 Estrutura de um Programa Java](../../docs/03-primeiro-programa.md)

## ❓ Problemas Comuns

### Erro: "javac não é reconhecido"

**Solução:** JDK não está instalado ou não está no PATH.
Veja: [Instalação e Configuração](../../docs/02-instalacao-configuracao.md)

### Erro: "Could not find or load main class HelloWorld"

**Soluções:**

1. Certifique-se que compilou: `javac HelloWorld.java`
2. Execute no diretório correto (onde está o .class)
3. Use `java HelloWorld` (sem .class no final)

### Erro de compilação

**Verifique:**

- Nome do arquivo = nome da classe
- Sintaxe correta (chaves, ponto e vírgula)
- Case das palavras

**[⬅️ Voltar ao Índice de Códigos](../README.md)**
