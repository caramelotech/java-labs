# 01 - Introdução à Linguagem Java

> **Última atualização:** 01/03/2026  
> **Nível:** Iniciante  
> **Tempo de leitura:** 15 minutos

---

## Introdução

Java é uma das linguagens de programação mais populares e amplamente utilizadas no mundo. Criada com o objetivo de ser **portável, segura e robusta**, Java permite que você escreva um programa uma vez e execute em praticamente qualquer plataforma, desde computadores desktop até dispositivos móveis e servidores corporativos.

Neste primeiro tópico, você vai entender o que é Java, suas principais características, como funciona o processo de desenvolvimento e os conceitos fundamentais que tornam esta linguagem tão poderosa e versátil.

Se você está começando sua jornada em programação, Java é uma excelente escolha por sua sintaxe clara, vasta documentação e enorme comunidade de desenvolvedores.

---

## O que é Java?

Java é uma **linguagem de programação de alto nível, orientada a objetos**, criada na década de 1990 por uma equipe liderada por **James Gosling** na empresa Sun Microsystems (posteriormente adquirida pela Oracle).

Inicialmente, Java foi desenvolvida para dispositivos eletrônicos embarcados, mas rapidamente ganhou popularidade com o crescimento da internet, tornando-se uma das linguagens mais utilizadas para:

- Sistemas corporativos
- Aplicações web
- Desenvolvimento mobile (Android)
- Sistemas distribuídos
- Backend de aplicações

### O Lema WORA

Um dos lemas mais conhecidos da linguagem é:

> **WORA — Write Once, Run Anywhere**
> 
> (Escreva uma vez, execute em qualquer lugar)

Isso significa que um programa Java pode ser executado em qualquer sistema operacional que possua uma **JVM (Java Virtual Machine)** instalada, sem necessidade de modificações no código.

---

## Principais Características do Java

### 1. Independente de Plataforma

Java não é compilado diretamente para código de máquina específico de um sistema operacional.

**Como funciona:**

1. O código Java é compilado para **bytecode**
2. O bytecode é executado pela **JVM**

Isso permite que o mesmo programa funcione em:

- ✅ Windows
- ✅ Linux
- ✅ macOS
- ✅ Servidores
- ✅ Dispositivos embarcados

#### Analogia

Imagine que você escreve uma carta em um idioma universal (bytecode). Cada país (sistema operacional) tem um tradutor especializado (JVM) que consegue ler e executar sua carta. Você escreve apenas uma vez, mas todos conseguem entender!

---

### 2. Orientado a Objetos

Java foi projetado seguindo os conceitos da **Programação Orientada a Objetos (POO)**.

Isso significa que programas são organizados em:

- **Classes:** Modelos ou estruturas
- **Objetos:** Instâncias das classes
- **Métodos:** Ações que os objetos podem realizar
- **Atributos:** Características dos objetos

**Benefícios da POO:**

- 📦 Reutilização de código
- 🔧 Facilidade de manutenção
- 📈 Escalabilidade
- 🗂️ Organização lógica

---

### 3. Garbage Collector (Coletor de Lixo)

Java possui **gerenciamento automático de memória**.

O **Garbage Collector (GC)** é responsável por:

- Liberar memória de objetos que não estão mais sendo utilizados
- Evitar vazamentos de memória (memory leaks)
- Reduzir erros comuns como ponteiros inválidos

**Vantagem:** O programador não precisa desalocar memória manualmente, diferente de linguagens como C ou C++.

---

### 4. Multithreading

Java possui **suporte nativo para execução concorrente**.

Isso significa que um programa pode executar múltiplas tarefas ao mesmo tempo, por exemplo:

- Processar dados enquanto baixa arquivos
- Executar várias requisições simultaneamente
- Criar aplicações responsivas

---

### 5. Tratamento de Exceções

Java possui um sistema robusto de tratamento de erros chamado **Exceptions**.

**Permite:**

- Detectar erros em tempo de execução
- Controlar falhas de forma segura
- Evitar que o sistema pare inesperadamente

**Exemplos de situações tratadas:**

- Arquivo não encontrado
- Divisão por zero
- Problemas de rede

---

## Processo de Desenvolvimento em Java

