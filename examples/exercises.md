# Exercícios

Pratique o que aprendeu nos conteúdos e exemplos de código.

## Como usar

1. Leia o enunciado do exercício
2. Tente resolver por conta própria antes de buscar ajuda
3. Consulte `src/content/docs/` se precisar revisar algum conceito
4. Compare sua solução com a dos colegas

## Exercício 01 - Hello World

**Objetivo:** Escrever e executar o primeiro programa Java.

**Instrução:**
Crie uma classe `OlaMundo` que imprima `"Olá, Caramelo Tech!"` na saída.

**Critérios de sucesso:**

- [ ] O arquivo se chama `OlaMundo.java`
- [ ] O programa compila sem erros (`javac OlaMundo.java`)
- [ ] A mensagem aparece corretamente ao executar (`java OlaMundo`)

## Exercício 02 - Variáveis e Tipos

**Objetivo:** Declarar e usar variáveis de diferentes tipos.

**Instrução:**

1. Declare uma variável `String` com seu nome
2. Declare uma variável `int` com sua idade
3. Declare uma variável `double` com sua altura em metros
4. Imprima uma frase combinando as três

Saída esperada:

```
Meu nome é Ana, tenho 25 anos e medo 1.68m.
```

**Critérios de sucesso:**

- [ ] As três variáveis são declaradas corretamente
- [ ] A mensagem é exibida conforme esperado

## Exercício 03 - Operadores Aritméticos

**Objetivo:** Usar operadores aritméticos e entender divisão inteira.

**Instrução:**

Crie uma classe `Calculadora` com um método `main` que:

1. Declare duas variáveis `int`: `a = 17` e `b = 5`
2. Imprima o resultado de cada operação: `+`, `-`, `*`, `/`, `%`
3. Declare uma variável `double` chamada `divisaoReal` com o resultado de `a / (double) b`
4. Imprima `divisaoReal`

Saída esperada:

```
Soma: 22
Subtração: 12
Multiplicação: 85
Divisão inteira: 3
Resto: 2
Divisão real: 3.4
```

**Critérios de sucesso:**

- [ ] Todas as cinco operações aritméticas são usadas
- [ ] A divisão inteira retorna `3`, não `3.4`
- [ ] A divisão real retorna `3.4`
- [ ] O programa compila e executa sem erros

> Consulte [Operadores em Java](/java-labs/03-operadores/) se precisar revisar.

---

## Exercício 04 - Operadores Relacionais e Lógicos

**Objetivo:** Combinar operadores relacionais e lógicos para avaliar condições.

**Instrução:**

Crie uma classe `VerificadorIdade` com um método `main` que:

1. Declare uma variável `int idade = 20` e `boolean temDocumento = true`
2. Usando operadores relacionais e lógicos, imprima:
   - Se a pessoa é maior de idade (`>= 18`)
   - Se a pessoa pode votar no Brasil (`>= 16`)
   - Se a pessoa pode dirigir (`>= 18` **e** `temDocumento == true`)
   - Se a pessoa é menor de 18 ou não tem documento

Saída esperada (com `idade = 20` e `temDocumento = true`):

```
Maior de idade: true
Pode votar: true
Pode dirigir: true
Menor de 18 ou sem documento: false
```

**Critérios de sucesso:**

- [ ] Usa pelo menos um operador `&&` e um `||`
- [ ] Usa pelo menos um operador `!`
- [ ] Os resultados mudam corretamente ao alterar os valores de `idade` e `temDocumento`

> Consulte [Operadores em Java](/java-labs/03-operadores/) se precisar revisar.

---

## Exercício 05 - Operador Ternário

**Objetivo:** Usar o operador ternário para simplificar condições.

**Instrução:**

Crie uma classe `Aprovacao` com um método `main` que:

