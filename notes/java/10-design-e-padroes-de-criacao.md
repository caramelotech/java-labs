# Design e Padrões de Criação

Você já viu os pilares da orientação a objetos em Java (Orientação a Objetos) e como o Spring gerencia beans e injeção de dependência (Spring Web). Essa nota fecha a lacuna entre os dois: como criar objetos de um jeito que não vire dor de cabeça depois, e como algumas decisões de design "óbvias" viram armadilha à medida que o sistema cresce.

## Construtores demais? Considere um método de fábrica estático

Um construtor com muitos parâmetros soltos é difícil de ler no ponto de chamada: `new Usuario("Jane", "jane@x.com", true, false, null, 3)` não diz nada sobre o que cada valor representa sem você abrir a classe.

Um método de fábrica estático troca essa lista posicional por um nome que já explica a intenção:

```java
public class Usuario {
    public static Usuario admin(String nome, String email) {
        return new Usuario(nome, email, Papel.ADMIN);
    }

    public static Usuario convidado(String nome, String email) {
        return new Usuario(nome, email, Papel.CONVIDADO);
    }

    private Usuario(String nome, String email, Papel papel) { /* ... */ }
}
```

`Usuario.admin("Jane", "jane@x.com")` já diz o que está sendo criado, sem precisar decorar a ordem dos parâmetros do construtor. Além do nome descritivo, um método de fábrica pode reaproveitar instâncias já existentes em vez de criar uma nova a cada chamada (pense em `Boolean.valueOf`, que devolve sempre as mesmas duas instâncias), e pode devolver um subtipo sem expor qual implementação concreta está sendo usada.

## Classe utilitária: nem toda classe representa um objeto do domínio

Algumas classes existem só como agrupamento de operações estáticas, sem estado nem identidade, `java.lang.Math` é o exemplo que a própria JDK usa. `new Math()` não faz sentido, porque não há nada para instanciar, e permitir essa instanciação só gera confusão sobre o que a classe representa.

A forma padrão de prevenir isso é declarar a classe `final` (impede que uma subclasse tente burlar via `super()`), concentrar só métodos `static`, e dar um construtor privado que lança exceção caso alguém tente chamar via reflection:

```java
public final class CalculoFrete {
    private CalculoFrete() {
        throw new AssertionError("classe utilitária, não instancie");
    }

    public static BigDecimal calcular(BigDecimal peso, BigDecimal distancia) { /* ... */ }
}
```

O `AssertionError` ali sinaliza violação de contrato de design (um erro de quem programa, não uma condição esperada em runtime). Um erro comum é misturar métodos estáticos com métodos de instância na mesma classe, se a classe tem os dois tipos misturados, provavelmente ela está tentando ser duas coisas ao mesmo tempo. Vale também evitar nomes genéricos como `Utils` ou `Helper`, que viram caixa-preta acumulando responsabilidades sem relação entre si. Nomes concretos como `CalculoFrete` ou `NormalizacaoDeTexto` forçam uma fronteira semântica clara.

## Builders: elegância que pode esconder bug

Um construtor com muitos campos opcionais empurra para o padrão telescoping constructor, várias sobrecargas do construtor, cada uma com mais parâmetros. Builder resolve isso oferecendo uma API fluente para montar o objeto passo a passo:

```java
Usuario usuario = Usuario.builder()
    .id(UUID.randomUUID())
    .nome("Jane Doe")
    .papeis(List.of("ADMIN"))
    .build();
```

O problema mais comum em builders escritos à mão é o vazamento de mutabilidade: se o objeto final guarda uma referência direta para uma lista passada ao builder, alguém pode alterar essa lista por fora depois da construção e mudar o estado do objeto que deveria ser imutável:

```java
var papeis = new ArrayList<>(List.of("ADMIN"));
var usuario = Usuario.builder().nome("Jane").papeis(papeis).build();
papeis.add("DEV"); // usuario.getPapeis() também muda, mesmo já "construído"
```