O fluxo de desenvolvimento Java segue etapas bem definidas:

### 1️⃣ Criação do Código Fonte

Todo código Java é escrito em arquivos de texto com extensão `.java`

**Exemplo:**
```
MeuPrograma.java
```

### 2️⃣ Compilação

O compilador Java (`javac`) transforma o código fonte em **bytecode**.

**Comando:**
```bash
javac MeuPrograma.java
```

**Isso gera:**
```
MeuPrograma.class
```

### 3️⃣ Execução

O arquivo `.class` é executado pela JVM:

```bash
java MeuPrograma
```

**Importante:** O arquivo `.class` **não contém código de máquina nativo**, mas sim **bytecode**, que é interpretado ou compilado dinamicamente pela JVM.

---

## Programação Orientada a Objetos (POO) em Java

Para desenvolver software de forma consistente em Java, é essencial compreender os pilares da POO.

### Classe e Objeto

- **Classe** → Modelo ou estrutura (planta de uma casa)
- **Objeto** → Instância da classe (casa construída)

**Exemplo conceitual:**

```
Classe: Carro
Objeto: MeuCarro (um carro específico, como um "Fusca azul, 1970")
```

---

### Encapsulamento

Consiste em **proteger os dados internos** de uma classe e permitir acesso controlado.

**Normalmente feito com:**

- Atributos privados (`private`)
- Métodos públicos (`get` e `set`)

**Benefícios:**

- 🔒 Segurança
- 📋 Organização
- 🎯 Controle de acesso

---

### Abstração

Abstração significa representar apenas as **características essenciais** de um objeto, escondendo detalhes desnecessários.

**Exemplo:**

Um usuário dirige um carro sem precisar saber como o motor funciona internamente. Você apenas usa o volante, acelerador e freio (interface pública).

---

### Herança

Permite que uma classe **herde características** de outra.

**Exemplo:**

```
Animal (classe pai)
   ↓
Cachorro (classe filha - herda características de Animal)
```

**Benefícios:**

- ♻️ Reutilização de código
- 📊 Hierarquia lógica
- ⚡ Redução de duplicação

---

### Polimorfismo

Significa "**muitas formas**".

Permite que um mesmo método tenha **comportamentos diferentes** dependendo do contexto.

**Exemplo:**

```
Animal.fazerSom()

Cachorro → Latir ("Au au!")
Gato → Miar ("Miau!")
```

---

## JVM — Java Virtual Machine

A **JVM** é o coração do Java.

**Ela é responsável por:**

- ⚙️ Executar bytecode
- 💾 Gerenciar memória
- 🗑️ Executar Garbage Collector
- 🔄 Controlar threads
- 🌍 Garantir portabilidade

**Importante:** Cada sistema operacional possui sua própria implementação de JVM, mas todas executam o mesmo bytecode Java.

---

## JDK — Java Development Kit

O **JDK** é o **kit completo para desenvolvimento Java**.

**Ele contém:**

- Compilador (`javac`)
- JVM
- Bibliotecas padrão
- Ferramentas de desenvolvimento (javadoc, debugger, etc.)

**Se você quer programar em Java, você precisa do JDK.**

---

## JRE — Java Runtime Environment

O **JRE** é o **ambiente de execução**.

**Contém:**

- JVM
- Bibliotecas necessárias para rodar programas

**Não contém ferramentas de desenvolvimento.**

**Nota:** Nas versões modernas do Java, o JRE está embutido no JDK.

---

## Plataformas Java

### Java SE (Standard Edition)

**Base da linguagem Java.**

**Inclui:**

- Sintaxe da linguagem
- Bibliotecas fundamentais
- APIs principais

**Usado para:**

- Aplicações desktop
- Sistemas backend
- Fundamentos do Java

---

### Java EE (Enterprise Edition) — Atualmente Jakarta EE

**Voltado para sistemas corporativos.**

**Inclui:**

- Servlets
- APIs Web
- Persistência
- Segurança
- Microsserviços

**Muito usado em empresas e sistemas de grande porte.**

---

### Java ME (Micro Edition)

**Versão para dispositivos com recursos limitados:**

- Dispositivos embarcados
- Sistemas antigos de celulares
- IoT (Internet of Things)

