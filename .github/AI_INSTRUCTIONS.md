# Instruções para Assistentes de IA

Este documento contém diretrizes para assistentes de IA que auxiliam na criação de conteúdo para o repositório Java4Beginners.

---

## 🎯 Objetivo do Repositório

Criar uma base de conhecimento completa e acessível sobre Java para iniciantes em português (pt-BR).

---

## 📋 Diretrizes Gerais

### Público-Alvo

- **Iniciantes** em programação
- Pessoas com pouco ou nenhum conhecimento de Java
- Estudantes que buscam material em português
- Autodidatas procurando conteúdo estruturado

### Tom e Linguagem

- ✅ Use **linguagem clara e acessível**
- ✅ Seja **didático e paciente**
- ✅ Explique **termos técnicos** quando usá-los
- ✅ Use **analogias do cotidiano**
- ✅ Forneça **exemplos práticos e relevantes**
- ❌ Evite jargões sem explicação
- ❌ Não assuma conhecimento prévio avançado
- ❌ Evite linguagem muito técnica ou acadêmica

---

## 📚 Estrutura de Conteúdo

### Arquivos de Conteúdo

Localização: `docs/XX-nome-do-topico.md`

Formato de numeração:
- `01-introducao-java.md`
- `02-variaveis-tipos-dados.md`
- `03-estruturas-controle.md`
- etc.

### Template Obrigatório

**SEMPRE** use o template em `.github/TEMPLATE.md` como base.

### Seções Essenciais

1. **Cabeçalho com metadados:**
   - Última atualização
   - Nível (Iniciante/Intermediário/Avançado)
   - Tempo estimado de leitura

2. **Introdução:**
   - O que será aprendido
   - Por que é importante
   - Contexto relevante

3. **Explicação do Conceito:**
   - Definição clara
   - Como funciona
   - Conceitos relacionados

4. **Exemplos Práticos:**
   - Código funcional e testado
   - Comentários explicativos
   - Saída esperada

5. **Exercícios:**
   - Práticos e aplicáveis
   - Com dicas de resolução
   - Progressão de dificuldade

6. **Navegação:**
   - Link para tópico anterior
   - Link para próximo tópico
   - Link para índice

---

## 💻 Código

### Padrões de Código Java

```java
// ✅ BOM: Código claro com comentários
public class CalculadoraSimples {
    // Método para somar dois números
    public int somar(int a, int b) {
        return a + b;
    }
}
```

```java
// ❌ EVITE: Código sem explicação
public class Calc {
    public int s(int x, int y) { return x + y; }
}
```

### Regras para Código

1. **Sempre inclua comentários explicativos**
2. **Use nomes descritivos** para variáveis e métodos
3. **Siga convenções Java:**
   - Classes: PascalCase
   - Métodos/variáveis: camelCase
   - Constantes: UPPER_SNAKE_CASE
4. **Teste o código** antes de incluir
5. **Inclua a saída esperada** quando relevante
6. **Mantenha exemplos simples** e focados

---

## 📖 Progressão de Conteúdo

### Ordem Lógica

Organize tópicos em sequência didática:

1. **Fundamentos:**
   - Introdução ao Java
   - Instalação e configuração
   - Primeiro programa
   - Variáveis e tipos de dados

2. **Básico:**
   - Operadores
   - Estruturas de controle (if, switch)
   - Loops (for, while)
   - Arrays

3. **Intermediário:**
   - Orientação a Objetos
   - Classes e Objetos
   - Herança
   - Polimorfismo
   - Encapsulamento

4. **Avançado:**
   - Coleções
   - Exceções
   - I/O
   - Threads
   - Generics

### Dependências

- Sempre mencione **pré-requisitos** no início
- Link para tópicos necessários
- Recapitule conceitos importantes

---

## 🎨 Formatação Markdown

### Títulos

```markdown
# Título Principal (H1) - Apenas um por arquivo
## Seção Principal (H2)
### Subseção (H3)
#### Detalhes (H4)
```

### Código

````markdown
```java
// Código Java
public class Exemplo {
    public static void main(String[] args) {
        System.out.println("Olá, Mundo!");
    }
}
```
````

### Destaques

```markdown
**Negrito** para ênfase
*Itálico* para termos técnicos
`código inline` para comandos/código
```

### Listas

```markdown
- Item não ordenado
- Outro item

1. Item ordenado
2. Segundo item
```

### Blocos de Citação

```markdown
> **Importante:** Informação crucial que o leitor deve saber
```

### Details/Summary (FAQ)

```markdown
<details>
<summary><strong>Pergunta frequente?</strong></summary>

Resposta detalhada aqui.

</details>
```

---

## 🔗 Links e Navegação

### Links Internos

```markdown
[📖 Tópico Relacionado](../docs/02-variaveis.md)
[💻 Exemplo de Código](../codigos/01-HelloWorld/)
```

### Links Externos

```markdown
[Documentação Oficial do Java](https://docs.oracle.com/javase/)
```

### Navegação de Rodapé

Sempre inclua no final:

```markdown
**[⬅️ Voltar ao Índice](../README.md)** | **[➡️ Próximo Tópico](02-topico.md)**
```

---

## 📊 Organização de Arquivos

