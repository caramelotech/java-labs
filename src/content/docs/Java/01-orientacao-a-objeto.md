---
title: "Programacao Orientada a Objetos"
description: "Classes, objetos, encapsulamento, abstracao, heranca, polimorfismo, interfaces e classes abstratas em Java"
lastUpdated: 2026-04-26
sidebar:
  order: 6
tags: ["java", "poo", "orientacao-a-objetos", "heranca", "polimorfismo"]
---

POO e um paradigma de programacao que organiza o codigo em torno de objetos - entidades que combinam estado (atributos) e comportamento (metodos). Em Java, tudo (exceto tipos primitivos) e um objeto.

## Classes e Objetos

Uma **classe** e o molde. Um **objeto** e uma instancia concreta desse molde.

```java
public class Carro {
    // Atributos (estado)
    String marca;
    String modelo;
    int ano;
    double velocidadeAtual;

    // Metodos (comportamento)
    void acelerar(double incremento) {
        velocidadeAtual += incremento;
    }

    void frear(double decremento) {
        velocidadeAtual = Math.max(0, velocidadeAtual - decremento);
    }

    void buzinar() {
        System.out.println("Beep beep!");
    }
}
```

Criando e usando objetos:

```java
public class Main {
    public static void main(String[] args) {
        Carro meuCarro = new Carro();
        meuCarro.marca = "Toyota";
        meuCarro.modelo = "Corolla";
        meuCarro.ano = 2023;

        meuCarro.acelerar(60);
        System.out.println(meuCarro.velocidadeAtual); // 60.0
        meuCarro.buzinar();
    }
}
```

## Construtores

Construtores inicializam objetos no momento da criacao. Tem o mesmo nome da classe e nenhum tipo de retorno:

```java
public class Carro {
    private String marca;
    private String modelo;
    private int ano;

    public Carro(String marca, String modelo, int ano) {
        this.marca = marca;
        this.modelo = modelo;
        this.ano = ano;
    }

    // Construtor vazio que delega ao principal
    public Carro() {
        this("Desconhecida", "Desconhecido", 0);
    }
}
```

```java
Carro c1 = new Carro("Honda", "Civic", 2022);
Carro c2 = new Carro(); // usa defaults
```

## Encapsulamento

Encapsular significa proteger o estado interno de uma classe e expor apenas o necessario. O padrao e deixar atributos `private` e fornecer metodos `public` de acesso:

```java
public class ContaBancaria {
    private double saldo;
    private String titular;

    public ContaBancaria(String titular, double saldoInicial) {
        this.titular = titular;
        this.saldo = saldoInicial >= 0 ? saldoInicial : 0;
    }

    public double getSaldo() {
        return saldo;
    }

    public void depositar(double valor) {
        if (valor > 0) {
            saldo += valor;
        }
    }

    public boolean sacar(double valor) {
        if (valor > 0 && valor <= saldo) {
            saldo -= valor;
            return true;
        }
        return false;
    }
}
```

O saldo nunca fica negativo porque a validacao acontece dentro da classe. Codigo externo nao tem como burlar isso.

## Heranca

Heranca permite que uma classe reutilize e estenda o comportamento de outra. Usa-se `extends`:

```java
public class Veiculo {
    protected String marca;
    protected int ano;

    public Veiculo(String marca, int ano) {
        this.marca = marca;
        this.ano = ano;
    }

    public void ligar() {
        System.out.println("Vrum!");
    }

    public String getInfo() {
        return marca + " (" + ano + ")";
    }
}

public class Carro extends Veiculo {
    private int numeroDePortas;

    public Carro(String marca, int ano, int portas) {
        super(marca, ano); // chama o construtor da superclasse
        this.numeroDePortas = portas;
    }

    @Override
    public String getInfo() {
        return super.getInfo() + " - " + numeroDePortas + " portas";
    }
}
```

```java
Carro c = new Carro("Honda", 2022, 4);
c.ligar();                        // herdado de Veiculo
System.out.println(c.getInfo());  // "Honda (2022) - 4 portas"
```

Java suporta apenas heranca simples - uma classe so pode `extends` uma superclasse. Para heranca multipla de comportamento, usa-se interfaces.

## Polimorfismo

Polimorfismo significa que um mesmo tipo de referencia pode se comportar de formas diferentes dependendo do objeto real:

```java
public class Animal {
    public void emitirSom() {
        System.out.println("...");
    }
}

public class Cachorro extends Animal {
    @Override
    public void emitirSom() {
        System.out.println("Au au!");
    }
}

public class Gato extends Animal {
    @Override
    public void emitirSom() {
        System.out.println("Miau!");
    }
}
```

```java
Animal[] animais = {new Cachorro(), new Gato(), new Cachorro()};

for (Animal a : animais) {
    a.emitirSom(); // comportamento diferente para cada tipo real
}
// Au au!
// Miau!
// Au au!
```