1. Declare `double nota = 6.5`
2. Use o operador ternário para atribuir `"Aprovado"` ou `"Reprovado"` a uma variável `String resultado` (aprovado se `nota >= 7.0`)
3. Imprima o resultado
4. Repita com `nota = 8.0` e verifique que o resultado muda

Saída esperada:

```
Nota 6.5: Reprovado
Nota 8.0: Aprovado
```

**Critérios de sucesso:**

- [ ] O operador ternário é usado (não `if-else`)
- [ ] O resultado muda corretamente conforme a nota
- [ ] O programa compila e executa sem erros

> Consulte [Operadores em Java](/java-labs/03-operadores/) se precisar revisar.

---

## Exercício 06 - Estrutura de Repetição

**Objetivo:** Usar loops para repetir ações.

**Instrução:**
Escreva um programa que imprima os números de 1 a 10, um por linha, usando um loop `for`.

Saída esperada:

```
1
2
3
...
10
```

**Critérios de sucesso:**

- [ ] Todos os números de 1 a 10 são exibidos
- [ ] Cada número aparece em uma linha separada
- [ ] Um loop `for` é usado (não imprima cada número manualmente)

> Este exercício usa estruturas de controle que serão cobertas em módulos futuros.

---

## Exercício 07 - Classe e Encapsulamento

**Objetivo:** Criar uma classe com atributos privados e acesso via getters e setters.

**Instrução:**

Crie uma classe `Produto` com:

- Atributos privados: `String nome`, `double preco`, `int estoque`
- Construtor que recebe os três valores
- Getter para cada atributo
- Método `aplicarDesconto(double percentual)` que reduz o preço pelo percentual informado (ex: `10` reduz 10%)
- Método `adicionarEstoque(int quantidade)` que soma ao estoque atual (ignorar valores negativos)

Crie uma classe `Main` para testar:

```java
Produto p = new Produto("Notebook", 3500.0, 10);
System.out.println(p.getNome());    // Notebook
System.out.println(p.getPreco());   // 3500.0
p.aplicarDesconto(10);
System.out.println(p.getPreco());   // 3150.0
p.adicionarEstoque(5);
System.out.println(p.getEstoque()); // 15
```

**Critérios de sucesso:**

- [ ] Os atributos são `private`
- [ ] O construtor inicializa todos os atributos
- [ ] `aplicarDesconto(10)` reduz o preço de 3500.0 para 3150.0
- [ ] `adicionarEstoque(-3)` não altera o estoque
- [ ] O programa compila e executa sem erros

> Consulte [Programação Orientada a Objetos](/java-labs/11-orientacao-a-objeto/) se precisar revisar.

---

## Exercício 08 - Herança e Polimorfismo

**Objetivo:** Criar uma hierarquia de classes usando herança e sobrescrever métodos.

**Instrução:**

Crie as seguintes classes:

1. `Veiculo` — atributos `String marca` e `int ano`; método `void descricao()` que imprime `"Veículo: [marca], [ano]"`
2. `Carro extends Veiculo` — atributo `int numeroPortas`; sobrescreve `descricao()` para imprimir `"Carro: [marca], [ano], [numeroPortas] portas"`
3. `Moto extends Veiculo` — atributo `boolean temCarenagem`; sobrescreve `descricao()` para imprimir `"Moto: [marca], [ano], carenagem: [true/false]"`

Crie uma classe `Main` que instancie um `Carro` e uma `Moto` e chame `descricao()` em cada um.

Saída esperada:

```
Carro: Toyota, 2022, 4 portas
Moto: Honda, 2021, carenagem: true
```

**Critérios de sucesso:**

- [ ] `Carro` e `Moto` usam `extends Veiculo`
- [ ] Ambas sobrescrevem `descricao()` com `@Override`
- [ ] Os atributos herdados (`marca`, `ano`) são acessados nas subclasses
- [ ] O programa compila e executa sem erros

> Consulte [Programação Orientada a Objetos](/java-labs/11-orientacao-a-objeto/) se precisar revisar.
