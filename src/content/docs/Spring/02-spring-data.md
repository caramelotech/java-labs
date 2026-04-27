---
title: "Spring Data"
description: "Persistência de dados com Spring Data JPA, Hibernate, repositórios e H2 Database"
lastUpdated: 2026-04-26
sidebar:
  order: 12
tags: ["spring", "spring-data", "jpa", "hibernate", "banco-de-dados"]
---

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
