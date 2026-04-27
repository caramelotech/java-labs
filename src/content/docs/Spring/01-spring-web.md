---
title: "Spring Web"
description: "Criando APIs REST com Spring Boot: controllers, rotas, injeção de dependência, IoC e consumo de APIs externas"
lastUpdated: 2026-04-26
sidebar:
  order: 11
tags: ["spring", "spring-boot", "rest", "java", "backend"]
---

Spring Boot é o framework mais usado para construir aplicações Java modernas. Ele elimina grande parte da configuração manual do Spring tradicional e permite ter uma API funcionando em poucos minutos.

## Criando um projeto

O jeito mais simples é pelo **Spring Initializr** em [start.spring.io](https://start.spring.io):

1. Escolha **Maven** ou **Gradle** como gerenciador de dependências
2. Selecione **Java** e a versão do Spring Boot (use a mais recente estável)
3. Adicione as dependências necessárias (para começar: `Spring Web`)
4. Clique em **Generate** e importe o projeto na IDE

### Maven vs Gradle

**Maven** usa XML (`pom.xml`) e é o mais tradicional. **Gradle** usa Groovy ou Kotlin (`build.gradle`) e é mais conciso e rápido. Ambos funcionam bem - a escolha é geralmente por preferência do time.

```xml
<!-- pom.xml - dependência do Spring Web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

```groovy
// build.gradle
implementation 'org.springframework.boot:spring-boot-starter-web'
```

## Classe principal

Todo projeto Spring Boot tem uma classe com o método `main` anotada com `@SpringBootApplication`:

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MinhaApiApplication {
    public static void main(String[] args) {
        SpringApplication.run(MinhaApiApplication.class, args);
    }
}
```

`@SpringBootApplication` combina três anotações: `@Configuration`, `@EnableAutoConfiguration` e `@ComponentScan`. Ao iniciar, o Spring escaneia o pacote da classe principal e todos os subpacotes em busca de componentes.

## IoC e Injeção de Dependência

**Inversão de Controle (IoC)** é o princípio onde o framework controla a criação e o ciclo de vida dos objetos, em vez do seu código.

**Injeção de Dependência (DI)** é a forma como o IoC é implementado: você declara do que uma classe depende, e o Spring cria e injeta os objetos.

```java
// Sem Spring - você cria as dependências manualmente
public class PedidoService {
    private final EmailService emailService = new EmailService(); // acoplado
    private final PedidoRepository repository = new PedidoRepository();
}

// Com Spring - o framework injeta as dependências
@Service
public class PedidoService {
    private final EmailService emailService;
    private final PedidoRepository repository;

    // Spring injeta automaticamente via construtor
    public PedidoService(EmailService emailService, PedidoRepository repository) {
        this.emailService = emailService;
        this.repository = repository;
    }
}
```

### Beans Spring

Um **bean** é um objeto gerenciado pelo container Spring. Para registrar uma classe como bean, use uma das anotações:

| Anotação          | Uso                                          |
| ----------------- | -------------------------------------------- |
| `@Component`      | Componente genérico                          |
| `@Service`        | Lógica de negócio                            |
| `@Repository`     | Acesso a dados                               |
| `@Controller`     | Controlador MVC                              |
| `@RestController` | Controlador REST (Controller + ResponseBody) |

## Criando um Controller REST

```java
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/produtos")
public class ProdutoController {

    // GET /produtos
    @GetMapping
    public List<String> listar() {
        return List.of("Notebook", "Mouse", "Teclado");
    }

    // GET /produtos/42
    @GetMapping("/{id}")
    public String buscar(@PathVariable Long id) {
        return "Produto " + id;
    }

    // POST /produtos
    @PostMapping
    public String criar(@RequestBody String nome) {
        return "Produto criado: " + nome;
    }

    // PUT /produtos/42
    @PutMapping("/{id}")
    public String atualizar(@PathVariable Long id, @RequestBody String nome) {
        return "Produto " + id + " atualizado para: " + nome;
    }

    // DELETE /produtos/42
    @DeleteMapping("/{id}")
    public void deletar(@PathVariable Long id) {
        System.out.println("Produto " + id + " removido");
    }
}
```

### Anotações de mapeamento

| Anotação         | Método HTTP | Uso                    |
| ---------------- | ----------- | ---------------------- |
| `@GetMapping`    | GET         | Buscar / listar        |
| `@PostMapping`   | POST        | Criar                  |
| `@PutMapping`    | PUT         | Substituir completo    |
| `@PatchMapping`  | PATCH       | Atualizar parcialmente |
| `@DeleteMapping` | DELETE      | Remover                |

### Extraindo dados da requisição

```java
// Parâmetro na URL: /produtos/42
@GetMapping("/{id}")
public Produto buscar(@PathVariable Long id) { ... }

// Query params: /produtos?categoria=eletronicos&page=1
@GetMapping
public List<Produto> listar(
    @RequestParam String categoria,
    @RequestParam(defaultValue = "0") int page
) { ... }

// Body da requisição
@PostMapping
public Produto criar(@RequestBody ProdutoRequest request) { ... }

// Headers
@GetMapping
public String exemplo(@RequestHeader("Authorization") String auth) { ... }
```

## ResponseEntity

Para ter controle total sobre a resposta (status code, headers, body):

```java
import org.springframework.http.ResponseEntity;
import org.springframework.http.HttpStatus;

@RestController
@RequestMapping("/usuarios")
public class UsuarioController {

    @PostMapping
    public ResponseEntity<Usuario> criar(@RequestBody Usuario usuario) {
        Usuario salvo = service.salvar(usuario);
        return ResponseEntity.status(HttpStatus.CREATED).body(salvo);
    }

    @GetMapping("/{id}")
    public ResponseEntity<Usuario> buscar(@PathVariable Long id) {
        return service.buscar(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deletar(@PathVariable Long id) {
        service.deletar(id);
        return ResponseEntity.noContent().build();
    }
}
```

## Arquitetura em camadas

A estrutura mais comum em projetos Spring:

```
Controller → Service → Repository
```

- **Controller:** recebe a requisição, valida entrada, delega para o service, retorna resposta
- **Service:** contém a lógica de negócio
- **Repository:** acessa o banco de dados

```java
// DTO - objeto de transferência de dados
public record ProdutoRequest(String nome, double preco) {}
public record ProdutoResponse(Long id, String nome, double preco) {}

// Controller - sem lógica de negócio
@RestController
@RequestMapping("/produtos")
public class ProdutoController {
    private final ProdutoService service;

    public ProdutoController(ProdutoService service) {
        this.service = service;
    }

    @PostMapping
    public ResponseEntity<ProdutoResponse> criar(@RequestBody ProdutoRequest request) {
        ProdutoResponse produto = service.criar(request);
        return ResponseEntity.status(201).body(produto);
    }
}

// Service - lógica de negócio
@Service
public class ProdutoService {
    private final ProdutoRepository repository;

    public ProdutoService(ProdutoRepository repository) {
        this.repository = repository;
    }

    public ProdutoResponse criar(ProdutoRequest request) {
        // validações, regras de negócio...
        Produto produto = new Produto(request.nome(), request.preco());
        Produto salvo = repository.save(produto);
        return new ProdutoResponse(salvo.getId(), salvo.getNome(), salvo.getPreco());
    }
}
```

## Tratamento de erros global

Em vez de try/catch em cada controller, use `@ControllerAdvice`:

```java
import org.springframework.web.bind.annotation.*;
import org.springframework.http.ResponseEntity;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RecursoNaoEncontradoException.class)
    public ResponseEntity<ErroResponse> handleNotFound(RecursoNaoEncontradoException e) {
        return ResponseEntity.status(404)
            .body(new ErroResponse("NOT_FOUND", e.getMessage()));
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErroResponse> handleGeneric(Exception e) {
        return ResponseEntity.status(500)
            .body(new ErroResponse("INTERNAL_ERROR", "Erro interno do servidor"));
    }
}

public record ErroResponse(String codigo, String mensagem) {}
```

## Consumindo APIs externas

### RestTemplate (tradicional)

```java
import org.springframework.web.client.RestTemplate;

@Service
public class ViaCepService {
    private final RestTemplate restTemplate = new RestTemplate();

    public EnderecoResponse buscarCep(String cep) {
        String url = "https://viacep.com.br/ws/" + cep + "/json/";
        return restTemplate.getForObject(url, EnderecoResponse.class);
    }
}
```

### WebClient (reativo, moderno)

```java
import org.springframework.web.reactive.function.client.WebClient;

@Service
public class ViaCepService {
    private final WebClient webClient;

    public ViaCepService(WebClient.Builder builder) {
        this.webClient = builder
            .baseUrl("https://viacep.com.br")
            .build();
    }

    public EnderecoResponse buscarCep(String cep) {
        return webClient.get()
            .uri("/ws/{cep}/json/", cep)
            .retrieve()
            .bodyToMono(EnderecoResponse.class)
            .block();
    }
}
```

## Configurações em application.properties

```properties
# Porta do servidor (padrão: 8080)
server.port=8080

# Prefixo de contexto
server.servlet.context-path=/api

# Nível de log
logging.level.com.exemplo=DEBUG

# Configurações personalizadas
app.nome=Minha API
app.versao=1.0.0
```

```java
// Injetando configurações
@Value("${app.nome}")
private String nomeApp;

// Ou com @ConfigurationProperties para grupos de configuração
@ConfigurationProperties(prefix = "app")
public record AppConfig(String nome, String versao) {}
```

## Testando com cURL

```bash
# GET
curl http://localhost:8080/produtos

# POST
curl -X POST http://localhost:8080/produtos \
  -H "Content-Type: application/json" \
  -d '{"nome": "Notebook", "preco": 2999.99}'

# PUT
curl -X PUT http://localhost:8080/produtos/1 \
  -H "Content-Type: application/json" \
  -d '{"nome": "Notebook Pro", "preco": 3499.99}'

# DELETE
curl -X DELETE http://localhost:8080/produtos/1
```

Ferramentas com interface gráfica: **Postman**, **Insomnia** ou a extensão **REST Client** do VS Code.
