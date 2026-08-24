# Java Collections Framework

Toda vez que você guarda uma lista de produtos, um conjunto de emails únicos ou um mapa de usuários por ID, está usando o Collections Framework. Ele é o conjunto de interfaces e classes prontas do Java para guardar e manipular grupos de objetos, e escolher a implementação certa faz diferença real em performance e correção do código.

## Hierarquia do framework

A raiz de tudo é `Iterable` - qualquer coisa que pode ser percorrida em um `for-each`. `Collection` estende `Iterable` e é a interface base de `List`, `Set` e `Queue`. `Map` fica fora dessa árvore porque não trabalha com uma sequência de elementos, e sim com pares chave-valor.

```mermaid
graph TD
    Iterable --> Collection
    Collection --> List
    Collection --> Set
    Collection --> Queue
    List --> ArrayList
    List --> LinkedList
    List --> Vector
    Vector --> Stack
    Set --> HashSet
    Set --> LinkedHashSet
    Set --> TreeSet
    Map --> HashMap
    Map --> LinkedHashMap
    Map --> TreeMap
    Map --> Hashtable
```

`Map` não aparece ligado a `Collection` no diagrama de propósito - ela é uma família à parte, mas segue as mesmas convenções de nomes e métodos das outras estruturas.

## Interfaces principais

- **Collection** - representa um grupo de objetos, é a interface mais genérica das três famílias abaixo dela
- **List** - sequência ordenada, permite elementos duplicados, acesso por índice
- **Set** - não permite elementos duplicados
- **Queue** - fila, pensada para elementos que aguardam processamento (FIFO na maioria das implementações)
- **Map** - pares chave-valor, cada chave aparece no máximo uma vez

## Implementações de List

| Classe       | Ordem | Duplicatas | Null | Thread-safe | Performance                   |
| ------------ | ----- | ---------- | ---- | ----------- | ----------------------------- |
| `ArrayList`  | Sim   | Sim        | Sim  | Não         | Rápida para `get`/`set`       |
| `LinkedList` | Sim   | Sim        | Sim  | Não         | Rápida para `add`/`remove`    |
| `Vector`     | Sim   | Sim        | Sim  | Sim         | Lenta (sincronização em tudo) |
| `Stack`      | Sim   | Sim        | Sim  | Sim         | Lenta                         |

`ArrayList` é apoiada por um array que cresce automaticamente por trás dos panos. Isso torna o acesso por índice (`get(i)`) muito rápido, mas inserir ou remover no meio da lista é caro, porque os elementos seguintes precisam ser deslocados:

```java
List<String> nomes = new ArrayList<>();
nomes.add("Ana");
nomes.add("Bruno");
nomes.add(0, "Zeca"); // desloca Ana e Bruno uma posição para a direita
```

`LinkedList` é uma lista duplamente encadeada - cada elemento aponta para o anterior e para o próximo. Inserir e remover no meio é barato (só rearranja os ponteiros vizinhos), mas acessar um índice qualquer exige percorrer a lista a partir de uma das pontas:

```java
List<String> fila = new LinkedList<>();
fila.add("primeiro");
fila.addFirst("zero"); // insere no início, O(1)
```

`Vector` e `Stack` são classes antigas, de antes do Collections Framework existir formalmente. Elas sincronizam todos os métodos internamente, o que as torna mais lentas mesmo em código single-thread. Use `ArrayList` no dia a dia e recorra a `Vector`/`Stack` só se algum código legado exigir.

## Implementações de Set

| Classe          | Ordem                         | Duplicatas | Null | Ordenação       |
| --------------- | ----------------------------- | ---------- | ---- | --------------- |
| `HashSet`       | Não garantida                 | Não        | Sim  | Nenhuma         |
| `LinkedHashSet` | Ordem de inserção             | Não        | Sim  | Nenhuma         |
| `TreeSet`       | Ordem natural (ou Comparator) | Não        | Não  | Sempre ordenado |

```java
Set<String> semOrdem = new HashSet<>();
Set<String> porInsercao = new LinkedHashSet<>();
Set<String> ordenado = new TreeSet<>();

for (Set<String> conjunto : List.of(semOrdem, porInsercao, ordenado)) {
    conjunto.add("banana");
    conjunto.add("maçã");
    conjunto.add("banana"); // ignorado, já existe
}
```

`HashSet` usa uma tabela hash por trás, então não há garantia nenhuma sobre a ordem de iteração - ela pode até mudar entre execuções. `LinkedHashSet` resolve isso mantendo uma lista ligada extra só para preservar a ordem de inserção. `TreeSet` guarda os elementos em uma árvore rubro-negra internamente, o que garante iteração sempre em ordem crescente, mas custa um pouco mais em cada inserção.

