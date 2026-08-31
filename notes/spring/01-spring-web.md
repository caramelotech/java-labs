# Spring Web

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

### Starters mais usados

Starters são pacotes de dependências prontos - em vez de adicionar biblioteca por biblioteca e resolver conflito de versão manualmente, você adiciona um starter e ganha tudo que ele precisa:

| Starter                          | Para que serve               |
| -------------------------------- | ---------------------------- |
| `spring-boot-starter-web`        | Construir aplicações web/API |
| `spring-boot-starter-data-jpa`   | JPA e Hibernate              |
| `spring-boot-starter-security`   | Segurança                    |
| `spring-boot-starter-validation` | Validação de dados           |
| `spring-boot-starter-test`       | Testes                       |
| `spring-boot-starter-actuator`   | Monitoramento                |

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

Assim que o Spring registra um bean, ele passa por um ciclo de vida fixo: primeiro **instancia** o objeto, depois **popula as propriedades** (injeta as dependências), em seguida **inicializa** o bean (chamando `@PostConstruct`, se existir), deixa ele disponível para **uso** durante toda a vida da aplicação e, ao encerrar o contexto, chama a **destruição** (`@PreDestroy`, se existir).

```
Instanciar -> Popular propriedades -> Inicializar -> Usar -> Destruir
```

### Injeção por Autowired, Qualifier e Primary

`@Autowired` é a anotação que diz ao Spring "injete aqui a dependência certa automaticamente". Ela pode ir no construtor, num setter ou direto no campo:

```java
@Service
public class PedidoService {

    @Autowired
    private EmailService emailService; // injeção por campo
}
```

Isso funciona, mas existem três formas de injeção e elas não são equivalentes:

1. **Construtor** - recomendada. Deixa as dependências explícitas, permite que os campos sejam `final` (imutáveis) e torna impossível criar o objeto num estado inválido, sem suas dependências.
2. **Setter** - útil para dependências opcionais, que podem ser trocadas depois da criação do objeto.
3. **Campo** - a mais simples de escrever, mas dificulta testes (não dá para injetar um mock sem reflection) e esconde as dependências reais da classe.

Quando o construtor tem um único parâmetro de cada tipo, o Spring já resolve sozinho e o `@Autowired` no construtor nem precisa ser escrito explicitamente (como já visto na seção de IoC acima). O problema aparece quando existe mais de um bean candidato para o mesmo tipo:

```java
public interface Pagamento { }

@Component
public class PagamentoCartao implements Pagamento { }

@Component
public class PagamentoPix implements Pagamento { }
```

Se algum lugar do código pedir `Pagamento pagamento` sem mais detalhes, o Spring não sabe qual dos dois beans usar. `@Qualifier` resolve isso apontando o nome exato do bean desejado:

```java
@Service
public class CheckoutService {
    private final Pagamento pagamento;

    public CheckoutService(@Qualifier("pagamentoPix") Pagamento pagamento) {
        this.pagamento = pagamento;
    }
}
```

Outra alternativa é `@Primary`, que marca um dos beans como o padrão sempre que houver ambiguidade e nenhum `@Qualifier` for informado:

```java
@Component
@Primary
public class PagamentoCartao implements Pagamento { }
```

Use `@Qualifier` quando o código que injeta precisa escolher explicitamente qual implementação quer, e `@Primary` quando existe uma implementação "óbvia" que deve ser o padrão na maioria dos casos.

Preferir injeção por construtor tem outro efeito: quando dois beans dependem um do outro, o ciclo aparece já no startup em vez de ser resolvido silenciosamente. Veja [Dependência circular no Spring](/labs/java/spring/10-dependencia-circular/) para entender como o framework lida com isso.

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

Todas essas anotações são atalhos para `@RequestMapping` com o método HTTP já fixado. `@RequestMapping` sozinho aparece no nível da classe, definindo o prefixo de rota comum a todos os endpoints do controller.

### PUT x PATCH

A dúvida mais comum na hora de montar um endpoint de atualização. As duas mexem num recurso que já existe, mas com semânticas diferentes.

`PUT` substitui o recurso inteiro pelo que veio no corpo. Se o cliente manda `{"nome": "Notebook Pro"}` e omite o campo `preco`, a interpretação correta é que o preço deve virar nulo (ou o valor padrão), não que ele fica como estava. O corpo do PUT é o novo estado completo do recurso.

`PATCH` aplica uma alteração parcial. O corpo descreve só o que muda, e os campos ausentes ficam intocados. `{"preco": 3499.99}` num PATCH altera apenas o preço.

