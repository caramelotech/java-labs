---
title: "Backend Roadmap"
description: "Um guia prático e progressivo para dominar Java moderno e backend."
lastUpdated: 2026-04-15
sidebar:
  order: 99
tags: ["java", "roadmap"]
---

Um guia prático e progressivo para dominar Java moderno e backend.

## 🧱 1. Fundamentos de Programação

### Lógica de Programação

- Algoritmos e resolução de problemas
- Pseudocódigo
- Linguagens de programação

### Conceitos básicos

- Variáveis e constantes
- Tipos de dados (primitivos e não primitivos)
- Atribuição de valores
- Comentários

### Operações

- Operadores aritméticos
- Operadores relacionais e de igualdade
- Operadores lógicos
- Incremento e decremento
- Precedência de operadores

### Entrada e saída

- Leitura de dados
- Escrita de dados
- Concatenação

### Controle de fluxo

- Condicionais (`if`, `else`, `switch`)
- Estruturas de repetição (`for`, `while`, `do-while`)

### Estruturas de dados

- Arrays
- Listas
- Mapas
- Pilhas e filas (conceitual)

## 🧩 2. Programação Orientada a Objetos (POO)

### Fundamentos

- Classes e objetos
- Métodos e atributos
- Instanciação

### Pilares da POO

- Encapsulamento
- Herança
- Polimorfismo
- Abstração

### Conceitos avançados

- Interfaces
- Classes abstratas
- Sobrecarga vs sobrescrita
- Modificadores de acesso (`public`, `private`, `protected`)

## ☕ 3. Java Core (Moderno)

### Fundamentos da linguagem

- Estrutura de um projeto Java
- JVM, JDK e JRE
- Processo de compilação

### Orientação a objetos em Java

- Construtores
- `this` e `super`
- Classe `Object`

### Tipos e memória

- Autoboxing / Unboxing
- Upcasting / Downcasting

### Collections

- List, Set, Map
- `ArrayList`, `HashMap`, `HashSet`
- Comparators e Comparable

### Manipulação de Strings

- Métodos principais
- Imutabilidade

### Exceptions

- Checked vs Unchecked
- Try / Catch / Finally
- Boas práticas

### Java moderno (Java 8+ até 21+)

#### Funcional

- Lambda expressions
- Method reference
- Streams API
- Optional

#### Novidades recentes

- Records
- Text Blocks
- Pattern Matching (`instanceof`, `switch`)
- Sealed Classes
- Virtual Threads (Project Loom)

### APIs importantes

- Java Time API
- Java IO / NIO
- Concurrency API (Threads, Executors)

### Performance e JVM

- Garbage Collector
- JIT Compiler
- Memory model

## 🌿 4. Git e GitHub

### Versionamento

- Conceito de versionamento
- Repositórios e branches

### Comandos essenciais

```
git clone
git branch
git checkout
git status
git add
git commit
git push
git pull
```

### Fluxo de trabalho

- Pull Requests
- Code Review
- Merge
- Resolução de conflitos

## 🗄️ 5. Banco de Dados (SQL)

### Fundamentos

- CREATE DATABASE
- CREATE TABLE
- INSERT
- SELECT
- UPDATE
- DELETE
- TRUNCATE

### Relacionamentos

- Chaves primárias e estrangeiras

### JOINS

- INNER JOIN
- LEFT JOIN
- RIGHT JOIN
- FULL OUTER JOIN
- SELF JOIN

## 🌐 6. HTTP, APIs e REST

### Conceitos

- O que é API
- Cliente vs servidor
- REST

### HTTP

- Métodos: GET, POST, PUT, DELETE, PATCH
- Status codes:
  - 1xx → Informacional
  - 2xx → Sucesso
  - 3xx → Redirecionamento
  - 4xx → Erro do cliente
  - 5xx → Erro do servidor

### Boas práticas REST

- Recursos
- Idempotência
- Versionamento

## 🌱 7. Spring Web

### Setup

- Spring Initializr
- Maven vs Gradle

### Fundamentos

- IoC (Inversão de Controle)
- Injeção de dependência
- Beans

### Desenvolvimento

- Controllers (`@RestController`)
- Rotas (`@RequestMapping`)
- Teste com Postman / Insomnia

### Integrações

- Consumo de APIs externas
- Comunicação entre serviços

## 🗃️ 8. Spring Data

### Persistência

- JPA
- Hibernate
- DAO Pattern

### Repositórios

- `CrudRepository`
- `JpaRepository`

### Banco em memória

- H2 Database

### CRUD completo

- Criar
- Listar
- Atualizar
- Deletar

## 🔐 9. Spring Security

### Conceitos

- Autenticação vs autorização

### Segurança

- Basic Auth
- JWT
- OAuth2

### Implementação

- Login com JWT
- Refresh Token
- Password encoder (BCrypt)

## 🧪 10. Qualidade de Software

### Testes

- Testes unitários (JUnit, Mockito)
- Testes de integração
- Testes de contrato

### Boas práticas

- SOLID
- Clean Code

## ☁️ 11. Ecossistema Backend

### Arquitetura

- Arquitetura em camadas
- Arquitetura hexagonal
- Microsserviços

### Infraestrutura

- Docker
- Cloud (AWS, GCP, Azure)

### Dados e mensageria

- NoSQL (MongoDB, Redis)
- Filas (RabbitMQ, Kafka)

## 🛡️ 12. Segurança e Produção

- OWASP Top 10
- Logging e observabilidade
- Monitoramento
- Resiliência (retry, circuit breaker)

## 🎯 13. Outros tópicos

- System Design
- Performance tuning
- Escalabilidade
- Engenharia de plataforma
- IA aplicada ao desenvolvimento

## 🧠 Dica

> Foque em **projetos reais** ao invés de só teoria.

Sugestões:

- API REST completa com autenticação
- Sistema com mensageria (Kafka)
- Microsserviço com Spring Boot

---

🔥 **Regra de ouro:**
Aprenda → Aplique → Ensine → Repita
