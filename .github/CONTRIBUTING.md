# Guia de Contribuição

Obrigado por querer contribuir com o Java Labs!

## O que pode ser contribuido

- Melhorias e correções nos conteúdos (`/docs`)
- Novos exemplos de código em `/codigos`
- Exercícios adicionais em `/exercicios`
- Projetos práticos em `/projetos`
- Recursos recomendados em `/recursos`

## Processo

1. Crie uma branch a partir de `main` seguindo o padrão:
   ```
   feature/descricao-curta
   fix/descricao-curta
   docs/descricao-curta
   ```

2. Faça commits atômicos com mensagens no padrão de Conventional Commits:
   ```
   feat: adicionar exercícios sobre loops
   fix: corrigir exemplo de recursão
   docs: melhorar conteúdo sobre herança
   ```

   Tipos válidos: `feat`, `fix`, `docs`, `style`, `refactor`, `chore`

3. Abra um Pull Request usando o template disponível e aguarde revisão.

4. Após aprovação, o merge será feito por um mantenedor.

## Padrões de conteúdo

### Documentação (Markdown)

- Escreva em português (pt-BR)
- Use títulos hierárquicos (`##`, `###`)
- Prefira exemplos curtos e diretos
- Inclua o "por quê", não apenas o "como"
- Nomeie os arquivos com prefixo numérico sequencial: `03-nome-do-topico.md`
- Atualize o índice em `docs/README.md` ao adicionar um novo tópico

### Exemplos de código (`/codigos`)

- Um conceito por pasta, nomeada com prefixo sequencial: `02-NomeDoExemplo/`
- Inclua um comentário no topo do arquivo explicando o que o exemplo demonstra
- Adicione um `README.md` na pasta com instruções de execução
- Teste o código antes de submeter: `javac Arquivo.java && java Arquivo`

### Exercícios (`/exercicios`)

- Descreva claramente o objetivo
- Indique o nível de dificuldade (iniciante / intermediário / avançado)
- Inclua exemplos de saída esperada
- Use critérios de sucesso em formato de checklist

### Projetos (`/projetos`)

- Descreva o objetivo e o contexto
- Liste os requisitos em formato de checklist
- Inclua ao menos um exemplo de uso ou saída esperada
- Adicione dicas para quem está começando

## Dúvidas?

Abra uma issue com a tag `question`.