A defesa é copiar a coleção dentro do `build()`, com `List.copyOf(...)`, que cria uma cópia nova, independente e não modificável:

```java
public Usuario build() {
    return new Usuario(id, nome, List.copyOf(papeis)); // cópia defensiva
}
```

Outro problema comum é o telescoping builder disfarçado: um builder gigante com dezenas de setters chamáveis em qualquer ordem pode produzir estados inconsistentes, porque nada garante que os campos obrigatórios foram preenchidos antes do `build()`. Quando a ordem de preenchimento importa de verdade, um staged builder guia a sequência através de interfaces intermediárias, forçando `id` antes de `nome` antes de `papeis`, por exemplo, de modo que o código simplesmente não compila fora de ordem.

Builder não é thread-safe nem reutilizável por padrão, trate-o como descartável: uma instância, um `build()`. Quando você precisa editar um objeto já construído, exponha um `toBuilder()` que copia o estado atual para um builder novo, em vez de tentar reaproveitar o builder original:

```java
Usuario editado = usuarioExistente.toBuilder()
    .nome("Jane Silva")
    .build();
```

Validação cruzada entre campos (uma data de início que precisa ser anterior à de fim, por exemplo) deve morar no `build()` ou no construtor canônico, nunca espalhada pelos setters individuais, porque só no momento final você sabe o estado completo do objeto:

```java
public Usuario build() {
    if (dataInicio.isAfter(dataFim)) {
        throw new IllegalStateException("dataInicio não pode ser depois de dataFim");
    }
    return new Usuario(id, nome, List.copyOf(papeis), dataInicio, dataFim);
}
```

Se o seu projeto usa o `@Builder` do Lombok, vale lembrar que ele reduz a digitação, não a responsabilidade: a validação cruzada ainda precisa ser escrita manualmente em algum lugar. `@Singular` gera uma coleção nova a cada chamada, então evite usar em caminhos de execução muito frequentes (hot paths), e `@SuperBuilder` combinado com herança pode quebrar um invariante do tipo pai se a subclasse não revalidar.

## Singleton: do jeito clássico ele nasceu quebrado

O padrão Singleton do GoF, publicado em 1994, funcionava bem em aplicações de uma única thread. Com múltiplas threads, duas delas podiam criar instâncias diferentes ao mesmo tempo. A correção mais direta, `synchronized` no método inteiro, resolvia a corrida, mas criava um ponto de contenção permanente, mesmo depois que o objeto já estava criado e não havia mais corrida nenhuma para proteger.

Para evitar esse gargalo, surgiu o Double-Checked Locking: checar sem lock no caminho rápido, sincronizar só na criação. A ideia parece sólida, mas esconde um detalhe sutil: criar um objeto não é uma operação indivisível. `instancia = new Singleton()` envolve alocar memória, rodar o construtor e atribuir a referência, e sem a palavra-chave `volatile`, o runtime pode reordenar essas etapas. Outra thread pode enxergar a referência já preenchida antes do construtor terminar de rodar, e receber um objeto parcialmente inicializado, um bug que depende de timing e carga, passa nos testes com tranquilidade e aparece só sob concorrência real em produção.

```java
public class ConfigService {
    private static volatile ConfigService instancia; // volatile não é opcional aqui

    public static ConfigService getInstance() {
        if (instancia == null) {
            synchronized (ConfigService.class) {
                if (instancia == null) {
                    instancia = new ConfigService();
                }
            }
        }
        return instancia;
    }
}
```

Hoje existem caminhos mais simples e igualmente seguros: um `enum` com um único valor, quando a criação é rápida (a JVM garante inicialização única e segura por construção, sem código extra); ou o holder idiom, uma classe estática interna que só carrega quando referenciada pela primeira vez, aproveitando a garantia de inicialização do próprio class loader sem precisar de lock manual.

