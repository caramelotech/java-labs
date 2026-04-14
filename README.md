# Java Labs

> Base de conhecimento completa sobre Java para iniciantes em portugues.

Bem-vindo ao **Java Labs**. Este repositorio foi organizado para ensinar Java de forma clara, pratica e progressiva, combinando notas publicadas com Astro + Starlight, exemplos executaveis e desafios para consolidar o aprendizado.

[![GitHub Pages](https://img.shields.io/badge/GitHub-Pages-blue)](https://caramelotech.github.io/java-labs)
[![License](https://img.shields.io/github/license/caramelotech/java-labs)](LICENSE)

## Sobre o projeto

O **Java Labs** e uma iniciativa educacional com foco em:

- Conteudo didatico em portugues (pt-BR)
- Explicacoes claras e acessiveis
- Exemplos praticos e funcionais
- Exercicios para fixacao
- Projetos para aplicar os conceitos em contexto real
- Recursos adicionais curados

## Estrutura do repositorio

```text
java-labs/
├── src/content/docs/   -> Anotacoes e estudos publicados no site
├── examples/           -> Exemplos de codigo, exercicios e projetos praticos
├── src/styles/         -> Estilos customizados do tema Starlight
└── .github/            -> Workflows, templates e guias de contribuicao
```

### Navegacao rapida

| Secao        | Descricao                        | Link                                                                         |
| ------------ | -------------------------------- | ---------------------------------------------------------------------------- |
| Site         | Documentacao publicada           | [caramelotech.github.io/java-labs](https://caramelotech.github.io/java-labs) |
| Notas        | Conteudos teoricos em Markdown   | [src/content/docs/](src/content/docs/)                                       |
| Exemplos     | Codigos organizados por topico   | [examples/README.md](examples/README.md)                                     |
| Exercicios   | Desafios praticos de estudo      | [examples/exercises.md](examples/exercises.md)                               |
| Projetos     | Propostas de aplicacao integrada | [examples/projects.md](examples/projects.md)                                 |
| Contribuicao | Guia para contribuir             | [.github/CONTRIBUTING.md](.github/CONTRIBUTING.md)                           |

## Como usar

Se voce esta comecando agora, este fluxo funciona bem:

1. Leia [Introducao ao Java](src/content/docs/01-introducao-java.md)
2. Continue pelas notas em `src/content/docs/` seguindo a ordem numerica
3. Execute o exemplo [Hello World](examples/01-HelloWorld/)
4. Resolva os desafios em [examples/exercises.md](examples/exercises.md)
5. Escolha um projeto em [examples/projects.md](examples/projects.md)
6. Consulte [recursos adicionais](src/content/docs/recursos.md) para aprofundar

## Conteudo atual

### Notas publicadas

- [01 - Introducao ao Java](src/content/docs/01-introducao-java.md)
- [02 - Sintaxe Java](src/content/docs/02-sintaxe.md)
- [03 - Operadores em Java](src/content/docs/03-operadores.md)
- [11 - Programacao Orientada a Objetos](src/content/docs/11-orientacao-a-objeto.md)
- [Recursos adicionais](src/content/docs/recursos.md)

### Exemplos de codigo

- [01-HelloWorld](examples/01-HelloWorld/)

### Pratica guiada

- [Exercicios](examples/exercises.md)
- [Projetos](examples/projects.md)

## Rodando localmente

Instale as dependencias e inicie o site Astro:

```bash
npm install
npm run dev
```

O servidor local roda em `http://localhost:4321`.

Outros comandos uteis:

```bash
npm run build
npm run preview
```

Versao publicada:

`https://caramelotech.github.io/java-labs`

## Executando os exemplos Java

Para compilar e executar um exemplo localmente:

```bash
cd examples/01-HelloWorld
javac HelloWorld.java
java HelloWorld
```

Recomendacao de ambiente:

- JDK 17 LTS ou superior
- `java --version`
- `javac --version`

## Adicionando notas

Novas anotacoes devem ser criadas em `src/content/docs/`, podendo usar subpastas quando fizer sentido. Use nomes descritivos com prefixo numerico para manter a ordem no menu lateral.

Exemplo de frontmatter padrao Starlight:

```md
---
title: "Titulo da nota"
description: "Resumo curto explicando o foco da pagina."
lastUpdated: 2026-01-01
sidebar:
  order: 4
tags: ["java", "tema", "iniciante"]
---
```

Boas praticas:

- Escreva em portugues (pt-BR)
- Comece pelo conceito e depois mostre exemplos
- Mantenha descricoes curtas e objetivas no frontmatter
- Atualize links internos quando criar novas paginas ou subpastas

## Objetivos de aprendizado

Ao seguir a trilha do Java Labs, voce deve conseguir:

- Compreender os fundamentos da linguagem Java
- Escrever programas orientados a objetos
- Utilizar estruturas de controle e tipos de dados basicos
- Resolver exercicios progressivos com autonomia
- Criar pequenas aplicacoes Java e evoluir para frameworks mais avancados

## Como contribuir

Contribuicoes sao muito bem-vindas. Voce pode ajudar com:

- Melhorias nas notas de `src/content/docs/`
- Novos exemplos em `examples/`
- Novos exercicios em `examples/exercises.md`
- Novos projetos em `examples/projects.md`
- Curadoria de recursos em `src/content/docs/recursos.md`

Antes de abrir um PR, leia o [Guia de Contribuicao](.github/CONTRIBUTING.md).

## Licenca

Este projeto esta sob a licenca definida em [LICENSE](LICENSE).
