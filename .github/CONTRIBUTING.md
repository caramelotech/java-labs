# Guia de Contribuicao

Obrigado por querer contribuir com o Java Labs.

## O que pode ser contribuido

- Melhorias e correcoes nas notas em `src/content/docs/`
- Novos exemplos de codigo em `examples/`
- Exercicios adicionais em `examples/exercises.md`
- Projetos praticos em `examples/projects.md`
- Recursos recomendados em `src/content/docs/recursos.md`
- Melhorias no site Astro + Starlight

## Processo

1. Crie uma branch a partir de `main` seguindo o padrao:

   ```text
   feature/descricao-curta
   fix/descricao-curta
   docs/descricao-curta
   ```

2. Faca commits atomicos com mensagens no padrao Conventional Commits:

   ```text
   feat: adicionar exercicios sobre loops
   fix: corrigir exemplo de recursao
   docs: melhorar conteudo sobre heranca
   ```

   Tipos validos: `feat`, `fix`, `docs`, `style`, `refactor`, `chore`

3. Abra um Pull Request usando o template do repositorio.

4. Apos aprovacao, o merge sera feito por uma pessoa mantenedora.

## Rodando o site localmente

O Java Labs usa Astro + Starlight para publicar a documentacao.

```bash
npm install
npm run dev
```

O site fica disponivel em `http://localhost:4321`.

Para validar antes de abrir o PR:

```bash
npm run build
npm run preview
```

## Padroes de conteudo

### Notas em `src/content/docs/`

- Escreva em portugues (pt-BR)
- Use titulos hierarquicos (`##`, `###`)
- Prefira exemplos curtos e objetivos
- Explique o "por que" alem do "como"
- Nomeie arquivos com prefixo numerico quando fizer parte da trilha, por exemplo `04-variaveis.md`
- Use frontmatter Starlight completo

Frontmatter recomendado:

```yaml
---
title: "Titulo da nota"
description: "Resumo curto explicando o foco da pagina."
lastUpdated: 2026-01-01
sidebar:
  order: 1
tags: ["java", "tema", "iniciante"]
---
```

`sidebar.order` e sequencial por diretorio, nao global. A ordem entre secoes e definida pelo array `sidebar` em `astro.config.mjs`. Dentro de cada pasta, numere a partir de 1.

Se a nota ficar melhor agrupada por assunto, voce pode criar subpastas dentro de `src/content/docs/`. Para que a nova pasta apareça na sidebar, adicione uma entrada `autogenerate` em `astro.config.mjs` (veja instrucoes em `CLAUDE.md`).

### Exemplos em `examples/`

- Um conceito por pasta, nomeada com prefixo sequencial quando fizer sentido, por exemplo `02-Variaveis/`
- Inclua um comentario no topo do arquivo explicando o objetivo do exemplo
- Adicione um `README.md` na pasta com contexto e instrucoes de execucao
- Teste o codigo antes de submeter com `javac Arquivo.java` e `java NomeDaClasse`

### Exercicios em `examples/exercises.md`

- Descreva claramente o objetivo
- Indique o nivel de dificuldade quando necessario
- Inclua exemplos de saida esperada
- Use criterios de sucesso em formato de checklist
- Aponte para notas relacionadas em `src/content/docs/` quando isso ajudar

### Projetos em `examples/projects.md`

- Descreva o objetivo e o contexto
- Liste requisitos em formato de checklist
- Inclua ao menos um exemplo de uso ou saida esperada
- Adicione dicas para quem esta comecando

## Duvidas

Abra uma issue com a tag `question`.