Vale contrastar isso com o "singleton" do Spring, que resolve o problema de outro jeito. Ao anotar uma classe com `@Component` (ou declarar um `@Bean`), o Spring cria só uma instância por `ApplicationContext`, mas por trás disso não há Double-Checked Locking nem `volatile` espalhado pelo seu código. O container mantém um cache (na prática, algo equivalente a um `ConcurrentHashMap`) com as instâncias já criadas. Consultas ao bean já existente não passam por nenhum lock; a criação de um bean novo acontece dentro de uma região protegida, uma única vez, e só depois disso a referência é publicada para as outras threads. Isso é conhecido como Singleton Registry Pattern, diferente do Singleton clássico do GoF: você nunca chama `Servico.getInstance()`, você recebe a instância única via injeção de construtor, o que mantém o código testável (dá para injetar um fake no teste) mesmo com uma única instância rodando em produção.

## Injeção de dependência é sobre quem decide criar o quê

Quando uma classe cria sua própria dependência com `new`, ela se acopla à implementação concreta:

```java
class ServicoDeEmail {
    private final ClienteSmtp cliente = new ClienteSmtp(); // impossível trocar por um fake no teste
}
```

Testar isso isoladamente é difícil: não dá para simular uma falha de rede, não dá para verificar se o e-mail "foi enviado" sem realmente enviar. Inverter essa responsabilidade, receber a dependência pronta em vez de criá-la, é o princípio de Inversão de Controle: a classe não decide de onde vem o colaborador, ela só recebe e usa.

```java
class ServicoDeEmail {
    private final ClienteSmtp cliente;

    ServicoDeEmail(ClienteSmtp cliente) { // injetado, pode ser um fake no teste
        this.cliente = cliente;
    }
}
```

A forma mais direta de aplicar isso é passar a dependência pelo construtor. Frameworks como o Spring (visto em Spring Web) só automatizam essa injeção, o princípio por trás é sempre o mesmo: uma classe não deveria decidir sozinha quem ela chama, só receber e usar.

## Modelo anêmico: dado sem comportamento

Anemic Domain Model é quando uma classe de domínio vira só um saco de campos com getters e setters, e toda a regra de negócio mora espalhada em classes de serviço:

```java
class Pedido {
    private BigDecimal saldo; // setter público, qualquer um pode deixar negativo
    public void setSaldo(BigDecimal saldo) { this.saldo = saldo; }
    public BigDecimal getSaldo() { return saldo; }
}

class PedidoService {
    void debitar(Pedido pedido, BigDecimal valor) {
        pedido.setSaldo(pedido.getSaldo().subtract(valor)); // nada impede saldo negativo aqui
    }
}
```

O setter público é o sintoma mais visível: ele expõe a representação interna e permite estado inválido a qualquer momento, porque a invariante (saldo nunca negativo, por exemplo) que deveria ser garantida em um único lugar vira um acordo informal espalhado entre todos os pontos que chamam o setter. Um sinal a observar em revisão de código: um método que só trabalha com os parâmetros recebidos e nunca toca nos campos da própria classe indica uma separação artificial entre estado e comportamento.

```java
class Pedido {
    private BigDecimal saldo;

    void debitar(BigDecimal valor) {
        if (saldo.compareTo(valor) < 0) throw new SaldoInsuficienteException();
        saldo = saldo.subtract(valor); // a regra mora junto do estado que ela protege
    }
}
```

Isso não é incompatível com controllers do Spring serem stateless por design, para atender requisições em paralelo sem compartilhar estado entre elas. É só uma questão de onde cada responsabilidade mora: o controller coordena a requisição, o domínio protege as próprias invariantes.

## Composição em vez de herança de classe concreta

Herdar de uma classe concreta para adicionar um comportamento (auditoria, validação) parece direto, mas cria uma dependência frágil de detalhes internos de implementação:

```java
class PedidoServiceValidado extends PedidoService {
    @Override
    void salvar(Pedido pedido) {
        validarCredito(pedido);
        super.salvar(pedido); // depende de salvar() ser o único caminho de persistência
    }
}
```