### Sobrecarga de metodos

**Sobrecarga** e quando varios metodos tem o mesmo nome mas assinaturas diferentes. A escolha acontece em tempo de compilacao:

```java
public class Calculadora {
    public int somar(int a, int b) {
        return a + b;
    }

    public double somar(double a, double b) {
        return a + b;
    }

    public int somar(int a, int b, int c) {
        return a + b + c;
    }
}
```

### Sobrescrita de metodos

**Sobrescrita** (`@Override`) e quando uma subclasse reimplementa um metodo da superclasse. A escolha acontece em tempo de execucao:

```java
@Override
public void emitirSom() {
    System.out.println("Au au!");
}
```

A anotacao `@Override` e opcional mas recomendada - se o metodo nao existir na superclasse, o compilador sinaliza o erro.

## Abstracao

Abstracao significa expor apenas o que e necessario e ocultar os detalhes de implementacao.

### Classes abstratas

Uma classe abstrata nao pode ser instanciada diretamente. Pode ter metodos concretos e abstratos:

```java
public abstract class Forma {
    private String cor;

    public Forma(String cor) {
        this.cor = cor;
    }

    public String getCor() {
        return cor;
    }

    // Subclasses sao obrigadas a implementar
    public abstract double calcularArea();
    public abstract double calcularPerimetro();
}

public class Circulo extends Forma {
    private double raio;

    public Circulo(String cor, double raio) {
        super(cor);
        this.raio = raio;
    }

    @Override
    public double calcularArea() {
        return Math.PI * raio * raio;
    }

    @Override
    public double calcularPerimetro() {
        return 2 * Math.PI * raio;
    }
}
```

### Interfaces

Uma interface e um contrato - define o que uma classe deve fazer, sem especificar como:

```java
public interface Pagamento {
    boolean processar(double valor);
    String getDescricao();
}

public class CartaoCredito implements Pagamento {
    private String numero;

    public CartaoCredito(String numero) {
        this.numero = numero;
    }

    @Override
    public boolean processar(double valor) {
        System.out.println("Processando R$ " + valor + " no cartao " + numero);
        return true;
    }

    @Override
    public String getDescricao() {
        return "Cartao final " + numero.substring(numero.length() - 4);
    }
}

public class Pix implements Pagamento {
    private String chave;

    public Pix(String chave) {
        this.chave = chave;
    }

    @Override
    public boolean processar(double valor) {
        System.out.println("Enviando R$ " + valor + " via Pix para " + chave);
        return true;
    }

    @Override
    public String getDescricao() {
        return "Pix - chave: " + chave;
    }
}
```

```java
// Mesma interface, implementacoes diferentes
Pagamento p = new CartaoCredito("1234567890123456");
p.processar(150.00);

Pagamento p2 = new Pix("email@exemplo.com");
p2.processar(150.00);
```

Uma classe pode implementar multiplas interfaces:

```java
public class ContaDigital implements Pagamento, Investimento, Auditavel {
    // implementa todos os contratos
}
```

### Interface vs Classe Abstrata

| Caracteristica         | Classe Abstrata            | Interface                      |
| ---------------------- | -------------------------- | ------------------------------ |
| Instanciacao           | Nao                        | Nao                            |
| Heranca multipla       | Nao (so uma por classe)    | Sim (varias por classe)        |
| Metodos com corpo      | Sim                        | Sim (default methods, Java 8+) |
| Atributos de instancia | Sim                        | Nao (so constantes)            |
| Quando usar            | Compartilhar implementacao | Definir contratos              |

## Modificadores de Acesso

| Modificador | Mesma classe | Mesmo pacote | Subclasse | Qualquer classe |
| ----------- | ------------ | ------------ | --------- | --------------- |
| `public`    | Sim          | Sim          | Sim       | Sim             |
| `protected` | Sim          | Sim          | Sim       | Nao             |
| _(default)_ | Sim          | Sim          | Nao       | Nao             |
| `private`   | Sim          | Nao          | Nao       | Nao             |

Regra geral: atributos sempre `private`, metodos `public` ou `protected` quando precisam ser herdados, `private` para auxiliares internos.

## Membros estaticos

`static` indica que o membro pertence a classe, nao a objetos individuais:

```java
public class Contador {
    private static int total = 0; // compartilhado entre todas as instancias
    private int id;

    public Contador() {
        total++;
        this.id = total;
    }

    public static int getTotal() { return total; }
    public int getId() { return id; }
}

Contador c1 = new Contador();
Contador c2 = new Contador();
Contador c3 = new Contador();
System.out.println(Contador.getTotal()); // 3
```

Metodos estaticos nao podem acessar atributos de instancia - nao existe `this` nesse contexto.
