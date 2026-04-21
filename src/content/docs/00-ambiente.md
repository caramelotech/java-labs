---
title: "Configurando o Ambiente"
description: "Instale o JDK, configure variáveis de ambiente e escolha uma IDE para começar a programar em Java"
lastUpdated: 2026-04-21
sidebar:
  order: 0
tags: ["java", "jdk", "ambiente", "sdkman", "iniciante"]
---

Antes de escrever qualquer código Java, você precisa de três coisas:

1. **JDK** — o kit de desenvolvimento que inclui o compilador e a JVM
2. **Variáveis de ambiente** — para que o terminal encontre o Java
3. **IDE** — um editor com suporte a Java para escrever seu código

Este guia cobre os dois caminhos mais comuns: Mac/Linux (usando SDKMAN) e Windows (instalação manual).

---

## Instalando o JDK no Mac ou Linux (SDKMAN)

O [SDKMAN](https://sdkman.io) é um gerenciador de versões para ferramentas Java. Com ele você instala e troca versões do JDK com um comando, sem precisar configurar variáveis de ambiente manualmente.

### 1. Instalar o SDKMAN

Abra o terminal e execute:

```bash
curl -s "https://get.sdkman.io" | bash
```

Após a instalação, feche e reabra o terminal, ou execute:

```bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

### 2. Instalar o JDK

Instale a versão LTS mais recente (recomendado para iniciantes):

```bash
sdk install java
```

Para ver todas as versões disponíveis:

```bash
sdk list java
```

Para instalar uma versão específica (ex: Java 21 da Temurin):

```bash
sdk install java 21.0.3-tem
```

### 3. Verificar a instalação

```bash
java --version
javac --version
```

A saída deve ser algo como:

```
openjdk 21.0.3 2024-04-16
javac 21.0.3
```

Se aparecer um número de versão, está funcionando.

> **SDKMAN não funciona no Windows nativo.** Se você usa Windows, siga a seção abaixo. Se usa WSL (Windows Subsystem for Linux), o SDKMAN funciona normalmente dentro do WSL.

---

## Instalando o JDK no Windows (manual)

### 1. Baixar o instalador

Acesse o site da **Eclipse Temurin** (distribuição gratuita e recomendada do OpenJDK):

```
https://adoptium.net
```

- Escolha a versão **LTS** mais recente (ex: Java 21)
- Baixe o instalador `.msi` para Windows x64
- Execute o instalador e siga os passos

> Durante a instalação, marque a opção **"Set JAVA_HOME variable"** se disponível — isso configura a variável automaticamente.

### 2. Configurar JAVA_HOME manualmente (se necessário)

Se a opção não estava disponível no instalador, configure manualmente:

1. Clique com o botão direito em **Este Computador** → **Propriedades**
2. Vá em **Configurações avançadas do sistema** → **Variáveis de Ambiente**
3. Em **Variáveis do Sistema**, clique em **Novo** e preencha:
   - Nome: `JAVA_HOME`
   - Valor: caminho onde o JDK foi instalado, ex: `C:\Program Files\Eclipse Adoptium\jdk-21`
4. Encontre a variável `Path`, clique em **Editar** e adicione:
   - `%JAVA_HOME%\bin`

### 3. Verificar a instalação

Abra o **Prompt de Comando** (cmd) ou **PowerShell** e execute:

```cmd
java --version
javac --version
```

Se aparecer a versão, está configurado corretamente.

---

## Verificando que tudo funciona

Independente do sistema operacional, faça este teste rápido:

Crie um arquivo chamado `Teste.java` com o seguinte conteúdo:

```java
public class Teste {
    public static void main(String[] args) {
        System.out.println("Ambiente configurado com sucesso!");
    }
}
```

Compile e execute:

```bash
javac Teste.java
java Teste
```

Saída esperada:

```
Ambiente configurado com sucesso!
```

Se isso funcionar, seu ambiente está pronto.

---

## Escolhendo uma IDE

Uma IDE (Integrated Development Environment) é um editor de código com recursos como autocomplete, destaque de erros e execução integrada. Para Java, as opções mais usadas são:

### IntelliJ IDEA Community (recomendado)

- Gratuito e open source
- Melhor suporte a Java do mercado
- Download: `https://www.jetbrains.com/idea/download` (escolha **Community Edition**)

### VS Code com extensão Java

- Gratuito e leve
- Requer a extensão **Extension Pack for Java** da Microsoft
- Boa opção se você já usa VS Code para outras linguagens
- Download: `https://code.visualstudio.com`

### Qual escolher?

Se você está começando do zero em Java, o **IntelliJ IDEA Community** oferece a melhor experiência — ele entende Java profundamente e sugere erros antes mesmo de compilar. O VS Code é uma boa alternativa se você já o usa e prefere um ambiente mais leve.

---

## Verifique seu Entendimento

Antes de avançar, confirme:

- [ ] `java --version` retorna uma versão no terminal
- [ ] `javac --version` retorna uma versão no terminal
- [ ] Você conseguiu compilar e executar o arquivo `Teste.java`
- [ ] Sua IDE está instalada e aberta

Se algum item falhou, revise a seção correspondente acima antes de continuar.

## Próximos Passos

Com o ambiente configurado, você está pronto para começar a aprender Java de verdade. Continue para [Introdução ao Java](/java-labs/01-introducao-java/) e escreva seu primeiro programa.