Uma consequência prática é a idempotência. `PUT` é idempotente: mandar a mesma requisição uma ou dez vezes deixa o recurso no mesmo estado final. `PATCH` não é necessariamente, depende de como a alteração é descrita (um PATCH do tipo "incremente o estoque em 1" muda o resultado a cada chamada). O `PATCH` foi padronizado depois dos outros métodos, pela RFC 5789, justamente para cobrir o caso de atualização parcial que o `PUT` não atende bem.

Na prática, muita API trata o PATCH como "PUT parcial" (manda um subconjunto dos campos, esses são atualizados) em vez de seguir formatos de patch mais formais como JSON Patch. Funciona, só não é o que a RFC descreve ao pé da letra.

E o POST? Ele até serve para atualizar (o HTTP permite), mas num design REST limpo o POST fica reservado para criar recursos, e a atualização vai de PUT ou PATCH conforme o caso.

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

### Códigos de status HTTP

O código de status é a primeira coisa que quem consome a API olha para saber se algo deu certo. Os mais comuns em APIs REST:

| Código | Significado           | Quando usar                               |
| ------ | --------------------- | ----------------------------------------- |
| 200    | OK                    | Requisição bem-sucedida (GET, PUT, PATCH) |
| 201    | Created               | Recurso criado com sucesso (POST)         |
| 204    | No Content            | Sucesso sem corpo de resposta (DELETE)    |
| 400    | Bad Request           | Requisição inválida (validação falhou)    |
| 401    | Unauthorized          | Não autenticado                           |
| 403    | Forbidden             | Autenticado, mas sem permissão            |
| 404    | Not Found             | Recurso não existe                        |
| 500    | Internal Server Error | Erro inesperado no servidor               |

Padronizar o formato da resposta - tanto de sucesso quanto de erro - facilita muito a vida de quem consome a API, porque o formato fica previsível independente do endpoint:

```json
{
  "success": true,
  "message": "Operação realizada com sucesso",
  "data": { "id": 1, "nome": "Notebook" },
  "timestamp": "2026-08-24T10:30:00"
}
```

### Boas práticas RESTful

- **Use substantivos, não verbos, nas URLs** - `/students`, não `/getStudents`. O verbo já está no método HTTP.
- **Use o método HTTP correto** - GET para buscar, POST para criar, PUT para substituir, PATCH para atualizar parcialmente, DELETE para remover.
- **Mantenha URLs em minúsculas** - `/students/10`, não `/Students/10`.
- **Retorne o código de status apropriado** - não devolva 200 para tudo, nem 500 para um erro de validação que é culpa do cliente (isso é 400).
- **Pagine listas grandes** - devolver 50 mil registros de uma vez trava tanto o servidor quanto quem consome.
- **Use DTOs em vez da entidade crua** - evita expor colunas internas do banco e desacopla o contrato da API do modelo de persistência.
- **Mantenha as respostas consistentes** - o mesmo formato de sucesso e erro em toda a API, não um formato diferente por endpoint.

```
✓ GET    /students          ✗ GET    /getStudents
✓ GET    /students/10       ✗ GET    /students/getAllStudents
✓ POST   /students          ✗ POST   /createStudent
✓ PUT    /students/10       ✗ PUT    /updateStudent/10
✓ DELETE /students/10       ✗ DELETE /deleteStudent/10
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

Essa divisão em três camadas dá conta da maioria dos projetos. Quando o domínio fica complexo e a regra de negócio começa a se espalhar, vale conhecer a [Arquitetura Limpa](/labs/java/spring/07-arquitetura-limpa/), que reorganiza essas responsabilidades para manter o núcleo de negócio independente de framework e banco.

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

O mesmo em `application.yml` - formato equivalente, só muda a sintaxe (hierárquico em vez de chave plana com pontos):

```yaml
server:
  port: 8080
  servlet:
    context-path: /api

logging:
  level:
    com.exemplo: DEBUG

app:
  nome: Minha API
  versao: 1.0.0
```

Os dois formatos funcionam igual - `.properties` é mais direto para configurações pequenas, `.yml` fica mais legível quando a estrutura cresce e tem muitos níveis aninhados (como configuração de datasource, por exemplo).

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

## Referências

- [Web MVC - Annotated Controllers: Mapping Requests](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html) - documentação oficial do Spring, inglês
- [RFC 5789: PATCH Method for HTTP](https://www.rfc-editor.org/rfc/rfc5789.html) - IETF, inglês
- [HTTP request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) - MDN Web Docs, inglês
- [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service) - guia oficial do Spring, inglês
