# Projetos

Mini projetos para aplicar o que foi aprendido de forma integrada.

## Como usar

1. Leia o enunciado do projeto
2. Planeje antes de codificar - defina classes, métodos e fluxo
3. Implemente incrementalmente
4. Teste cada parte antes de avançar

## Projeto 01 - Calculadora de Linha de Comando

**Objetivo:** Construir uma calculadora interativa via terminal.

**Descrição:**
Crie um programa que receba dois números e uma operação do usuário e exiba o resultado.

**Operações suportadas:**

- Adição (`+`)
- Subtração (`-`)
- Multiplicação (`*`)
- Divisão (`/`)

**Exemplo de uso:**

```
Informe o primeiro número: 10
Informe o operador (+, -, *, /): *
Informe o segundo número: 5
Resultado: 50
```

**Requisitos:**

- [ ] Receber os dois números e o operador via `Scanner`
- [ ] Exibir o resultado correto para cada operação
- [ ] Tratar divisão por zero com uma mensagem de erro
- [ ] Organizar a lógica em métodos separados por operação

**Desafio extra:**

- Permitir múltiplos cálculos sem reiniciar o programa
- Adicionar suporte a números decimais (`double`)

**Dica:**
Comece pela adição. Quando funcionar, adicione as demais operações com `if/else` ou `switch`.

## Projeto 02 - Cadastro de Estudantes

**Objetivo:** Praticar orientação a objetos com uma pequena aplicação de cadastro.

**Descrição:**
Crie um sistema simples que armazene estudantes e calcule médias.

**Requisitos:**

- [ ] Classe `Estudante` com nome, matrícula e lista de notas
- [ ] Método para calcular a média do estudante
- [ ] Método para verificar se está aprovado (média >= 7.0)
- [ ] Programa principal que cadastra ao menos 3 estudantes e exibe o resultado de cada um

**Exemplo de saída:**

```
Ana Silva - Média: 8.5 - Aprovada
Carlos Souza - Média: 6.0 - Reprovado
Maria Lima - Média: 7.0 - Aprovada
```
