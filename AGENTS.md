# Java Labs - Guia para Agentes de IA

Bem-vindo ao **Java Labs**, uma base de conhecimento sobre Java para iniciantes em português. Este arquivo ajuda agentes de IA a entender a estrutura do projeto e ser produtivos rapidamente.

## Visão Geral do Projeto

- **Tipo**: Site de documentação educacional + exemplos práticos (Astro + Starlight)
- **Público**: Iniciantes em Java (português BR)
- **Objetivo**: Ensinar Java de forma progressiva com teoria, exemplos, exercícios e projetos
- **Licença**: [LICENSE](LICENSE)

## Estrutura Essencial

```
java-labs/
├── src/content/docs/         # Notas teóricas e roadmap (Markdown)
├── examples/                  # Código Java e material prático
├── package.json              # Scripts npm (dev, build, preview)
└── astro.config.mjs          # Configuração Starlight (title, locales, CSS)
```

## Comandos Essenciais

```bash
# Desenvolvimento local
npm run dev       # Servidor em localhost:4321

# Validação antes de PR
npm run build     # Gera build estático
npm run preview   # Preview do build

# Para exemplos Java
javac Arquivo.java   # Compilar
java NomeDaClasse    # Executar
```

## Padrões de Conteúdo

### Notas em `src/content/docs/`

1. **Numeração**: Arquivos numerados (`01-`, `02-`) indicam trilha obrigatória; outros são suplementares
2. **`sidebar.order` é sequencial por diretório**, não global. A ordem entre seções é controlada pelo array `sidebar` em `astro.config.mjs`. Dentro de cada pasta, numere os arquivos a partir de 1.
3. **Frontmatter Starlight obrigatório**:
   ```yaml
   ---
   title: "Título"
   description: "Resumo 1 linha"
   lastUpdated: YYYY-MM-DD
   sidebar:
     order: N
   tags: ["java", "tema", "nível"]
   ---
   ```
4. **Estilo**: Português BR, progressivo, explica "por quê", exemplos curtos
5. **Estrutura**: Headings `##` (primeira seção), `###` (subsections), não use `#` (Starlight gerencia)

### Criando uma nova seção superior

Para adicionar uma nova seção no topo da sidebar (ex: `nova-categoria/`):
1. Crie o diretório em `src/content/docs/nova-categoria/`
2. Adicione um arquivo `index.md` como página de destino
3. Adicione entrada `autogenerate` em `astro.config.mjs`:
   ```javascript
   {
     label: "Título da Seção",
     autogenerate: { directory: "nova-categoria" },
   }
   ```

### Exemplos em `examples/`

1. **Uma pasta por conceito**: `01-HelloWorld/`, `02-Variaveis/`, etc.
2. **Arquivo Java com comentário no topo**:
   ```java
   // Objetivo: Demonstrar X
   // Como usar: javac HelloWorld.java && java HelloWorld
   public class HelloWorld { ... }
   ```
3. **README.md complementar** com contexto, emojis de seção, modo de usar
4. **Sempre testar antes de submeter**: `javac` + `java` deve funcionar

### Exercícios em `examples/exercises.md` e Projetos em `examples/projects.md`

- Objetivo claro + nível de dificuldade
- Exemplo de saída esperada
- Critérios de sucesso em checklist
- Referências cruzadas para notas em `src/content/docs/`

## Regras Importantes para Agentes

### ✅ Quando adicionar/editar conteúdo

- **Notas**: Use frontmatter Starlight, mantenha numeração consistente, sempre explique "por quê"
- **Exemplos Java**: Pasta própria, README.md + comentários no código, teste antes
- **Exercícios**: Sejam progressivos (fácil → médio → difícil), com saída esperada clara
- **Markdown**: Não use `#` em docs (Starlight gerencia); comece com `##` para seções principais

### ⚠️ Pitfalls Comuns

1. **Duplicação de conteúdo**: Veja [CONTRIBUTING.md](.github/CONTRIBUTING.md) para detalhes, não copie integralmente
2. **Código Java não testado**: Sempre compile e execute antes de submeter
3. **Frontmatter incompleto**: Starlight precisa de `title`, `description`, `sidebar.order`, `tags`
4. **Numeração inconsistente**: Se começar numerado, mantenha sequência; se não, use nomes descritivos
5. **Sem ligação entre docs**: Exercícios devem referenciar notas; projetos devem citar múltiplas seções

### 💡 Decisões Arquiteturais Chave

- **Starlight para estrutura**: Sistema de docs, sidebar automático via frontmatter, suporta múltiplos idiomas
- **Numeração mista**: Trilha obrigatória (01-, 02-, 03-...) + suplementares (recursos.md, roadmap.md)
- **Exemplos em pasta**: Facilita adicionar código, testes, README próprio sem poluir raiz
- **Emojis no roadmap**: Torna visual e memorável; seções principais têm emojis únicos

## Links Importantes

- **Para contribuir**: [.github/CONTRIBUTING.md](.github/CONTRIBUTING.md) - Padrões de commit, branch, conteúdo
- **Trilha de aprendizado**: [src/content/docs/roadmap.md](src/content/docs/roadmap.md) - Mapa do 🚀
- **Exemplo completo**: [examples/01-HelloWorld/](examples/01-HelloWorld/) - Veja estrutura padrão
- **Exercícios**: [examples/exercises.md](examples/exercises.md) - Veja formato esperado
- **Site ao vivo**: [caramelotech.github.io/java-labs](https://caramelotech.github.io/java-labs)

## Perguntas Frequentes para Agentes

**P: Devo duplicar o conteúdo de CONTRIBUTING.md em minha documentação?**  
R: Não. Sempre linke para [.github/CONTRIBUTING.md](.github/CONTRIBUTING.md); adicione contexto específico apenas se necessário.

**P: Qual é a ordem correta para estudar?**  
R: Seguir [roadmap.md](src/content/docs/roadmap.md) e ordem numerada em `src/content/docs/`. Depois, práticar com `examples/`.

**P: Como valido um novo exemplo Java antes de commitar?**  
R: `cd examples/seu-exemplo && javac Arquivo.java && java NomeDaClasse`. Se funcionar, está pronto.

**P: Preciso de frontmatter completo mesmo em arquivos suplementares?**  
R: Sim. Starlight precisa de `title`, `description`, `sidebar.order`, `tags` mesmo que a nota não esteja na trilha principal.

---

**Última atualização**: 2026-04-26  
**Linguagem**: Português (pt-BR) | **Audiência**: Iniciantes | **Framework**: Astro + Starlight
