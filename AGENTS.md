# AGENTS.md

Orientações especializadas para agentes de IA trabalhando com este repositório de conteúdo.

## Contexto do Projeto

Este é o repositório de **conteúdo** do Java Labs (Caramelo Tech):

- **Conteúdo:** 100% em `notes/`, Markdown puro em português brasileiro, sem frontmatter
- **Publicação:** o repositório hub [labs](https://github.com/caramelotech/labs) busca as notas daqui e publica em `https://caramelotech.com.br/labs/java/`
- **Sem build de site:** não há dependências Node - apenas Markdown e exemplos Java em `examples/`
- **Seções:** ver "Seções de conteúdo" abaixo

Veja o [README.md](README.md) para a visão geral do repositório.

## Seções de conteúdo

Cada pasta em `notes/` é uma seção do site, com um escopo próprio. Use o escopo para decidir onde uma nota nova entra. A ideia é não manter uma lista de tópicos por pasta aqui nem no `index.md` - ao adicionar conteúdo dentro de um escopo já descrito, esses arquivos não precisam mudar.

- **`fundamentos/`** - primeiros passos com Java: instalar e configurar o ambiente, entender o que é a linguagem e escrever os primeiros programas (sintaxe, tipos, operadores, controle de fluxo). Para quem nunca programou em Java.
- **`java/`** - a linguagem Java a fundo, sem framework: orientação a objetos, biblioteca padrão (Collections, exceções, IO, acesso a banco com JDBC), recursos do Java moderno (records, streams, pattern matching, virtual threads) e uma trilha avançada com os temas de código de produção e entrevista (concorrência, generics, enums, padrões de criação, memória da JVM, internos das coleções, segurança).
- **`spring/`** - construir aplicações backend com Spring Boot: expor APIs REST, persistir com Spring Data/JPA, autenticar e autorizar, validar entrada, observar, testar, empacotar e fazer deploy, organizar a arquitetura e lidar com tópicos de sistemas distribuídos.
- **`roadmap.md`** (nota solta) - trilha de estudos de backend com Java, do zero à produção. É um guia de percurso, mantido à parte e independente da cobertura atual das notas.
- **`recursos.md`** (nota solta) - materiais externos para aprofundar (livros, cursos, documentação, canais).

## Tarefas Comuns

### Adicionar uma nova nota

1. **Escolha a pasta pelo escopo:** consulte "Seções de conteúdo" e pegue a pasta cujo escopo cobre o tema. Se nenhuma cobre e há material para 2-3 notas relacionadas, avalie uma pasta nova (ver "Criar nova seção de tema").
2. **Nomeie com prefixo numérico** para controlar a ordem na barra lateral: se a pasta já tem `03-*.md`, crie `04-nome-do-topico.md`
3. **Primeira linha = título:** comece o arquivo com `# Título da Nota` - o site usa esse H1 como título da página
4. **Sem frontmatter:** escreva direto o Markdown

Exemplo de nota nova (`notes/java/04-collections.md`):

```markdown
# Collections em Java

## Introdução

Conteúdo aqui...
```

### Criar nova seção de tema

1. Crie a subpasta em `notes/nova-secao/` (nome em minúsculas) com ao menos uma nota
2. Adicione a seção em `sidebar.json`:
   ```json
   { "label": "Título da Seção", "directory": "nova-secao" }
   ```

## Regras de Conteúdo

- Use **hífens (-)**, não travessões (—)
- NÃO use `---` para separar seções (apenas para notas/atribuições no final do arquivo)
- Apenas um `# H1` por arquivo, na primeira linha
- Imagens ficam junto das notas e são referenciadas com caminho relativo em sintaxe Markdown: `![descrição](./assets/img.png)` - nunca `<img>` HTML nem caminho absoluto
- Links para outras notas do site: caminho completo `/labs/java/<secao>/<nota>/` (slug em minúsculas, igual ao nome da pasta)

## Publicação

- Push em `main` alterando `notes/` ou `sidebar.json` dispara o workflow `notify-hub.yml`, que aciona o rebuild do site no hub
- O workflow requer o secret `HUB_DISPATCH_TOKEN` configurado no repositório
- Para validar como a nota fica no site, rode no clone do hub: `npm run fetch:local && npm run build`

## Git Conventions

- **NUNCA** fazer `git commit` ou `git push` automaticamente
- Apenas executar comandos git quando explicitamente solicitado pelo usuário
- Comunicar claramente o que será commitado antes de executar

Para informações adicionais, ver o [README.md](README.md).
