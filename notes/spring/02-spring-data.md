# Spring Data

Spring Data JPA abstrai o acesso a banco de dados, eliminando a maior parte do código SQL manual. Com ele, você define entidades e repositórios, e o framework cuida da persistência.

## Dependências

No `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Para usar PostgreSQL -->
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
</dependency>

<!-- Para usar H2 (banco em memória para desenvolvimento) -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

## JPA e Hibernate

**JPA** (Jakarta Persistence API) é a especificação Java para mapeamento objeto-relacional. **Hibernate** é a implementação mais popular dessa especificação - é o que Spring Data usa por baixo.

O objetivo é mapear classes Java para tabelas de banco de dados e gerenciar as operações automaticamente.

## Entidades

Uma entidade é uma classe Java mapeada para uma tabela do banco:

```java
import jakarta.persistence.*;

@Entity
@Table(name = "usuarios")
public class Usuario {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String nome;

    @Column(nullable = false, unique = true, length = 150)
    private String email;

    @Column(name = "data_nascimento")
    private LocalDate dataNascimento;

    @Column(nullable = false)
    private boolean ativo = true;

    @CreationTimestamp
    @Column(name = "criado_em", updatable = false)
    private LocalDateTime criadoEm;

    // construtores, getters, setters...
}
```

### Anotações principais

| Anotação               | Função                                           |
| ---------------------- | ------------------------------------------------ |
| `@Entity`              | Marca a classe como entidade JPA                 |
| `@Table(name = "...")` | Define o nome da tabela (padrão: nome da classe) |
| `@Id`                  | Chave primária                                   |
| `@GeneratedValue`      | Estratégia de geração do ID                      |
| `@Column`              | Personaliza a coluna (nome, tamanho, nullable)   |
| `@Transient`           | Campo ignorado pelo JPA (não persiste)           |
| `@CreationTimestamp`   | Preenchido automaticamente no INSERT             |
| `@UpdateTimestamp`     | Preenchido automaticamente no UPDATE             |

Uma entidade precisa ser uma classe mutável de verdade, com construtor sem argumentos: um `record` (visto em Java Moderno) não funciona como entidade JPA, porque a especificação exige exatamente o que um record proíbe por definição (campos não-`final`, classe não-`final`, construtor vazio). Records continuam sendo a escolha certa para DTO e projeção de leitura desse mesmo dado, só não para a entidade gerenciada pelo Hibernate.

## Relacionamentos

### @ManyToOne e @OneToMany

```java
@Entity
public class Pedido {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // Muitos pedidos para um usuário
    @ManyToOne(optional = false)
    @JoinColumn(name = "usuario_id")
    private Usuario usuario;

    @Column(nullable = false)
    private BigDecimal valor;

    @Enumerated(EnumType.STRING)
    private StatusPedido status;
}

// No Usuario (opcional - mapeamento bidirecional)
@Entity
public class Usuario {
    // ...

