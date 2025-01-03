
# JAVA (Spring/Maven)

## Comandos no VS Code

- **`CTRL + SHIFT + P`**: Abre um campo de pesquisa no VS Code. Use `>` para consultar todas as informações disponíveis.

---

## Criando um Projeto com Spring Initializr

1. Abra a pesquisa com `CTRL + SHIFT + P` e digite `maven`.
2. Selecione **`Spring Initializr: Create a Maven Project`**.
3. Defina:
   - Versão do Spring Boot.
   - Linguagem.
   - Grupo e artefato.
   - Versão do Java.
4. Escolha as dependências iniciais (podem ser adicionadas posteriormente).

### Dependências Iniciais

- **Spring Web**: Criação de APIs RESTful.
- **Spring Devtools**: Reload automático durante o desenvolvimento.
- **H2 Database**: Banco de dados em memória para testes.
- **JPA**: Mapeamento ORM com bancos relacionais.
- **PostgreSQL**: Banco relacional PostgreSQL.

---

## Utilizando Docker com Banco Relacional (Postgres)

1. Crie o arquivo `docker-compose.yml`.
2. Configure o arquivo conforme o exemplo abaixo:

```yml
# ====================================================================================================================
# POSTGRES SERVER
# ====================================================================================================================
services:
  postgres:
    container_name: gestao_meca_postgres
    image: postgres
    ports:
      - 5434:5432
    environment:
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=admin
      - POSTGRES_DB=gestao_meca
    networks:
      - dev-network

# ====================================================================================================================
# PGADMIN
# ====================================================================================================================
  pgadmin-docker:
    image: dpage/pgadmin4
    container_name: dev-pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: me@example.com
      PGADMIN_DEFAULT_PASSWORD: 1234567
    ports:
      - 5050:80
    volumes:
      - ./.data/pgadmin:/var/lib/pgadmin
    depends_on:
      - postgres
    networks:
      - dev-network

# ====================================================================================================================
# REDE
# ====================================================================================================================
networks:
  dev-network:
    driver: bridge
```

---

## Criando Pacotes

### Estrutura de Pacotes

1. **Estrutura com pacotes separados**:

```
nomeProjeto
  > pacotePrincipal
    - application.java
    > pacoteController
    > pacoteDTO
```

2. **Estrutura com pacotes dentro do pacote principal**:

```
nomeProjeto
  > pacotePrincipal
    @ComponentScan(basePackages = "br.com.nomeProjeto")
    - application.java
    > pacoteController
    > pacoteDTO
```

---

## Criando Classe para Teste

Utilizando **Records** para simplificar a criação de classes de teste sem a necessidade de getters/setters:

```java
@PostMapping("/metodoBodyParams")
public String metodoBodyParams(@RequestBody Usuario username){
    return "metodoBodyParams " + username;
}

record Usuario(String username){}
```

---

## Executando a Aplicação

1. Abra o terminal e execute o comando `mvn spring-boot:run`.
2. Caso as extensões do Spring no VS Code estejam instaladas, um botão **run** aparecerá sobre a aplicação principal.

---

## Annotations

- **`@SpringBootApplication`**: Define a classe principal da aplicação.
- **`@RequestMapping`**: Mapeia uma rota, ex: `@RequestMapping("/primeiraController")`.
- **`@RestController`**: Define a classe como uma controller que processa as requisições.
- **`@GetMapping`**: Define o tipo de requisição GET, ex: `@GetMapping("/primeiroMetodo")`.
- **`@ComponentScan(basePackages = "br.com.nomeProjeto")`**: Realiza a varredura no projeto para detectar beans fora do pacote principal.
- **`@Autowired`**: Faz a injeção de dependência do Spring.
- **`@ControllerAdvice`**: Define uma classe global para tratamento de exceções.
- **`@ExceptionHandler(MethodArgumentNotValidException.class)`**: Captura exceções específicas.
- **`@Id`**: Marca a variável como chave primária.
- **`@GeneratedValue(strategy = GenerationType.UUID)`**: Gera um UUID para a chave primária.
- **`@GeneratedValue(strategy = GenerationType.IDENTITY)`**: Gera um ID sequencial.

---

## API REST

### Tipos de Requisição e Parâmetros

- **HTTP Methods**: GET, POST, PUT, DELETE.
- **Tipos de Retorno**: XML, JSON.
- **Tipos de Parâmetros**:
  - **Body Params**: Parâmetros enviados no corpo da requisição.
  - **Path Params**: Parâmetros passados na URL, ex: `https://dominio.com.br/java/pathParams/{id}`.
  - **Query Params**: Parâmetros passados após `?`, ex: `?param1=value1&param2=value2`.
  - **Header Params**: Parâmetros enviados no cabeçalho da requisição.

### Exemplos de Parâmetros

- **Path Params**:

