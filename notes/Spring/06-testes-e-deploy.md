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
