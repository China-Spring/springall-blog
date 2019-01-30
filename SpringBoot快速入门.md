Spring Boot是对Spring平台的一种过于注重常规配置的添加——对于以最少的工作量开始并创建独立的、产品级的应用程序非常有用。

本文我们将介绍一些核心配置，前端，快速数据操作和异常处理。

> 设置

首先，让我们使用[Spring Initializr](https://start.spring.io/)为我们的项目生成基础。

生成的项目依赖于Boot父级：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.1.RELEASE</version>
    <relativePath />
</parent>
```

最初的依赖关系非常简单：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>
```

> 应用程序配置

接下来，我们将为我们的应用程序配置一个简单的主类：

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

注意我们如何使用@SpringBootApplication作为我们的主要应用程序配置类; 在幕后，这相当于@Configuration，@EnableAutoConfiguration和@ComponentScan在一起。

最后，我们将定义一个简单的application.properties文件 - 现在只有一个属性：

```
server.port=8081
```

server.port将服务器端口从默认的8080更改为8081; 当然还有更多[Spring Boot可用属性](https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html)。

> 简单的MVC视图

现在让我们使用Thymeleaf添加一个简单的前端。

首先，我们需要将spring-boot-starter-thymeleaf依赖项添加到我们的pom.xml中：

```xml
<dependency> 
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring-boot-starter-thymeleaf</artifactId> 
</dependency>
```

这样就可以默认启用Thymeleaf - 无需额外配置。

我们现在可以在application.properties中配置它：

```
spring.thymeleaf.cache=false
spring.thymeleaf.enabled=true
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
 
spring.application.name=Bootstrap Spring Boot
```

接下来，我们将定义一个简单的控制器和一个基本主页 - 带有欢迎消息：

```java
@Controller
public class SimpleController {
    @Value("${spring.application.name}")
    String appName;
 
    @GetMapping("/")
    public String homePage(Model model) {
        model.addAttribute("appName", appName);
        return "home";
    }
}
```

最后，这是我们的home.html：

```html
<html>
<head><title>Home Page</title></head>
<body>
<h1>Hello !</h1>
<p>Welcome to <span th:text="${appName}">Our App</span></p>
</body>
</html>
```

注意我们如何使用我们在属性中定义的属性 - 然后注入它以便我们可以在主页上显示它。

> 安全

接下来，让我们为我们的应用程序添加安全性 - 首先包括安全启动器：

```xml
<dependency> 
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring-boot-starter-security</artifactId> 
</dependency>
```

到目前为止，您希望注意到一种模式 - **大多数Spring库都可以使用简单的Boot启动程序轻松导入到我们的项目中**。

一旦spring-boot-starter-security依赖于应用程序的类路径 - 默认情况下所有端点都是安全的，使用基于Spring Security内容协商策略的httpBasic或formLogin。

这就是为什么，如果我们在类路径上有启动器，我们通常应该通过扩展WebSecurityConfigurerAdapter类来定义我们自己的自定义安全配置：

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
 
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .anyRequest()
            .permitAll()
            .and().csrf().disable();
    }
}
```

在我们的示例中，我们允许不受限制地访问所有端点。

当然，Spring Security是一个广泛的主题，并且在几行配置中不容易涵盖 - 所以我绝对鼓励你深入研究[Spring整合Security](http://www.springall.com.cn/article/136)。

> 简单的持久性

让我们从定义我们的数据模型开始 - 一个简单的Book实体：

```java
@Entity
public class Book {
  
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;
 
    @Column(nullable = false, unique = true)
    private String title;
 