    @OneToMany(mappedBy = "usuario", cascade = CascadeType.ALL)
    private List<Pedido> pedidos = new ArrayList<>();
}
```

### @ManyToMany

```java
@Entity
public class Produto {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToMany
    @JoinTable(
        name = "produto_categoria",
        joinColumns = @JoinColumn(name = "produto_id"),
        inverseJoinColumns = @JoinColumn(name = "categoria_id")
    )
    private List<Categoria> categorias = new ArrayList<>();
}
```

## Escolhendo a estratégia de chave primária

`@GeneratedValue(strategy = GenerationType.IDENTITY)` (delegando para um `AUTO_INCREMENT`/`SERIAL` do banco) é a opção mais comum para começar, mas em sistemas distribuídos ou de alto volume, a escolha do tipo de ID afeta performance de um jeito que só aparece depois que a tabela já cresceu.

`UUID.randomUUID()` (a versão 4 do UUID) é totalmente aleatório, e isso é justamente o problema para uma chave primária indexada. Bancos relacionais organizam índices numa estrutura de árvore (B-tree), pensada para inserções que chegam em sequência crescente. Um UUID aleatório cai num ponto imprevisível da árvore a cada inserção, o que aumenta a fragmentação do índice e o custo de I/O conforme a tabela cresce.

```java
@Id
private UUID id = UUID.randomUUID(); // funciona, mas fragmenta o índice ao longo do tempo
```

Isso não significa que a alternativa seja voltar para um ID sequencial simples. Sequências puras trazem problemas próprios: expõem informação do sistema (criar um registro no primeiro e no último dia do mês permite inferir quantos registros existem no período), e em arquitetura distribuída dependem de um contador centralizado, o que cria contenção e dificulta escalar horizontalmente sem coordenação entre instâncias.

O meio-termo mais usado hoje é UUIDv7 ou ULID, que incorporam um componente de tempo nos bits mais significativos do identificador, o que os torna aproximadamente ordenados por ordem de criação, e por isso muito mais amigáveis para índice B-tree e para particionamento por faixa (sharding) do que o UUIDv4 puro. UUIDv7 tem a vantagem de preservar o formato padrão de UUID, funcionando como substituto direto de `UUID.randomUUID()` sem mudar o tipo da coluna nem quebrar nenhum contrato existente.

Regra prática: se a tabela é pequena ou local a um único banco, `IDENTITY` continua sendo a opção mais simples. Se o sistema é distribuído ou de alto volume e você precisa gerar o ID antes de persistir (fora do banco), prefira UUIDv7 a UUIDv4 aleatório.

## Repositórios

Spring Data gera a implementação automaticamente. Você só define a interface:

### CrudRepository

Operações básicas de CRUD:

```java
import org.springframework.data.repository.CrudRepository;

public interface UsuarioRepository extends CrudRepository<Usuario, Long> {
    // findAll, findById, save, deleteById - já herdados
}
```

### JpaRepository

Estende `CrudRepository` com funcionalidades extras (paginação, ordenação):

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UsuarioRepository extends JpaRepository<Usuario, Long> {
    // Todos os métodos do CrudRepository + findAll(Pageable) etc.
}
```

`JpaRepository<Entidade, TipoDoId>` - use este na maioria dos casos.

### Query methods

Spring Data deriva queries automaticamente a partir do nome do método:

```java
public interface UsuarioRepository extends JpaRepository<Usuario, Long> {

    // SELECT * FROM usuarios WHERE email = ?
    Optional<Usuario> findByEmail(String email);

    // SELECT * FROM usuarios WHERE nome LIKE '%?%'
    List<Usuario> findByNomeContaining(String nome);

    // SELECT * FROM usuarios WHERE ativo = true ORDER BY nome
    List<Usuario> findByAtivoTrueOrderByNome();

    // SELECT * FROM usuarios WHERE ativo = ? AND email LIKE ?
    List<Usuario> findByAtivoAndEmailContaining(boolean ativo, String email);

    // Existência
    boolean existsByEmail(String email);

    // Contagem
    long countByAtivo(boolean ativo);

    // Deletar por critério
    void deleteByAtivoFalse();
}
```

### @Query para queries customizadas

```java
// JPQL - usa nomes de classes e atributos Java, não SQL
@Query("SELECT u FROM Usuario u WHERE u.email = :email AND u.ativo = true")
Optional<Usuario> buscarAtivoPorEmail(@Param("email") String email);

// SQL nativo
@Query(value = "SELECT * FROM usuarios WHERE YEAR(data_nascimento) = :ano",
       nativeQuery = true)
List<Usuario> buscarPorAnoNascimento(@Param("ano") int ano);

// Update/Delete via @Modifying
@Modifying
@Query("UPDATE Usuario u SET u.ativo = false WHERE u.id = :id")
void desativar(@Param("id") Long id);
```

### Paginação e ordenação

```java
import org.springframework.data.domain.*;

// No repositório
Page<Usuario> findByAtivo(boolean ativo, Pageable pageable);

// No service/controller
Pageable pageable = PageRequest.of(0, 20, Sort.by("nome").ascending());
Page<Usuario> pagina = repository.findByAtivo(true, pageable);

System.out.println(pagina.getContent());    // lista da página
System.out.println(pagina.getTotalElements()); // total de registros
System.out.println(pagina.getTotalPages());    // total de páginas
```

