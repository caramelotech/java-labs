## Programação Orientada a Objetos (POO) em Java

Para desenvolver software de forma consistente em Java, é essencial compreender os pilares da POO.

### Classe e Objeto

- **Classe** → Modelo ou estrutura (planta de uma casa)
- **Objeto** → Instância da classe (casa construída)

**Exemplo conceitual:**

```
Classe: Carro
Objeto: MeuCarro (um carro específico, como um "Fusca azul, 1970")
```

### Encapsulamento

Consiste em **proteger os dados internos** de uma classe e permitir acesso controlado.

**Normalmente feito com:**

- Atributos privados (`private`)
- Métodos públicos (`get` e `set`)

**Benefícios:**

- 🔒 Segurança
- 📋 Organização
- 🎯 Controle de acesso

### Abstração

Abstração significa representar apenas as **características essenciais** de um objeto, escondendo detalhes desnecessários.

**Exemplo:**

Um usuário dirige um carro sem precisar saber como o motor funciona internamente. Você apenas usa o volante, acelerador e freio (interface pública).

### Herança

Permite que uma classe **herde características** de outra.

**Exemplo:**

```
Animal (classe pai)
   ↓
Cachorro (classe filha - herda características de Animal)
```

**Benefícios:**

- ♻️ Reutilização de código
- 📊 Hierarquia lógica
- ⚡ Redução de duplicação

### Polimorfismo

Significa "**muitas formas**".

Permite que um mesmo método tenha **comportamentos diferentes** dependendo do contexto.

**Exemplo:**

```
Animal.fazerSom()

Cachorro → Latir ("Au au!")
Gato → Miar ("Miau!")
```