    @Column(nullable = false)
    private String author;
}
```

和它的存储库，在这里充分利用Spring Data：

```java
public interface BookRepository extends CrudRepository<Book, Long> {
    List<Book> findByTitle(String title);
}
```

最后，我们当然需要配置新的持久层：

```java
@EnableJpaRepositories("org.baeldung.persistence.repo") 
@EntityScan("org.baeldung.persistence.model")
@SpringBootApplication
public class Application {
   ...
}
```

请注意，我们正在使用：

- @EnableJpaRepositories扫描指定的包以获取存储库
- @EntityScan可以获取我们的JPA实体

为了简单起见，我们在这里使用H2内存数据库 - 这样我们在运行项目时就没有任何外部依赖关系了。

一旦我们包含H2依赖关系，Spring Boot会自动检测它并设置我们的持久性，而不需要额外的配置，除了数据源属性：

```
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.url=jdbc:h2:mem:bootapp;DB_CLOSE_DELAY=-1
spring.datasource.username=sa
spring.datasource.password=
```

当然，就像安全性一样，持久性是一个比这里基本设置更广泛的话题，你应该查看[Spring Persistence](http://www.springall.com.cn/article/137)。

> 网络和控制器

接下来，让我们看一下Web层 - 我们将通过设置一个简单的控制器 - BookController来启动它。

我们将实现基本的CRUD操作，通过一些简单的验证来公开Book资源：

```java
@RestController
@RequestMapping("/api/books")
public class BookController {
 
    @Autowired
    private BookRepository bookRepository;
 
    @GetMapping
    public Iterable findAll() {
        return bookRepository.findAll();
    }
 
    @GetMapping("/title/{bookTitle}")
    public List findByTitle(@PathVariable String bookTitle) {
        return bookRepository.findByTitle(bookTitle);
    }
 
    @GetMapping("/{id}")
    public Book findOne(@PathVariable Long id) {
        return bookRepository.findById(id)
          .orElseThrow(BookNotFoundException::new);
    }
 
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public Book create(@RequestBody Book book) {
        return bookRepository.save(book);
    }
 
    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) {
        bookRepository.findById(id)
          .orElseThrow(BookNotFoundException::new);
        bookRepository.deleteById(id);
    }
 
    @PutMapping("/{id}")
    public Book updateBook(@RequestBody Book book, @PathVariable Long id) {
        if (book.getId() != id) {
          throw new BookIdMismatchException();
        }
        bookRepository.findById(id)
          .orElseThrow(BookNotFoundException::new);
        return bookRepository.save(book);
    }
}
```

鉴于应用程序的这一方面是一个API，我们在这里使用@RestController注释 - 它等同于@Controller和@ResponseBody - 以便每个方法将返回的资源编组到HTTP响应中。

只有一个值得指出的注意事项 - 我们在这里公开我们的Book实体作为我们的外部资源。这对我们这里的简单应用程序来说很好，但在实际应用程序中，您可能希望[Spring REST API实体到DTO的转换](http://www.springall.com.cn/article/49)

> 错误处理

现在核心应用程序已准备就绪，让我们专注于使用@ControllerAdvice 的简单集中式错误处理机制：

```java
@ControllerAdvice
public class RestExceptionHandler extends ResponseEntityExceptionHandler {
 
    @ExceptionHandler({ BookNotFoundException.class })
    protected ResponseEntity<Object> handleNotFound(
      Exception ex, WebRequest request) {
        return handleExceptionInternal(ex, "Book not found", 
          new HttpHeaders(), HttpStatus.NOT_FOUND, request);
    }
 
    @ExceptionHandler({ BookIdMismatchException.class, 
      ConstraintViolationException.class, 
      DataIntegrityViolationException.class })
    public ResponseEntity<Object> handleBadRequest(
      Exception ex, WebRequest request) {
        return handleExceptionInternal(ex, ex.getLocalizedMessage(), 
          new HttpHeaders(), HttpStatus.BAD_REQUEST, request);
    }
}
```

除了我们在这里处理的标准异常之外，我们还使用了一个自定义异常：

```java
public class BookNotFoundException extends RuntimeException {
 
    public BookNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }
    // ...
}
```

这应该让您了解这种全局异常处理机制的可能性。如果您希望看到完整的实现，请查看[Spring REST中的错误处理](http://www.springall.com.cn/article/48)

请注意，默认情况下，Spring Boot还提供/错误映射。我们可以通过创建一个简单的error.html来自定义其视图：

```html
<html lang="en">
<head><title>Error Occurred</title></head>
<body>
    <h1>Error Occurred!</h1>    
    <b>[<span th:text="${status}">status</span>]
        <span th:text="${error}">error</span>
    </b>
    <p th:text="${message}">message</p>