```java
@GetMapping("/primeiroMetodo/{id}")
public String primeiroMetodo(@PathVariable String id){
    return "O parametro é " + id;
}
```

- **Query Params**:

```java
@GetMapping("/metodoQueryParams")
public String metodoQueryParams(@RequestParam String id){
    return "O parametro é " + id;
}

@GetMapping("/metodoQueryParamsAllParams")
public String metodoQueryParamsAllParams(@RequestParam Map<String, String> allParams){
    return "Os parâmetros são: " + allParams.entrySet();
}
```

- **Body Params**:

```java
@PostMapping("/metodoBodyParams")
public String metodoBodyParams(@RequestBody String username){
    return "metodoBodyParams " + username;
}

// Corpo da requisição
{
    "username": "Bruno"
}
```

- **Headers**:

```java
@PostMapping("/metodoHeaders")
public String metodoHeaders(@RequestHeader("name") String name){
    return "metodoHeaders " + name;
}

@PostMapping("/metodoListHeaders")
public String metodoListHeaders(@RequestHeader Map<String, String> headers){
    return "metodoListHeaders " + headers.entrySet();
}
```

---

## Retorno com Response Entity

```java
@GetMapping("/metodoResponseEntity")
public ResponseEntity<Object> metodoResponseEntity(){
    var usuario = new Usuario("brunosilva");
    return ResponseEntity.status(HttpStatus.CREATED).body("Mensagem de erro");
}
```

---

## Injeção de Dependências (IoC)

O Spring gerencia os beans automaticamente utilizando a annotation `@Autowired`.

---

## Tratamento de Exceções

### Criando uma Classe de Exceção Global

```java
@ControllerAdvice
public class ExceptionHandlerController {

    private MessageSource messageSource;

    public ExceptionHandlerController(MessageSource message) {
        this.messageSource = message;
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<List<ErrorMessageDTO>> handleMethodArgumentNotValidException(
            MethodArgumentNotValidException e) {
        List<ErrorMessageDTO> dto = new ArrayList<>();
        e.getBindingResult().getFieldErrors().forEach(err -> {
            String message = messageSource.getMessage(err, LocaleContextHolder.getLocale());
            dto.add(new ErrorMessageDTO(message, err.getField()));
        });
        return new ResponseEntity<>(dto, HttpStatus.BAD_REQUEST);
    }
}
```

---

## Repositórios (Repositories)

- Um repositório é uma interface que extende **JpaRepository** para realizar operações CRUD.

```java
public interface GameRepository extends JpaRepository<GameEntity, Long> {
}

public interface UserRepository extends JpaRepository<UserEntity, UUID> {
}
```

---

## Criando Validações

As validações evitam duplicação de dados. Exemplo com `Optional`:

```java
public interface UserRepository extends JpaRepository<UserEntity, UUID> {
    Optional<UserEntity> findByUsernameOrEmail(String username, String email);
}
```

---

## Controllers (GET/POST/DELETE/PUT)

Exemplo de **Controller**:

```java
@RestController
@RequestMapping("/game")
public class GameController {

    @Autowired
    private GameRepository gameRepository;

    @PostMapping("/")
    public List<GameEntity> create(@RequestBody List<GameEntity> gameEntities) {
        System.out.println("Recebido: " + gameEntities);
        gameEntities.forEach(gameEntity -> {
            this.gameRepository.findByTitle(gameEntity.getTitle())
                .ifPresent((game) -> {
                    throw new GameFoundException();
                });
            this.gameRepository.save(gameEntity);
        });
        return gameEntities;
    }
}
```

---

## UseCase

**UseCase** centraliza operações simples e é utilizado em conjunto com a **Controller**.

Exemplo de **UserController** e **UseCase**:

```java
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private CreateUserUseCase createUserUseCase;

    @PostMapping("/")
    public ResponseEntity<Object> create(@Valid @RequestBody UserEntity userEntity) {
        try {
            var result = this.createUserUseCase.execute(userEntity);
            return ResponseEntity.ok().body(result);
        } catch (Exception e) {
            return ResponseEntity.badRequest().body(e.getMessage());
        }
    }
}

@Service
public class CreateUserUseCase {

    @Autowired
    private UserRepository userRepository;

    public UserEntity execute(UserEntity userEntity) {
        this.userRepository.findByUsernameOrEmail(userEntity.getUsername(), userEntity.getEmail())
            .ifPresent((user) -> {
                throw new UserFoundException();
            });
        return this.userRepository.save(userEntity);
    }
}
```

---

## Logs (SLF4J)

- Para registrar logs, use o `LoggerFactory.getLogger(Classe.class)`.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class GameController {
    private static final Logger logger = LoggerFactory.getLogger(GameController.class);

    public void someMethod() {
        logger.info("This is an info log");
        logger.error("This is an error log");
    }
}
```