## Projeções: trazer só os campos que a tela usa

Imagine uma tela que lista usuários e mostra só nome e email. O jeito óbvio é `findByAtivoTrue()`, que devolve `List<Usuario>`. Só que a entidade `Usuario` tem uns 15 campos, talvez um relacionamento com `Endereco`, outro com `Pedido`. O banco carrega tudo isso, o Hibernate monta os objetos, e você usa dois campos.

Numa lista de 50 linhas isso passa despercebido. Numa de milhares, ou com relacionamentos que puxam mais tabelas junto, a diferença aparece: consultas que levam segundos para montar dados que ninguém vai olhar.

O custo extra de carregar a entidade completa numa leitura tem três partes:

- **Dirty checking**: toda entidade carregada entra no contexto de persistência, e o Hibernate guarda um snapshot dela para, no fim da transação, comparar campo a campo e ver o que mudou. Numa consulta só de leitura, esse trabalho é jogado fora.
- **Memória**: o objeto mais o snapshot, multiplicados pela quantidade de linhas.
- **SELECT gordo**: todas as colunas da tabela, mais os joins dos relacionamentos que forem `EAGER`.

Projeção é pedir ao Spring Data para trazer só um subconjunto de campos, num objeto que não é a entidade. Existem três formatos.

### Projeção com record (a mais direta)

Um [record](/labs/java/java/03-java-moderno/) com os campos que você quer, e um método no repositório que retorna esse tipo:

```java
public record ResumoUsuario(String nome, String email) {}
```

```java
public interface UsuarioRepository extends JpaRepository<Usuario, Long> {

    List<ResumoUsuario> findByAtivoTrue();
}
```

O Spring Data olha o record, vê que os nomes dos componentes (`nome`, `email`) batem com atributos da entidade, e gera um `SELECT u.nome, u.email FROM Usuario u WHERE u.ativo = true`. O resultado é uma lista de `ResumoUsuario`, e nenhum desses objetos entra no contexto de persistência: sem snapshot, sem dirty checking, sem flush comparando estado.

Para juntar dados de mais de uma tabela, use `@Query` com uma expressão de construtor JPQL (começa com `new` e o nome completo da classe):

```java
@Query("""
    SELECT new com.exemplo.dto.ResumoPedido(p.numero, u.nome, p.valorTotal, p.criadoEm)
    FROM Pedido p JOIN p.usuario u
    WHERE p.status = :status
    """)
List<ResumoPedido> resumoPorStatus(@Param("status") StatusPedido status);
```

Isso também funciona em query nativa, com `nativeQuery = true`, desde que os nomes das colunas retornadas batam com o construtor.

### Projeção por interface

Em vez de um record, uma interface só com os getters:

```java
public interface ResumoUsuario {
    String getNome();
    String getEmail();
}

List<ResumoUsuario> findByAtivoTrue();
```

O Spring Data cria um proxy em tempo de execução que implementa a interface. É a opção mais enxuta quando você não precisa de lógica nenhuma no objeto de saída, só ler os campos.

### Projeção dinâmica

Quando o mesmo método precisa às vezes devolver a entidade e às vezes um resumo, dá para deixar o tipo aberto:

```java
<T> List<T> findByAtivoTrue(Class<T> tipo);
```

```java
repository.findByAtivoTrue(Usuario.class);        // entidade completa
repository.findByAtivoTrue(ResumoUsuario.class);  // só o resumo
```

### Quando ainda usar a entidade

Projeção é para leitura. Se o fluxo vai alterar e salvar, você precisa da entidade gerenciada, porque é o dirty checking (aquele mesmo que era desperdício na leitura) que detecta a mudança e gera o `UPDATE`. A regra prática: consulta que só exibe dados pede projeção; consulta que carrega algo para modificar pede a entidade.