</body>
</html>
```

与Boot中的大多数其他方面一样，我们可以使用简单的属性来控制它：

```
server.error.path=/error2
```

> 测试

最后，让我们测试一下我们的新Books API。

我们将立即使用@SpringBootTest加载应用程序上下文：

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = { Application.class }, webEnvironment 
  = WebEnvironment.DEFINED_PORT)
public class LiveTest {
 
    private static final String API_ROOT
      = "http://localhost:8081/api/books";
 
    private Book createRandomBook() {
        Book book = new Book();
        book.setTitle(randomAlphabetic(10));
        book.setAuthor(randomAlphabetic(15));
        return book;
    }
 
    private String createBookAsUri(Book book) {
        Response response = RestAssured.given()
          .contentType(MediaType.APPLICATION_JSON_VALUE)
          .body(book)
          .post(API_ROOT);
        return API_ROOT + "/" + response.jsonPath().get("id");
    }
}
```

首先，我们可以尝试使用变体方法查找书籍：

```java
@Test
public void whenGetAllBooks_thenOK() {
    Response response = RestAssured.get(API_ROOT);
  
    assertEquals(HttpStatus.OK.value(), response.getStatusCode());
}
 
@Test
public void whenGetBooksByTitle_thenOK() {
    Book book = createRandomBook();
    createBookAsUri(book);
    Response response = RestAssured.get(
      API_ROOT + "/title/" + book.getTitle());
     
    assertEquals(HttpStatus.OK.value(), response.getStatusCode());
    assertTrue(response.as(List.class)
      .size() > 0);
}
@Test
public void whenGetCreatedBookById_thenOK() {
    Book book = createRandomBook();
    String location = createBookAsUri(book);
    Response response = RestAssured.get(location);
     
    assertEquals(HttpStatus.OK.value(), response.getStatusCode());
    assertEquals(book.getTitle(), response.jsonPath()
      .get("title"));
}
 
@Test
public void whenGetNotExistBookById_thenNotFound() {
    Response response = RestAssured.get(API_ROOT + "/" + randomNumeric(4));
     
    assertEquals(HttpStatus.NOT_FOUND.value(), response.getStatusCode());
}
```

接下来，我们将测试创建一本新书：

```java
@Test
public void whenCreateNewBook_thenCreated() {
    Book book = createRandomBook();
    Response response = RestAssured.given()
      .contentType(MediaType.APPLICATION_JSON_VALUE)
      .body(book)
      .post(API_ROOT);
     
    assertEquals(HttpStatus.CREATED.value(), response.getStatusCode());
}
 
@Test
public void whenInvalidBook_thenError() {
    Book book = createRandomBook();
    book.setAuthor(null);
    Response response = RestAssured.given()
      .contentType(MediaType.APPLICATION_JSON_VALUE)
      .body(book)
      .post(API_ROOT);
     
    assertEquals(HttpStatus.BAD_REQUEST.value(), response.getStatusCode());
}
```

更新现有图书：

```java
@Test
public void whenUpdateCreatedBook_thenUpdated() {
    Book book = createRandomBook();
    String location = createBookAsUri(book);
    book.setId(Long.parseLong(location.split("api/books/")[1]));
    book.setAuthor("newAuthor");
    Response response = RestAssured.given()
      .contentType(MediaType.APPLICATION_JSON_VALUE)
      .body(book)
      .put(location);
     
    assertEquals(HttpStatus.OK.value(), response.getStatusCode());
 
    response = RestAssured.get(location);
     
    assertEquals(HttpStatus.OK.value(), response.getStatusCode());
    assertEquals("newAuthor", response.jsonPath()
      .get("author"));
}
```

删除一本书：

```java
@Test
public void whenDeleteCreatedBook_thenOk() {
    Book book = createRandomBook();
    String location = createBookAsUri(book);
    Response response = RestAssured.delete(location);
     
    assertEquals(HttpStatus.OK.value(), response.getStatusCode());
 
    response = RestAssured.get(location);
    assertEquals(HttpStatus.NOT_FOUND.value(), response.getStatusCode());
}
```

> 案例结论

这是Spring Boot的快速而全面的介绍。

Spring Boot内容很多更多文章会陆续推出。