**Hoje menos comum.**

---

### JavaFX

**Framework para criação de interfaces gráficas modernas em Java.**

**Permite:**

- Interfaces desktop
- Animações
- Componentes visuais avançados

---

## Resumo Visual do Ecossistema

```
JDK (Java Development Kit)
 ├── JRE (Java Runtime Environment)
 │    ├── JVM (Java Virtual Machine)
 │    └── Bibliotecas de Classe
 └── Ferramentas de Desenvolvimento
      ├── javac (compilador)
      ├── javadoc (documentação)
      └── debugger, etc.
```

---

## Outras Características Importantes

### Multiplataforma
Executa em diversos sistemas operacionais sem alterações.

### Portável (WORA)
Write Once, Run Anywhere - Escreva uma vez, rode em qualquer lugar.

### Robusta
Java foi projetada para reduzir erros comuns:
- Verificação de tipos em tempo de compilação
- Gerenciamento automático de memória
- Tratamento de exceções
- Verificação de acesso

### Segura
Possui mecanismos de segurança como:
- Bytecode verification
- ClassLoader
- Security Manager (em contextos específicos)
- Sem ponteiros diretos como em C/C++

### Interpretada + Compilada
Java é considerada híbrida:
- Compilada para bytecode
- Executada pela JVM (interpretação ou JIT compilation)

---

## Resumo

Neste tópico, você aprendeu:

- ✅ Java é uma linguagem portável, robusta e orientada a objetos
- ✅ O lema WORA (Write Once, Run Anywhere) é possível graças à JVM
- ✅ Java é compilada para bytecode e executada pela JVM
- ✅ Os pilares da POO são: classes, objetos, encapsulamento, herança, polimorfismo e abstração
- ✅ JDK é para desenvolvimento, JRE é para execução
- ✅ Java possui gerenciamento automático de memória (Garbage Collector)
- ✅ Existem diferentes plataformas Java (SE, EE, ME, JavaFX)

---

## Próximos Passos

Agora que você entende o que é Java e suas características principais, está pronto para:

1. **[Instalar o JDK e configurar seu ambiente de desenvolvimento](02-instalacao-configuracao.md)**
2. **Escrever seu primeiro programa "Olá, Mundo!"**
3. **Aprender sobre variáveis e tipos de dados**

---

## Recursos Adicionais

### Para Aprender Mais

- 📚 **Livros:**
  - "Java: Como Programar" - Deitel & Deitel
  - "Use a Cabeça! Java" - Kathy Sierra & Bert Bates
  
- 🎓 **Cursos:**
  - [Java Programming - Coursera](https://www.coursera.org/learn/java-programming)
  - [Curso de Java - Curso em Vídeo](https://www.cursoemvideo.com/curso/java/)
  
- 📰 **Documentação:**
  - [Documentação Oficial do Java](https://docs.oracle.com/javase/)
  - [Java Tutorial da Oracle](https://docs.oracle.com/javase/tutorial/)

---

## Perguntas Frequentes

<details>
<summary><strong>Preciso pagar para usar Java?</strong></summary>

Não! Java é gratuito para desenvolvimento e uso. Você pode baixar o JDK gratuitamente e criar aplicações sem custos.

</details>

<details>
<summary><strong>Java e JavaScript são a mesma coisa?</strong></summary>

Não! Apesar do nome similar, são linguagens completamente diferentes. Java é uma linguagem de programação completa, enquanto JavaScript foi criada principalmente para navegadores web (embora hoje também rode em servidores).

</details>

<details>
<summary><strong>Java ainda é relevante em 2026?</strong></summary>

Sim! Java continua sendo uma das linguagens mais utilizadas no mundo, especialmente em sistemas corporativos, Android, sistemas bancários e aplicações de grande escala.

</details>

<details>
<summary><strong>É difícil aprender Java?</strong></summary>

Java tem uma curva de aprendizado moderada. Com os conceitos básicos de lógica de programação e POO, você consegue criar aplicações funcionais. Este repositório foi criado justamente para facilitar seu aprendizado!

</details>

---

**[⬅️ Voltar ao Índice](../README.md)** | **[➡️ Próximo: Instalação e Configuração](02-instalacao-configuracao.md)**