Vale notar a simetria com o DTO de entrada visto em [Validação, DTO e Logging](/labs/java/spring/04-validacao-e-logs/): lá, um objeto separado protege a entidade dos dados que chegam na requisição; aqui, um objeto separado evita expor e carregar a entidade inteira na resposta. Mesma ideia, pontas opostas do fluxo.

## H2 Database

H2 é um banco relacional em memória, ideal para desenvolvimento e testes. Não precisa de instalação.

```properties
# application.properties
spring.datasource.url=jdbc:h2:mem:devdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.database-platform=org.hibernate.dialect.H2Dialect

# Console web do H2 (acessível em /h2-console)
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

Acesse `http://localhost:8080/h2-console` para inspecionar o banco durante o desenvolvimento.

## Configuração com PostgreSQL

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/meudb
spring.datasource.username=postgres
spring.datasource.password=senha

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect

# Mostrar SQL gerado no log (útil em dev)
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# DDL: create, create-drop, update, validate, none
spring.jpa.hibernate.ddl-auto=validate
```

Em produção, use `validate` ou `none` e gerencie o schema com Flyway ou Liquibase.

## Exemplo completo - CRUD de Produto

### Entidade

```java
@Entity
@Table(name = "produtos")
public class Produto {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 200)
    private String nome;

    @Column(nullable = false, precision = 10, scale = 2)
    private BigDecimal preco;

    @Column(nullable = false)
    private int estoque;

    // getters e setters
}
```

### Repositório

```java
@Repository
public interface ProdutoRepository extends JpaRepository<Produto, Long> {
    List<Produto> findByNomeContainingIgnoreCase(String nome);
    List<Produto> findByEstoqueGreaterThan(int quantidade);
}
```

### Service

```java
@Service
@Transactional
public class ProdutoService {
    private final ProdutoRepository repository;

    public ProdutoService(ProdutoRepository repository) {
        this.repository = repository;
    }

    @Transactional(readOnly = true)
    public List<Produto> listar() {
        return repository.findAll();
    }

    @Transactional(readOnly = true)
    public Produto buscar(Long id) {
        return repository.findById(id)
            .orElseThrow(() -> new RecursoNaoEncontradoException("Produto não encontrado"));
    }

    public Produto criar(Produto produto) {
        return repository.save(produto);
    }

    public Produto atualizar(Long id, Produto dados) {
        Produto produto = buscar(id);
        produto.setNome(dados.getNome());
        produto.setPreco(dados.getPreco());
        produto.setEstoque(dados.getEstoque());
        return repository.save(produto);
    }

    public void deletar(Long id) {
        buscar(id); // verifica se existe
        repository.deleteById(id);
    }
}
```

### Controller

```java
@RestController
@RequestMapping("/produtos")
public class ProdutoController {
    private final ProdutoService service;

    public ProdutoController(ProdutoService service) {
        this.service = service;
    }

    @GetMapping
    public List<Produto> listar() {
        return service.listar();
    }

    @GetMapping("/{id}")
    public Produto buscar(@PathVariable Long id) {
        return service.buscar(id);
    }

    @PostMapping
    public ResponseEntity<Produto> criar(@RequestBody Produto produto) {
        Produto salvo = service.criar(produto);
        return ResponseEntity.status(201).body(salvo);
    }

    @PutMapping("/{id}")
    public Produto atualizar(@PathVariable Long id, @RequestBody Produto produto) {
        return service.atualizar(id, produto);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deletar(@PathVariable Long id) {
        service.deletar(id);
        return ResponseEntity.noContent().build();
    }
}
```

## @Transactional

Garante que operações de banco aconteçam dentro de uma transação:

```java
@Transactional
public void transferir(Long origemId, Long destinoId, BigDecimal valor) {
    Conta origem = buscar(origemId);
    Conta destino = buscar(destinoId);

    origem.debitar(valor);
    destino.creditar(valor);

    repository.save(origem);
    repository.save(destino);
    // Se qualquer linha acima lançar exceção, tudo é revertido (rollback)
}
```

Use `@Transactional(readOnly = true)` em métodos de apenas leitura - é uma dica de otimização para o banco.
