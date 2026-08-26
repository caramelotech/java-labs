# Testes, Build e Deploy

Escrever a API é só parte do trabalho. Sem testes, cada mudança é um salto de fé; sem um processo claro de build e deploy, colocar a aplicação no ar vira um ritual manual propenso a erro. Esta nota cobre as duas pontas.

## Testes no Spring Boot

Testar previne bugs em produção e dá segurança para mexer no código sem medo de quebrar algo que já funcionava. Existem alguns níveis de teste, cada um cobrindo uma fatia diferente da aplicação:

- **Teste unitário** - testa uma classe isolada, geralmente um service, com as dependências mockadas
- **Teste de integração** - testa várias camadas juntas (ex: controller + service + banco real ou em memória)
- **Slice test** - testa só uma fatia específica do Spring (só a camada web, ou só a camada de persistência), sem subir a aplicação inteira
- **Teste end-to-end** - simula o uso real da API do início ao fim, geralmente contra um ambiente parecido com produção

| Anotação          | Uso                                                    |
| ----------------- | ------------------------------------------------------ |
| `@SpringBootTest` | Sobe o contexto completo da aplicação                  |
| `@WebMvcTest`     | Testa só a camada web (controllers), sem subir o resto |
| `@DataJpaTest`    | Testa só a camada JPA, com banco em memória            |
| `@MockBean`       | Cria um mock e registra como bean no contexto Spring   |
| `@Test`           | Marca um método como caso de teste                     |
| `@BeforeEach`     | Roda antes de cada teste                               |
| `@AfterEach`      | Roda depois de cada teste                              |

Um teste de controller com `@WebMvcTest` sobe só a camada web, sem conectar a nenhum banco de verdade. `MockMvc` simula requisições HTTP, e o service real é substituído por um mock via Mockito:

```java
@WebMvcTest(StudentController.class)
class StudentControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private StudentService studentService;

    @Test
    void deveRetornarAlunoPorId() throws Exception {
        when(studentService.getById(1L))
            .thenReturn(new Student(1L, "Ana"));

        mockMvc.perform(get("/students/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Ana"));
    }
}
```

O `when(...).thenReturn(...)` do Mockito define o comportamento do mock, e o `mockMvc.perform(...)` dispara uma requisição fake contra o controller, checando status HTTP e corpo da resposta sem precisar de um servidor rodando de verdade.

## Massa de teste sem ruído

### Fixtures curtas em vez de sequências de add()

Um teste ruim costuma começar com ruído no próprio setup: para validar uma regra simples, o dado de entrada vira uma sequência de `new ArrayList<>()` seguida de vários `add()`, e o teste acaba tendo mais linhas de preparação do que de verificação.

Para uma lista que o teste só vai consultar, sem alterar, `List.of(...)` é direto e já devolve uma lista imutável (o que ainda ajuda a pegar erro cedo, porque `List.of` rejeita `null` na própria construção):

```java
List<Pedido> pedidosDeTeste = List.of(pedidoPendente(), pedidoAprovado());
```

Um detalhe que costuma confundir: `Arrays.asList(...)` parece uma lista comum, mas tem tamanho fixo. `set()` funciona nela, mas `add()` e `remove()` lançam `UnsupportedOperationException`, uma surpresa comum em quem espera uma lista totalmente mutável. Quando o código sob teste precisa alterar a lista de entrada, o caminho mais simples é envolver o `List.of(...)` numa `ArrayList` nova:

```java
List<Pedido> mutavel = new ArrayList<>(List.of(pedidoPendente(), pedidoAprovado()));
```

Para repetir o mesmo valor várias vezes, `Collections.nCopies(10, Pedido.pendente())` monta dez entradas iguais numa linha só (atenção: se o objeto repetido for mutável, todas as posições compartilham a mesma instância). Para uma sequência numérica, útil em testes de paginação ou de IDs sequenciais, `IntStream.rangeClosed(1, n).boxed().toList()` resolve em uma expressão:

```java
List<Integer> ids = IntStream.rangeClosed(1, 50).boxed().toList(); // 1 a 50, lista imutável
```

### Dados realistas com seed determinística