O problema aparece quando alguém adiciona um `salvarEmLote(...)` na classe base que não passa por `salvar()`, reaproveitando lógica interna de outro jeito. Nesse momento, pedidos processados em lote deixam de passar pela validação de crédito, sem nenhum erro de compilação, porque a subclasse dependia de uma ordem de chamadas internas que mudou sem aviso.

A alternativa mais segura é composição com o padrão Decorator: uma classe que implementa a mesma interface, contém uma instância do serviço original por dentro, e delega todas as chamadas depois de adicionar o comportamento extra:

```java
class PedidoServiceComValidacao implements PedidoService {
    private final PedidoService original;

    public void salvar(Pedido pedido) {
        validarCredito(pedido);
        original.salvar(pedido);
    }
}
```

Se um método novo aparecer na interface, o compilador força uma decisão explícita sobre como esse decorator lida com ele, em vez de deixar passar em silêncio como acontecia com a herança. Um padrão parecido aparece em bases como `BaseController` ou `AbstractService` pensadas só para compartilhar log ou tratamento de exceção: elas criam a mesma fragilidade, métodos protegidos chamados numa ordem implícita, estado mutável compartilhado via campo herdado. Para esse tipo de responsabilidade transversal (log, auditoria, métricas), AOP com `@Aspect` intercepta a chamada sem exigir que a classe de negócio herde de nada.

Herança continua fazendo sentido quando existe uma relação "é um" genuína e a superclasse foi desenhada de propósito para ser estendida (Java Core cobre quando escolher herança versus interface). Mesmo nesse caso, se você não controla o código da superclasse, composição costuma ser a opção mais segura.

## Interfaces pequenas e com propósito real

Um padrão comum e desnecessário é criar uma interface `XService` com uma única implementação `XServiceImpl`, sem nenhum motivo real além do costume. Isso adiciona uma camada de indireção sem trazer nada em troca. Uma interface só se justifica quando existem, ou vão existir de verdade, múltiplas implementações, ou quando ela marca uma fronteira real entre módulos.

```java
// interface sem propósito: existe uma única implementação, sempre existiu
interface PedidoService { void salvar(Pedido pedido); }
class PedidoServiceImpl implements PedidoService { /* ... */ }
```

Quando você precisa de uma implementação padrão de verdade, um nome como `PedidoServiceDefault` comunica melhor do que o sufixo genérico `Impl`, que não diz nada sobre a intenção daquela implementação específica.

Outro problema comum é violar o Princípio da Segregação de Interfaces: uma interface grande demais força quem implementa a declarar métodos que não fazem sentido para aquele caso.

```java
// interface grande demais, mistura responsabilidades que deveriam ser separadas
interface OrderService {
    void calcularFrete(Pedido pedido);
    void validarEstoque(Pedido pedido);
    void emitirNotaFiscal(Pedido pedido);
}
```

Se uma interface está fazendo demais, o problema raramente se resolve só criando uma interface em cima do que já existe, o design pede divisão real de responsabilidade: `CalculadoraDeFrete`, `ValidadorDeEstoque`, `EmissorDeNotaFiscal`, cada uma com uma única razão para mudar.

## Template Method com interface e default method

Implementar uma interface copiando dezenas de métodos repetidos é sinal de que falta uma camada intermediária. Interfaces com métodos `default` (desde o Java 8) permitem fornecer parte do comportamento pronto, deixando só o núcleo variável para quem implementa:

```java
interface Notificador {
    void enviar(String destinatario, String mensagem); // ponto de variação

    default void enviarComLog(String destinatario, String mensagem) { // comportamento pronto
        log.info("enviando para " + destinatario);
        enviar(destinatario, mensagem);
    }
}
```

Quando ainda sobra código repetido, ou existe estado comum entre implementações, uma classe abstrata que já implementa a interface e concentra esse comportamento resolve o que o `default method` sozinho não cobre (interface não guarda estado nem sobrescreve `equals`/`hashCode`). É o padrão Template Method do GoF aplicado: o algoritmo mora na classe base, os pontos de variação ficam nos métodos abstratos que cada subclasse preenche. É assim que `AbstractList` e `AbstractMap`, da própria JDK, são construídas.

