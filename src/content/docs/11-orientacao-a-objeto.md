---
title: "Programação Orientada a Objetos"
description: "Conceitos de POO em Java com código: classe, objeto, encapsulamento, abstração, herança e polimorfismo"
lastUpdated: 2026-04-21
sidebar:
  order: 11
tags: ["java", "poo", "orientação-a-objetos", "herança", "iniciante"]
---

A Programação Orientada a Objetos (POO) é o paradigma central do Java. Em vez de escrever um programa como uma lista de instruções, você modela o problema em **classes** e **objetos** que interagem entre si.

Este módulo cobre os quatro pilares da POO com exemplos de código. Se você ainda não viu sintaxe básica e operadores, revise os módulos anteriores antes de continuar.

## Classe e Objeto

Uma **classe** é um modelo — ela descreve o que um objeto tem (atributos) e o que ele pode fazer (métodos). Um **objeto** é uma instância concreta desse modelo.

**Analogia:** a planta de uma casa é a classe; cada casa construída a partir dela é um objeto.

```java
// Definição da classe (o modelo)
public class Carro {
    String marca;
    String cor;
    int ano;

    void ligar() {
        System.out.println(marca + " ligado.");
    }
}
```

```java
// Criando objetos a partir da classe
public class Main {
    public static void main(String[] args) {
        Carro fusca = new Carro();
        fusca.marca = "Volkswagen";
        fusca.cor = "Azul";
        fusca.ano = 1970;

        Carro gol = new Carro();
        gol.marca = "Volkswagen";
        gol.cor = "Branco";
        gol.ano = 2020;

        fusca.ligar(); // Volkswagen ligado.
        gol.ligar();   // Volkswagen ligado.
    }
}
```

Os dois objetos (`fusca` e `gol`) são independentes — cada um tem seus próprios valores de atributos.

## Encapsulamento

Encapsulamento significa **proteger os dados internos** de uma classe e expor apenas o que for necessário. Na prática: atributos `private` e acesso via métodos públicos (`get`/`set`).

**Por que isso importa?** Sem encapsulamento, qualquer parte do código pode alterar um atributo diretamente, inclusive com valores inválidos.

```java
public class ContaBancaria {
    private String titular;
    private double saldo; // ninguém de fora altera saldo diretamente

    public ContaBancaria(String titular, double saldoInicial) {
        this.titular = titular;
        this.saldo = saldoInicial;
    }

    public void depositar(double valor) {
        if (valor > 0) {
            saldo += valor;
        }
    }

    public void sacar(double valor) {
        if (valor > 0 && valor <= saldo) {
            saldo -= valor;
        } else {
            System.out.println("Saldo insuficiente.");
        }
    }

    public double getSaldo() {
        return saldo;
    }

    public String getTitular() {
        return titular;
    }
}
```

```java
ContaBancaria conta = new ContaBancaria("Ana", 1000.0);
conta.depositar(500.0);
conta.sacar(200.0);
System.out.println(conta.getSaldo()); // 1300.0

// Isso não compila — saldo é privado:
// conta.saldo = -9999;
```

O saldo só pode ser alterado pelos métodos `depositar` e `sacar`, que têm validação. Isso é encapsulamento em ação.

## Abstração

Abstração significa **expor apenas o que é necessário** e ocultar os detalhes internos. O objetivo é que quem usa a classe não precise saber como ela funciona por dentro.

**Analogia:** você usa o volante e o acelerador de um carro sem precisar entender o funcionamento do motor.

```java
public class Motor {
    // detalhes internos — quem usa Motor não precisa conhecer isso
    private double pressaoOleo;
    private double temperaturaAgua;

    private void verificarPressao() {
        // lógica interna complexa
    }

    private void ajustarTemperatura() {
        // lógica interna complexa
    }

    // interface pública — simples e direta
    public void ligar() {
        verificarPressao();
        ajustarTemperatura();
        System.out.println("Motor ligado.");
    }

    public void desligar() {
        System.out.println("Motor desligado.");
    }
}
```

Quem usa a classe `Motor` só precisa chamar `ligar()` e `desligar()`. Toda a complexidade está encapsulada e abstraída.