```
java4beginners/
├── docs/                    # Conteúdos numerados
│   ├── README.md           # Índice de conteúdos
│   ├── 01-introducao.md
│   ├── 02-variaveis.md
│   └── ...
├── codigos/                 # Exemplos de código
│   ├── README.md           # Índice de códigos
│   ├── 01-HelloWorld/
│   ├── 02-Variaveis/
│   └── ...
├── recursos/                # Livros, cursos, etc.
│   └── README.md
└── .github/
    ├── TEMPLATE.md
    ├── CONTRIBUTING.md
    └── AI_INSTRUCTIONS.md
```

---

## ✅ Checklist para Novos Conteúdos

Antes de criar/sugerir novo conteúdo, verifique:

- [ ] Usei o template oficial
- [ ] Numeração está correta e sequencial
- [ ] Conteúdo está em pt-BR
- [ ] Código foi testado e funciona
- [ ] Incluí exemplos práticos
- [ ] Adicionei exercícios quando aplicável
- [ ] Links estão funcionando
- [ ] Ortografia e gramática revisadas
- [ ] Metadados preenchidos (nível, tempo)
- [ ] Navegação (anterior/próximo) configurada
- [ ] README/índice atualizado

---

## 🎯 Princípios de Design Pedagógico

### 1. Progressão Gradual
- Do simples ao complexo
- Um conceito por vez
- Revisão de conceitos anteriores

### 2. Aprendizado Ativo
- Exercícios práticos
- Desafios aplicáveis
- Projetos incrementais

### 3. Contexto Relevante
- Exemplos do mundo real
- Problemas práticos
- Aplicações concretas

### 4. Feedback Imediato
- Saídas de código mostradas
- Dicas para exercícios
- Avisos sobre erros comuns

### 5. Múltiplas Representações
- Texto explicativo
- Código de exemplo
- Analogias
- Diagramas quando necessário

---

## 🚫 O Que Evitar

### Conteúdo

- ❌ Assumir conhecimento prévio não mencionado
- ❌ Usar jargões sem explicação
- ❌ Exemplos muito complexos para o nível
- ❌ Pular etapas importantes
- ❌ Código sem comentários

### Estilo

- ❌ Parágrafos muito longos
- ❌ Linguagem muito formal/acadêmica
- ❌ Excesso de informação de uma vez
- ❌ Falta de exemplos práticos
- ❌ Links quebrados

### Organização

- ❌ Numeração inconsistente
- ❌ Estrutura diferente do template
- ❌ Falta de navegação
- ❌ README não atualizado
- ❌ Arquivos mal organizados

---

## 💡 Dicas para Criar Conteúdo de Qualidade

1. **Comece com o "porquê":** Explique por que o tópico é importante
2. **Use analogias:** Relacione conceitos técnicos com situações do dia a dia
3. **Mostre, não apenas diga:** Exemplos concretos são essenciais
4. **Antecipe dúvidas:** Inclua seção de perguntas frequentes
5. **Teste tudo:** Código deve funcionar e ser executável
6. **Revise sempre:** Ortografia, gramática e clareza são fundamentais
7. **Pense no iniciante:** Se você não entenderia há 6 meses, simplifique
8. **Incremental:** Construa conhecimento passo a passo

---

## 📝 Exemplos de Bom Conteúdo

### Título Descritivo
✅ "02 - Variáveis e Tipos de Dados em Java"
❌ "Vars"

### Explicação Clara
✅ "Uma variável é como uma caixa que armazena informações. Cada caixa tem um nome (identificador) e pode guardar um tipo específico de dado."
❌ "Variável é um identificador que referencia um espaço de memória."

### Exemplo Prático
✅ 
```java
// Declarando uma variável para armazenar a idade de uma pessoa
int idade = 25;
System.out.println("A idade é: " + idade); // Saída: A idade é: 25
```

❌
```java
int x = 25;
```

---

## 🔄 Processo de Criação

1. **Planejamento:**
   - Defina o escopo do tópico
   - Liste conceitos a cobrir
   - Identifique pré-requisitos

2. **Estruturação:**
   - Use o template
   - Organize seções logicamente
   - Planeje exemplos e exercícios

3. **Desenvolvimento:**
   - Escreva com clareza
   - Crie/teste código
   - Adicione recursos

4. **Revisão:**
   - Verifique clareza
   - Teste todos os links e códigos
   - Revise ortografia

5. **Integração:**
   - Atualize README/índice
   - Configure navegação
   - Verifique numeração

---

## 🎓 Recursos de Referência

Ao criar conteúdo, consulte:

- [Documentação Oficial Java](https://docs.oracle.com/javase/)
- [Java Tutorial da Oracle](https://docs.oracle.com/javase/tutorial/)
- Conteúdos já existentes no repositório para manter consistência

---

## 📞 Suporte

Em caso de dúvidas sobre estas instruções:

1. Consulte exemplos existentes em `docs/`
2. Revise o template em `.github/TEMPLATE.md`
3. Leia o guia de contribuição em `.github/CONTRIBUTING.md`

---

**Lembre-se:** O objetivo é tornar o aprendizado de Java **acessível, prático e agradável** para iniciantes!