Evite estender uma classe `AbstractX` só para "reaproveitar código" sem que exista de fato um contrato sendo implementado, e evite concentrar responsabilidade demais numa classe base genérica. Quem implementa a interface deveria optar por herdar da classe base, não ser obrigado a isso.

## Magic numbers e constantes sem gambiarra

Um número solto no meio do código perde significado rápido: `if (idade < 21)` não explica por si só o que `21` representa ali.

```java
if (idade < 21) { /* ... */ } // 21 o quê? maioridade em qual contexto?
```

Nomear resolve isso de forma direta:

```java
private static final int IDADE_MINIMA_PARA_CONSUMO_ALCOOLICO = 21;
if (idade < IDADE_MINIMA_PARA_CONSUMO_ALCOOLICO) { /* ... */ }
```

Um segundo erro, menos óbvio, é criar uma interface só para concentrar constantes, às vezes uma única interface gigante reunindo todas as constantes do sistema. Interfaces definem contrato de comportamento observável; constantes não têm comportamento, então misturar os dois polui o modelo e cria uma dependência de compatibilidade binária desnecessária (qualquer classe que precise de uma dessas constantes acaba "implementando" um contrato vazio só para acessá-las).

A alternativa é uma classe utilitária no molde visto antes (`final`, construtor privado, campos `static final` em maiúsculas), ou, para um conjunto discreto e nomeável de valores, um `enum` (que vimos em detalhe em Enums Avançado).

## Testar métodos privados é o sintoma, não o problema

A recomendação clássica é testar comportamento observável, não implementação interna. Um teste que testa diretamente um método `private` está testando um detalhe que deveria poder mudar livremente, desde que o comportamento público continue correto.

Quando surge vontade de testar um método privado diretamente, isso normalmente sinaliza que o design está sob pressão de testabilidade: o método privado carrega lógica densa demais para ser validada só através da API pública. Duas saídas costumam resolver isso sem quebrar o encapsulamento: extrair essa lógica para uma classe própria, coesa o suficiente para ser testada isoladamente através da sua própria API pública; ou tornar o método `package-private` em vez de `private`, o que preserva o encapsulamento entre módulos diferentes mas permite que a suíte de teste, no mesmo pacote, acesse ele diretamente.

```java
class CalculadoraDeDesconto {
    BigDecimal calcular(Pedido pedido) { // package-private, testável sem ser public
        /* lógica densa o suficiente para merecer teste direto */
    }
}
```

Uma anotação como `@VisibleForTesting` (de bibliotecas como Guava) ajuda a documentar que aquela visibilidade maior é uma concessão para o teste, não parte da API pretendida. Regra prática: não torne nada `public` só para testar. Prefira extrair para uma classe coesa, ou usar `package-private` quando fizer sentido.

## Programe contra a interface, não contra a implementação

Declarar uma variável, parâmetro ou retorno com um tipo concreto (`ArrayList`, `HashMap`) em vez da interface correspondente (`List`, `Map`) limita a flexibilidade sem necessidade:

```java
// obriga todo mundo que chama esse método a ter uma ArrayList em mãos
void recalcularMargens(ArrayList<Pedido> pedidos) { /* ... */ }
```

Isso amarra quem chama a uma implementação específica, mesmo que o método nunca use nada exclusivo de `ArrayList`. Declarar com a interface mais genérica que atende à necessidade real elimina conversões forçadas do lado de quem chama e permite trocar a implementação interna sem afetar ninguém de fora:

```java
void recalcularMargens(List<Pedido> pedidos) { /* ... */ } // aceita ArrayList, LinkedList, List.of(), etc
```

A instanciação concreta continua normal (`new ArrayList<>()`), a regra vale para como você declara variáveis, parâmetros e retornos: use o tipo mais genérico que atenda à necessidade, guarde o tipo concreto só para o momento de criar o objeto.