`String` fixa tipo `"João Silva"` funciona para o caso feliz, mas não exercita validação de tamanho, caractere especial ou formato de locale que aparece em produção. Bibliotecas de geração de dados falsos, como o Datafaker, ajudam a criar massa de teste mais próxima da realidade sem escrever essa variação manualmente:

```java
Faker faker = new Faker();
Pedido pedido = new Pedido(faker.name().fullName(), faker.number().randomDouble(2, 10, 500));
```

O detalhe que faz diferença em CI é sempre fornecer uma seed explícita para o gerador. Sem seed, o teste gera um dado diferente a cada execução, e uma falha intermitente vira uma loteria, impossível de reproduzir localmente com o mesmo dado que quebrou no pipeline:

```java
Faker faker = new Faker(new Random(42L)); // sempre gera a mesma sequência de dados
```

Quando o objeto a ser gerado tem um grafo aninhado ou várias coleções, montar tudo manualmente com `Stream.generate` vira boilerplate rápido. Uma biblioteca de geração de grafo de objetos (como o Instancio) resolve a estrutura respeitando os tipos, e pode ser combinada com o gerador de dados realistas só nos campos que realmente importam para o teste.

## JUnit 6 e testes parametrizados de classe

O JUnit 6 trouxe `@ParameterizedClass`, que estende a ideia de `@ParameterizedTest` (parametrizar um único método) para a classe inteira. Antes, rodar toda uma suíte de testes contra vários cenários diferentes exigia repetir a mesma configuração em cada método, ou duplicar a classe de teste inteira.

```java
@ParameterizedClass
@ValueSource(strings = {"PIX", "BOLETO", "CARTAO"})
class ProcessadorDePagamentoTest {

    ProcessadorDePagamentoTest(String metodo) {
        this.metodo = metodo;
    }

    @Test
    void deveProcessarPagamentoValido() { /* roda uma vez para cada valor de "metodo" */ }

    @Test
    void deveRejeitarValorNegativo() { /* idem */ }
}
```

Com isso, a classe inteira roda uma vez para cada parâmetro, injetado via construtor ou campo, reduzindo a duplicação que antes só existia para cobrir múltiplos cenários com a mesma suíte de testes.

## Build e execução

Com o projeto pronto, o Maven cuida de compilar, testar e empacotar:

```bash
mvn clean install    # limpa, compila, roda os testes e instala no repositório local
mvn spring-boot:run  # roda a aplicação direto, sem gerar o JAR
mvn clean package    # gera o JAR executável em target/
java -jar target/minha-api.jar  # roda o JAR gerado
```

`mvn spring-boot:run` é o jeito mais rápido de rodar durante o desenvolvimento. Para gerar o artefato que efetivamente vai para produção, `mvn clean package` cria o JAR em `target/`, e esse JAR já vem com tudo empacotado dentro (servidor embutido incluso) - não precisa instalar um Tomcat separado.

## Deploy com Docker

Empacotar a aplicação numa imagem Docker facilita rodar o mesmo artefato em qualquer ambiente, do laptop do desenvolvedor até o servidor de produção:

```dockerfile
FROM openjdk:17
WORKDIR /app
COPY target/minha-api.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```bash
docker build -t minha-api .
docker run -p 8080:8080 minha-api
```

`docker build` lê o `Dockerfile` e monta a imagem; `docker run -p 8080:8080` sobe um container a partir dela, mapeando a porta 8080 do container para a porta 8080 da sua máquina. A partir daqui, a mesma imagem pode ser publicada num registry (Docker Hub, ECR, GCR) e usada por qualquer orquestrador (Kubernetes, ECS, etc).

## Boas práticas gerais

Uma lista curta que resume boa parte do que já foi visto nas outras notas de Spring, reunida num só lugar:

- Nomes significativos para classes, métodos e variáveis
- Seguir o princípio de responsabilidade única - cada classe faz uma coisa
- Injeção por construtor em vez de injeção por campo
- Usar DTOs, nunca expor a entidade diretamente na API
- Validar toda entrada de dados
- Logar de forma proporcional, sem exagero nem silêncio
- Seguir as convenções REST (verbos HTTP corretos, status code correto)
- Manter as dependências do projeto atualizadas
- Escrever testes para os módulos que realmente importam
- Guardar segredos (senhas, chaves de API) em variáveis de ambiente, nunca direto no `application.properties` versionado
