# Arrays

Imagine que você precisa guardar as notas de 5 provas de um aluno. Dá para criar `nota1`, `nota2`, `nota3`, `nota4` e `nota5`, mas isso não escala - e se forem 30 provas? E se você não souber de antemão quantas vai ter? Um **array** resolve isso: é uma única variável que guarda várias posições do mesmo tipo, uma do lado da outra na memória.

## O que é um array

Um array é uma coleção de elementos do mesmo tipo, com **tamanho fixo**, onde cada elemento é acessado por um **índice** numérico que começa em `0`.

```java
int[] notas = {85, 90, 78, 92, 88};
//     índice:  0   1   2   3   4
```

A primeira nota é `notas[0]`, não `notas[1]` - esse é o detalhe que mais derruba quem está começando.

## Declaração e criação

Declarar um array e criar o array são dois passos que podem vir juntos ou separados:

```java
int[] numeros;              // declaração: numeros ainda não existe, é só a intenção
numeros = new int[5];       // criação: agora existem 5 posições, todas com valor 0
```

Ou tudo numa linha:

```java
int[] numeros = new int[5];
```

O `5` ali dentro dos colchetes é o tamanho do array, e ele **não muda depois de criado**. Se você precisar de mais espaço, precisa criar um array novo (ou, melhor ainda, usar uma `List`, que veremos mais adiante no curso).

## Inicializando com valores

Quando você já sabe os valores de antemão, não precisa passar pelo `new` com tamanho - a inicialização direta já define o tamanho pela quantidade de valores:

```java
int[] notas = {85, 90, 78, 92, 88};       // array de 5 posições
String[] nomes = {"Ana", "Bruno", "Carla"}; // array de 3 posições
```

Quando você cria um array com `new` e não passa valores, cada posição recebe o **valor padrão** do tipo: `0` para tipos numéricos, `false` para `boolean`, e `null` para tipos de referência como `String`:

```java
int[] contadores = new int[3];
System.out.println(contadores[0]); // 0 (valor padrão de int)

String[] mensagens = new String[3];
System.out.println(mensagens[0]); // null (valor padrão de referência)
```

## Acessando e modificando elementos

O índice entre colchetes serve tanto para ler quanto para atribuir um valor:

```java
int[] notas = {85, 90, 78, 92, 88};

System.out.println(notas[2]); // lê: 78

notas[2] = 80;                // modifica a posição 2
System.out.println(notas[2]); // 80
```

Para saber quantas posições um array tem, use `.length` - sem parênteses, porque não é um método, é um atributo do próprio array:

```java
System.out.println(notas.length); // 5
```

## Percorrendo um array

Com `.length` você sabe até onde ir com um `for` tradicional:

```java
int[] notas = {85, 90, 78, 92, 88};

for (int i = 0; i < notas.length; i++) {
    System.out.println("Posição " + i + ": " + notas[i]);
}
```

Quando você só precisa dos valores, sem se importar com o índice, o `for-each` (visto em [Controle de Fluxo](/labs/java/fundamentos/05-controle-fluxo/)) é mais direto:

```java
for (int nota : notas) {
    System.out.println(nota);
}
```

Use o `for` tradicional quando precisar do índice (para modificar uma posição, por exemplo); use o `for-each` quando só precisar ler os valores em ordem.

## O erro clássico: ArrayIndexOutOfBoundsException

Acessar uma posição que não existe não trava silenciosamente, nem devolve `null` - a JVM lança uma exceção na hora:

```java
int[] notas = {85, 90, 78};
System.out.println(notas[3]); // ArrayIndexOutOfBoundsException: Index 3 out of bounds for length 3
```

Um array de 3 posições tem índices válidos `0`, `1` e `2` - o último índice válido é sempre `length - 1`, nunca `length`. Esse erro por "um a mais" (off-by-one) é um dos mais comuns em loops que percorrem array: usar `<=` em vez de `<` na condição do `for` é a causa mais frequente.

## Arrays multidimensionais

Um array pode guardar outros arrays dentro dele - é assim que se representa uma tabela, uma matriz ou um tabuleiro:

```java
int[][] tabuleiro = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

System.out.println(tabuleiro[1][2]); // linha 1, coluna 2 -> 6
```

Percorrer um array bidimensional é um `for` dentro de outro `for`, um para a linha e outro para a coluna:

```java
for (int linha = 0; linha < tabuleiro.length; linha++) {
    for (int coluna = 0; coluna < tabuleiro[linha].length; coluna++) {
        System.out.print(tabuleiro[linha][coluna] + " ");
    }
    System.out.println();
}
```

## Arrays vs. o que vem depois

Array é a estrutura mais básica para guardar vários valores, mas o tamanho fixo é uma limitação real: se você não sabe quantos elementos vai ter, ou precisa adicionar e remover itens com frequência, um array obriga você a ficar criando um array novo e copiando tudo toda vez que o tamanho muda.

É exatamente esse problema que o [Java Collections Framework](/labs/java/java/04-colecoes/) resolve, com `List`, `Set` e `Map` crescendo e encolhendo sob demanda. Array continua sendo a base por baixo de várias dessas estruturas (um `ArrayList`, por exemplo, usa um array internamente), e aparece sempre que o tamanho é conhecido e fixo por natureza - os 7 dias da semana, os 3 canais de cor RGB, o tabuleiro de um jogo da velha.

## Boas práticas

- Prefira `for-each` quando não precisar do índice - menos chance de errar o limite do loop
- Nunca assuma que uma posição tem o valor que você espera sem checar `.length` antes de acessar um índice calculado
- Para coleções que crescem, encolhem ou têm tamanho desconhecido de antemão, use `List` em vez de array

## Verifique seu Entendimento

Antes de avançar, tente responder mentalmente:

1. Um array `String[] cores = new String[4];` foi criado sem valores. O que `cores[0]` imprime?
2. Qual o índice do último elemento de um array de tamanho 10?
3. Por que `notas[notas.length]` sempre lança `ArrayIndexOutOfBoundsException`?
4. Quando você deveria preferir uma `List` em vez de um array?

<details>
<summary>Ver respostas</summary>

1. `null` - é o valor padrão de qualquer tipo de referência (como `String`) quando o array é criado com `new` sem valores explícitos.
2. `9` - os índices vão de `0` a `length - 1`, então um array de tamanho 10 tem índices de `0` a `9`.
3. Porque `notas.length` é a *quantidade* de elementos, não um índice válido. Se o array tem `length` igual a 5, os índices válidos são `0` a `4` - `notas[5]` (ou qualquer `notas[notas.length]`) está um a mais do que o permitido.
4. Quando o tamanho não é conhecido de antemão, ou quando você precisa adicionar e remover elementos depois de criada a estrutura - um array não cresce nem encolhe depois de criado.

</details>

## Exercícios Práticos

1. Crie um array `int[] idades` com 5 valores e imprima a soma de todas as idades usando um `for` tradicional
2. Crie um array `String[] frutas` com pelo menos 4 nomes e use um `for-each` para imprimir cada um em maiúsculas (`.toUpperCase()`)
3. Escreva um método que recebe um `int[]` e devolve o maior valor do array
4. Crie uma matriz `int[][]` de 3 linhas por 3 colunas representando um tabuleiro da velha, preencha com zeros e imprima a posição central

## Próximos Passos

Com arrays dominados, você já tem a base para entender como o [Java Collections Framework](/labs/java/java/04-colecoes/) resolve as limitações de tamanho fixo - `List`, `Set` e `Map` são o próximo passo natural quando os dados crescem e mudam em tempo de execução.
