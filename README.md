# Java Labs

Bem-vindo ao **Java Labs**. Este repositório foi organizado para ensinar Java de forma clara, prática e progressiva, combinando notas em Markdown, exemplos executáveis e desafios para consolidar o aprendizado.

As notas deste repositório são publicadas no site do [Caramelo Labs](https://caramelotech.com.br/labs/java/).

## O que você vai encontrar

- Conteúdo didático em português (pt-BR)
- Explicações claras e acessíveis
- Exemplos práticos e funcionais
- Exercícios para fixação
- Projetos para aplicar os conceitos em contexto real
- Recursos adicionais curados

## Conteúdo

| Seção       | Escopo                                                                                         |
| ----------- | ---------------------------------------------------------------------------------------------- |
| Fundamentos | Primeiros passos com Java: ambiente, o que é a linguagem e os primeiros programas              |
| Java        | A linguagem a fundo, sem framework: OO, biblioteca padrão, Java moderno e trilha avançada      |
| Spring      | Aplicações backend com Spring Boot: APIs, persistência, segurança, testes, deploy, arquitetura |
| Roadmap     | Trilha de estudos de backend com Java, do zero à produção                                      |
| Recursos    | Materiais externos para aprofundar cada assunto                                                |

## Estrutura do repositório

```text
java-labs/
├── notes/           → Anotações em Markdown puro (publicadas no site do Caramelo Labs)
├── sidebar.json     → Seções da barra lateral no site
├── examples/        → Exemplos de código, exercícios e projetos práticos
└── .github/         → Workflows, templates e guias de contribuição
```

Este repositório contém **apenas conteúdo** - não há build, dependências ou configuração de site. A estrutura web (Astro + Starlight) vive no repositório hub [labs](https://github.com/caramelotech/labs), que busca as notas daqui a cada atualização e publica o site.

## Como usar

Se você está começando agora, este fluxo funciona bem:

1. Leia as notas de `notes/fundamentos/` na ordem numérica (ou pelo [site](https://caramelotech.com.br/labs/java/))
2. Execute o exemplo [Hello World](examples/01-HelloWorld/)
3. Resolva os desafios em [examples/exercises.md](examples/exercises.md)
4. Escolha um projeto em [examples/projects.md](examples/projects.md)
5. Consulte o [roadmap](notes/roadmap.md) e os [recursos adicionais](notes/recursos.md) para aprofundar

## Executando os exemplos Java

```bash
cd examples/01-HelloWorld
javac HelloWorld.java
java HelloWorld
```

Recomendação de ambiente: JDK 17 LTS ou superior (`java --version`, `javac --version`).

## Escrevendo notas

As notas em `notes/` são Markdown puro, sem frontmatter:

- A primeira linha da nota deve ser o título: `# Título da Nota`
- Use prefixo numérico no nome do arquivo para controlar a ordem na barra lateral: `01-ambiente.md`, `02-introducao.md`
- Agrupe por tema em subpastas (`fundamentos/`, `java/`, `spring/`)
- Imagens ficam junto das notas (ex: `notes/secao/assets/img.png`) e são referenciadas com caminho relativo: `![descrição](./assets/img.png)`
- Links para outras notas do site usam o caminho completo: `/labs/java/<secao>/<nota>/`

Ao criar uma nova subpasta de tema, adicione a seção correspondente em `sidebar.json`.

## Contribuição

Contribuições são muito bem-vindas. Você pode ajudar com:

- Melhorias nas notas de `notes/`
- Novos exemplos em `examples/`
- Novos exercícios em `examples/exercises.md`
- Novos projetos em `examples/projects.md`
- Curadoria de recursos em `notes/recursos.md`

Antes de abrir um PR, leia o [Guia de Contribuição](.github/CONTRIBUTING.md).

## Licença

Este projeto está sob a licença definida em [LICENSE](LICENSE).