## Implementações de Map

| Classe          | Ordem               | Chaves duplicadas | Chave null  | Valores null | Ordenação       |
| --------------- | ------------------- | ----------------- | ----------- | ------------ | --------------- |
| `HashMap`       | Não garantida       | Não               | 1 permitida | Sim          | Nenhuma         |
| `LinkedHashMap` | Ordem de inserção   | Não               | 1 permitida | Sim          | Nenhuma         |
| `TreeMap`       | Ordenado pela chave | Não               | Não         | Sim          | Sempre ordenado |
| `Hashtable`     | Não garantida       | Não               | Não         | Não          | Nenhuma         |

```java
Map<String, Integer> idades = new HashMap<>();
idades.put("Ana", 28);
idades.put(null, -1); // HashMap aceita uma chave null, TreeMap não aceitaria
```

`TreeMap` exige que as chaves implementem `Comparable`, ou que você passe um `Comparator` no construtor - sem isso, ele não sabe em que ordem organizar as entradas. `Hashtable` é a versão legada e sincronizada do `HashMap`, criada antes do Java 1.2; hoje é melhor usar `HashMap` (ou `ConcurrentHashMap` em cenário multi-thread) e evitar `Hashtable` em código novo.

## Métodos comuns

Grande parte do trabalho com coleções usa sempre o mesmo punhado de métodos, então vale ter esse vocabulário na ponta da língua.

Em `Collection` (válido para `List` e `Set`):

```java
lista.add(elemento);
lista.remove(elemento);
lista.contains(elemento);
lista.size();
lista.isEmpty();
lista.clear();
lista.iterator();
```

Em `Map`:

```java
mapa.put(chave, valor);
mapa.get(chave);
mapa.remove(chave);
mapa.containsKey(chave);
mapa.containsValue(valor);
mapa.keySet();   // Set com todas as chaves
mapa.values();   // Collection com todos os valores
mapa.entrySet(); // Set de pares chave-valor, útil para iterar
```

## Iterator e fail-fast

`Iterator<E>` é o objeto que sabe percorrer uma coleção passo a passo, independente de como ela é implementada por dentro:

```java
Iterator<String> it = nomes.iterator();
while (it.hasNext()) {
    String nome = it.next();
    if (nome.equals("Bruno")) {
        it.remove(); // única forma segura de remover durante a iteração
    }
}
```

Repare que a remoção acontece pelo `Iterator`, não pela lista diretamente. Se você tentar `nomes.remove("Bruno")` dentro de um `for-each` que está iterando sobre `nomes`, o Java lança `ConcurrentModificationException`. Esse comportamento se chama **fail-fast**: as coleções do `java.util` detectam quando a estrutura foi modificada por fora do próprio iterador durante uma iteração e preferem falhar imediatamente a devolver um resultado inconsistente.

```java
for (String nome : nomes) {
    if (nome.equals("Bruno")) {
        nomes.remove(nome); // ConcurrentModificationException
    }
}
```

## For-each loop

O `for-each` foi introduzido no Java 5 como uma forma mais enxuta de percorrer qualquer `Iterable`:

```java
for (String nome : nomes) {
    System.out.println(nome);
}
```

Por baixo dos panos ele usa exatamente o `Iterator` que vimos acima, então herda a mesma limitação: é somente leitura em relação à estrutura da coleção. Dá para ler e até alterar o conteúdo de um objeto mutável dentro do loop, mas não dá para adicionar ou remover elementos da coleção enquanto ele roda.

## Classe utilitária Collections

Não confunda a interface `Collection` (singular) com a classe `Collections` (plural, com métodos estáticos utilitários):

```java
List<Integer> numeros = new ArrayList<>(List.of(3, 1, 4, 1, 5, 9, 2, 6));

Collections.sort(numeros);              // [1, 1, 2, 3, 4, 5, 6, 9]
Collections.reverse(numeros);           // inverte a lista atual
Collections.shuffle(numeros);           // embaralha aleatoriamente
int max = Collections.max(numeros);
int min = Collections.min(numeros);
int vezes = Collections.frequency(numeros, 1); // quantas vezes o 1 aparece

List<Integer> copia = new ArrayList<>(numeros);
Collections.copy(copia, numeros);

List<Integer> segura = Collections.synchronizedList(numeros); // envolve a lista para uso thread-safe
```

`Collections` é útil justamente para operações que fazem sentido "de fora" da coleção - ordenar, embaralhar, tornar thread-safe - em vez de espalhar essa lógica pelas próprias classes `ArrayList`, `HashSet` etc.
