# Exemplos

Esta pasta concentra os materiais praticos do repositorio: exemplos executaveis, exercicios e projetos.

## Indice

| Caminho          | Tipo       | Descricao                                                |
| ---------------- | ---------- | -------------------------------------------------------- |
| `01-HelloWorld/` | Exemplo    | Primeiro programa em Java com codigo e README proprio    |
| `exercises.md`   | Exercicios | Lista de desafios para praticar os conceitos das notas   |
| `projects.md`    | Projetos   | Propostas de mini projetos para consolidar o aprendizado |

## Como usar os exemplos

1. Entre na pasta do exemplo desejado
2. Compile com `javac`
3. Execute com `java`

Exemplo:

```bash
cd examples/01-HelloWorld
javac HelloWorld.java
java HelloWorld
```

## Relacao com as notas

As explicacoes teoricas vivem em `src/content/docs/`. Uma boa sequencia e:

1. Ler a nota correspondente
2. Executar o exemplo relacionado
3. Resolver um exercicio
4. Evoluir para um projeto

## Adicionando novos exemplos

- Use uma pasta propria para cada exemplo relevante
- Inclua um `README.md` com contexto e instrucoes
- Prefira nomes claros, com prefixo numerico quando fizer parte da trilha
- Teste o codigo antes de abrir o PR

Mais detalhes estao em [../.github/CONTRIBUTING.md](../.github/CONTRIBUTING.md).
