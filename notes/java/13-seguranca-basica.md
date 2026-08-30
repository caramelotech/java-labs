# Segurança Básica em Java

Spring Security (visto em Spring Security) cuida de autenticação e autorização na camada web. Essa nota é sobre duas decisões que acontecem um nível abaixo disso, na própria linguagem: como lidar com dado sensível na memória e por que confiar cegamente em bytes vindos de fora pode custar caro.

## Criptografia não é lugar para inventar

A primeira regra de criptografia seria engraçada se não fosse tão comum ver violada: não crie seu próprio algoritmo, não "embaralhe" texto como se fosse proteção, não use uma senha simples como chave, e nunca trate Base64 como segurança. Base64 é só uma codificação, transforma bytes em texto legível para transporte, qualquer um consegue reverter sem chave nenhuma.

Duas situações aparecem com frequência em aplicações Java, e pedem tratamento diferente:

**Dado que precisa ser lido de volta depois** (um documento, um token, um campo sensível de cadastro): use uma biblioteca de alto nível já revisada por especialistas, como o Tink do Google, em vez de montar a cifra na mão com `javax.crypto` puro. Quando você precisa de confidencialidade (esconder o conteúdo) e integridade (garantir que ninguém alterou o dado) ao mesmo tempo, o padrão certo é AEAD (Authenticated Encryption with Associated Data). Se você só precisa provar que uma mensagem não foi alterada, sem esconder o conteúdo, MAC (como HMAC) ou uma assinatura digital (quando a verificação precisa acontecer com uma chave pública, sem expor a chave privada) resolvem sem o custo extra de cifrar.

**Senha de usuário**: nunca use criptografia reversível. Uma senha não deveria poder ser "descriptografada" por ninguém, nem pelo próprio sistema. O caminho certo é hash adaptativo, não um hash rápido comum:

```java
// hash rápido (SHA-256, MD5) NÃO serve para senha, mesmo com salt
// é rápido demais: trivial de paralelizar em GPU e testar bilhões de combinações
String hashRuim = sha256(senha + salt);

// hash adaptativo: BCrypt, deliberadamente lento, com salt embutido
PasswordEncoder encoder = new BCryptPasswordEncoder();
String hash = encoder.encode(senhaDigitada);
boolean confere = encoder.matches(senhaDigitada, hash);
```

`BCryptPasswordEncoder` (do Spring Security) gera o salt automaticamente e aplica o algoritmo bcrypt, que é deliberadamente custoso de calcular, o que torna ataques de força bruta muito mais caros mesmo com hardware dedicado. O login nunca "descriptografa" a senha armazenada, ele recalcula o hash da senha digitada e compara os dois hashes.

Regra prática: precisa ler o dado de volta depois? AEAD. Só precisa provar que não foi alterado? MAC ou assinatura. É senha de usuário? Hash adaptativo, nunca texto plano, nunca reversível.

## Desserialização não é a mesma coisa que parsing

A serialização nativa do Java (`ObjectInputStream`/`ObjectOutputStream`) reconstrói objetos Java inteiros a partir de uma sequência de bytes. O risco central é tratar esses bytes como dado inofensivo quando eles vêm de uma fonte não confiável.

`ObjectInputStream` pode carregar qualquer classe presente no classpath da aplicação e acionar métodos como `readObject()` durante a reconstrução. Isso significa que bytes maliciosos, desenhados especificamente para o classpath daquela aplicação, podem encadear chamadas de métodos já existentes em bibliotecas legítimas (uma técnica chamada gadget chain) até conseguir executar código arbitrário no servidor, sem nunca ter explorado uma vulnerabilidade de memória, só a própria confiança do mecanismo de desserialização.

```java
// nunca desserialize bytes vindos de uma fonte não confiável assim
ObjectInputStream in = new ObjectInputStream(entradaDaRede);
Object objeto = in.readObject(); // pode instanciar QUALQUER classe do classpath
```

A defesa mais sólida é simplesmente não usar serialização nativa do Java para dado que atravessa uma fronteira não confiável (rede, fila, upload de arquivo). Formatos com schema explícito, como Protocol Buffers ou JSON com um contrato de tipo bem definido, resolvem melhor: o schema já define a estrutura esperada, e o payload não carrega nome de classe Java nenhum, então o receptor só lê dado, nunca é induzido a instanciar um tipo arbitrário.

Dado sensível (senha, token, chave, informação de sessão) não deveria fazer parte do que é serializado de forma alguma. Marcar um campo como `transient` remove ele da serialização automática:

```java
class Sessao implements Serializable {
    private String usuarioId;
    private transient String tokenSecreto; // nunca vai para o stream serializado
}
```

Quando a serialização nativa for realmente inevitável (integração com um sistema legado, por exemplo), `ObjectInputFilter` permite configurar uma allowlist explícita de classes aceitas, além de limites de profundidade e de número de referências, o que reduz bastante o espaço de ataque disponível para um payload malicioso. Vale reforçar: prefira sempre evitar o problema (schema explícito) a mitigar o problema (allowlist).