## Herança

Herança permite que uma classe **aproveite atributos e métodos de outra**. A palavra-chave é `extends`.

```java
// Classe pai (superclasse)
public class Animal {
    String nome;

    public void comer() {
        System.out.println(nome + " está comendo.");
    }

    public void dormir() {
        System.out.println(nome + " está dormindo.");
    }
}
```

```java
// Classe filha (subclasse) — herda tudo de Animal
public class Cachorro extends Animal {
    public void latir() {
        System.out.println(nome + " está latindo: Au au!");
    }
}
```

```java
// Classe filha — herda tudo de Animal e adiciona comportamento próprio
public class Gato extends Animal {
    public void miar() {
        System.out.println(nome + " está miando: Miau!");
    }
}
```

```java
Cachorro dog = new Cachorro();
dog.nome = "Rex";
dog.comer();  // herdado de Animal: Rex está comendo.
dog.latir();  // próprio do Cachorro: Rex está latindo: Au au!

Gato cat = new Gato();
cat.nome = "Mimi";
cat.dormir(); // herdado de Animal: Mimi está dormindo.
cat.miar();   // próprio do Gato: Mimi está miando: Miau!
```

**Benefícios:**
- Reutilização de código — `comer()` e `dormir()` são escritos uma vez e herdados por todos os animais
- Hierarquia lógica — a relação "Cachorro é um Animal" fica explícita no código

## Polimorfismo

Polimorfismo significa "**muitas formas**". Um mesmo método pode ter comportamentos diferentes dependendo de qual objeto o executa. Usa-se a anotação `@Override` para sobrescrever um método da classe pai.

```java
public class Animal {
    String nome;

    public void fazerSom() {
        System.out.println(nome + " faz um som.");
    }
}
```

```java
public class Cachorro extends Animal {
    @Override
    public void fazerSom() {
        System.out.println(nome + " late: Au au!");
    }
}
```

```java
public class Gato extends Animal {
    @Override
    public void fazerSom() {
        System.out.println(nome + " mia: Miau!");
    }
}
```

O poder do polimorfismo aparece quando você trabalha com a classe pai como tipo:

```java
Animal[] animais = new Animal[3];
animais[0] = new Cachorro();
animais[0].nome = "Rex";
animais[1] = new Gato();
animais[1].nome = "Mimi";
animais[2] = new Cachorro();
animais[2].nome = "Bob";

for (Animal a : animais) {
    a.fazerSom(); // cada animal executa sua própria versão do método
}
```

**Saída:**
```
Rex late: Au au!
Mimi mia: Miau!
Bob late: Au au!
```

O código que percorre o array não precisa saber se é um `Cachorro` ou `Gato` — ele chama `fazerSom()` e cada objeto sabe o que fazer.

## Verifique seu Entendimento

Antes de avançar, tente responder mentalmente:

1. Qual a diferença entre uma classe e um objeto?
2. Por que usar atributos `private` em vez de `public`?
3. O que a palavra-chave `extends` faz?
4. Qual a diferença entre herança e polimorfismo?

<details>
<summary>Ver respostas</summary>

1. A **classe** é o modelo (o molde). O **objeto** é uma instância concreta criada a partir desse molde. Você pode criar vários objetos de uma mesma classe, cada um com seus próprios valores.
2. Atributos `private` impedem que código externo altere os dados diretamente, sem passar pelas validações da classe. Isso evita estados inválidos — como um saldo negativo sendo atribuído diretamente.
3. `extends` faz uma classe herdar todos os atributos e métodos da classe pai, evitando duplicação de código e estabelecendo uma hierarquia.
4. **Herança** é o mecanismo (uma classe reutiliza código de outra). **Polimorfismo** é o comportamento que isso permite (o mesmo método produz resultados diferentes dependendo do objeto). Um não existe sem o outro.

</details>

## Próximos Passos

Com os pilares da POO compreendidos, os próximos tópicos naturais são:

- **Interfaces e classes abstratas** — contratos que definem o que uma classe deve implementar
- **Construtores** — formas de inicializar objetos com valores obrigatórios
- **Collections** — estruturas como `ArrayList` e `HashMap` que usam POO intensamente
