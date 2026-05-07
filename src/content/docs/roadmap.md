---
title: "Backend Roadmap"
description: "Um guia prático e progressivo para dominar Java moderno e backend."
lastUpdated: 2026-05-07
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

#### Novidades recentes (Java 14+)

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

## ☕ 4. JDK & Distribuições

- OpenJDK
- Temurin (Adoptium)
- Azul Zulu
- GraalVM

## 🔧 5. Build & Dependências

### Ferramentas de build

- Maven
- Gradle
- Bazel

### Gerenciamento de versões

- SDKMAN!

### Comparativo

- Maven vs Gradle: quando usar cada um
- Estrutura de `pom.xml` e `build.gradle`

## 🌿 6. Git e GitHub

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

## 🗄️ 7. Banco de Dados (SQL)

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

## 🌐 8. HTTP, APIs e REST

### Conceitos

- O que é API
- Cliente vs servidor
- REST

### HTTP

- Métodos: GET, POST, PUT, DELETE, PATCH
- Status codes:
  - 1xx - Informacional
  - 2xx - Sucesso
  - 3xx - Redirecionamento
  - 4xx - Erro do cliente
  - 5xx - Erro do servidor

### Boas práticas REST

- Recursos
- Idempotência
- Versionamento

## 🌱 9. Frameworks Web

### Spring Boot

- Spring Initializr
- IoC (Inversão de Controle)
- Injeção de dependência
- Beans
- Controllers (`@RestController`)
- Rotas (`@RequestMapping`)
- Teste com Postman / Insomnia
- Consumo de APIs externas

### Alternativas modernas

- Quarkus (foco em cloud-native e GraalVM)
- Micronaut (AOT, startup rápido)
- Helidon (compatível com MicroProfile e Jakarta EE)

## 🗃️ 10. Persistência & ORM

### JPA e Hibernate

- JPA
- Hibernate
- DAO Pattern

### Spring Data

- `CrudRepository`
- `JpaRepository`
- H2 Database (banco em memória)

### CRUD completo

- Criar
- Listar
- Atualizar
- Deletar

### Outras opções

- jOOQ (SQL type-safe via DSL)
- MyBatis (mapeamento SQL explícito)

## 🔄 11. Programação Reativa

- Spring WebFlux
- Project Reactor
- RxJava
- Virtual Threads como alternativa ao modelo reativo

## 🔗 12. APIs & Integração

### Documentação e contratos

- Swagger / OpenAPI
- Spring GraphQL

### Comunicação entre serviços

- Feign Client (REST declarativo)
- gRPC Java (RPC de alta performance)
- Spring Integration

## 🔐 13. Segurança & Auth

### Conceitos

- Autenticação vs autorização

### Spring Security

- Basic Auth
- JWT
- OAuth 2 + OIDC
- Login com JWT
- Refresh Token
- Password encoder (BCrypt)

### Gerenciamento de identidade

- Keycloak

## 🧪 14. Qualidade de Software

### Testes

- Testes unitários (JUnit 5, Mockito)
- Assertions fluentes com AssertJ
- Testes de integração com Testcontainers
- Testes de contrato

### Boas práticas

- SOLID
- Clean Code

## 📨 15. Mensageria

- Apache Kafka
- RabbitMQ
- Spring Integration
- ActiveMQ Artemis

## 📊 16. Observability

- Micrometer (métricas)
- Prometheus (coleta de métricas)
- OpenTelemetry (rastreamento distribuído)
- Grafana (visualização)
- Logging estruturado

## ☁️ 17. Cloud & Containers

### Containers

- Docker
- Kubernetes
- Jib (build de imagens Docker sem Dockerfile)

### Cloud

- AWS, GCP, Azure
- Native Image (GraalVM)

## 🛡️ 18. Segurança e Produção

- OWASP Top 10
- Resiliência (retry, circuit breaker)
- Monitoramento e alertas

## 🛠️ 19. IDEs & Ferramentas

### IDEs

- IntelliJ IDEA
- Eclipse

### Qualidade de código

- SonarQube

### Migração de banco

- Flyway
- Liquibase

## 🎯 20. Outros tópicos

- System Design
- Performance tuning
- Escalabilidade
- Engenharia de plataforma
- IA aplicada ao desenvolvimento

## 🚀 Próximos Passos - Da Sintaxe à Produção

1. Domine Java 21+: Records, Virtual Threads, Pattern Matching
2. Spring Boot + JPA + Testes (JUnit 5, Mockito, Testcontainers)
3. Kafka + Security (JWT, OAuth 2 + Keycloak) + OpenAPI
4. Docker + Kubernetes + Observability (OpenTelemetry, Grafana)
5. GraalVM Native Image + Performance Tuning

## 🧠 Dica

> Foque em **projetos reais** ao invés de só teoria.

Sugestões:

- API REST completa com autenticação (Spring Boot + JWT + OpenAPI)
- Sistema com mensageria (Kafka + Spring Integration)
- Microsserviço com Spring Boot + Docker + Kubernetes
- Serviço reativo com Spring WebFlux + Project Reactor

---

🔥 **Regra de ouro:**
Aprenda - Aplique - Ensine - Repita
