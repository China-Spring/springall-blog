[TOC]

Spring Boot使用固定算法来扫描和配置[DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html)。这使我们可以在默认情况下轻松获得完全配置的DataSource实现。

Spring Boot还会按顺序快速的自动配置连接池（HikariCP，  Apache Tomcat或Commons DBCP），具体取决于路径中的哪些类。

虽然Spring Boot的DataSource自动配置在大多数情况下运行良好，但有时我们需要更高级别的控制，因此我们必须设置自己的DataSource实现，因此忽略自动配置过程。

#### Maven依赖

---

**总体而言，以编程方式创建DataSource实现非常简单。**

为了学习如何实现这一目标，我们将实现一个简单的存储库层，它将对某些JPA实体执行CRUD操作。

我们来看看我们的演示项目的依赖项：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>2.4.1</version> 
    <scope>runtime</scope> 
</dependency>
```

我们将使用内存中的[H2数据库](https://search.maven.org/search?q=g:org.hsqldb%20AND%20a:hsqldb "H2数据库")实例来运行存储库层。通过这样做，我们将能够测试以编程方式配置的DataSource，而无需执行昂贵的数据库操作。

让我们确保在Maven Central上查看最新版本的spring-boot-starter-data-jpa。

#### 配置DataSource

---

如果我们坚持使用Spring Boot的DataSource自动配置并以当前状态运行我们的项目，程序将按预期工作。

**Spring Boot将为我们完成所有重型基础设施管道。**这包括创建H2 DataSource实现，该实现将由HikariCP，Apache Tomcat或Commons DBCP自动处理，并设置内存数据库实例。

此外，我们甚至不需要创建application.properties文件，因为Spring Boot也会提供一些默认的数据库设置。

正如我们之前提到的，有时我们需要更高级别的自定义，因此我们必须以编程方式配置我们自己的DataSource实现。

**实现此目的的最简单方法是定义DataSource工厂方法，并将其放在使用[@Configuration](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html "@Configuration")注解的类中：**

```java
@Configuration
public class DataSourceConfig {
     
    @Bean
    public DataSource getDataSource() {
        DataSourceBuilder dataSourceBuilder = DataSourceBuilder.create();
        dataSourceBuilder.driverClassName("org.h2.Driver");
        dataSourceBuilder.url("jdbc:h2:mem:test");
        dataSourceBuilder.username("SA");
        dataSourceBuilder.password("");
        return dataSourceBuilder.build();
    }
}
```

在这种情况下，我们使用方便的[DataSourceBuilder](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/jdbc/DataSourceBuilder.html "DataSourceBuilder")类  - 一个简洁的[Joshua Bloch构建器](https://www.pearson.com/us/higher-education/program/Bloch-Effective-Java-3rd-Edition/PGM1763855.html "Joshua Bloch构建器")模式  -  以编程方式创建我们的自定义DataSource对象。

这种方法非常好，因为构建器可以使用一些常用属性轻松配置DataSource。此外，它还可以使用底层连接池。

#### 使用application.properties文件外部化DataSource配置

---

当然，也可以部分外部化我们的DataSource配置。例如，我们可以在工厂方法中定义一些基本的DataSource属性：

```java
@Bean
public DataSource getDataSource() { 
    DataSourceBuilder dataSourceBuilder = DataSourceBuilder.create(); 
    dataSourceBuilder.username("SA"); 
    dataSourceBuilder.password(""); 
    return dataSourceBuilder.build(); 
}
```

并在application.properties文件中指定一些额外的配置：

```java
spring.datasource.url=jdbc:h2:mem:test
spring.datasource.driver-class-name=org.h2.Driver
```

在外部源中定义的属性（例如上面的application.properties文件或通过使用[@ConfigurationProperties](https://www.baeldung.com/configuration-properties-in-spring-boot "@ConfigurationProperties")注解的类）将覆盖Java API中定义的属性。

很明显，通过这种方法，我们不再将DataSource配置设置保存在一个地方。

另一方面，它允许我们保持编译时和运行时配置彼此并很好地分离。

这非常好，因为它允许我们轻松设置绑定点。这样我们可以从其他来源包含不同的DataSource，而无需重构我们的bean工厂方法。

#### 测试DataSource配置

---

测试我们的自定义DataSource配置非常简单。整个过程归结为创建JPA实体，定义基本存储库接口以及测试存储库层。

- 创建JPA实体

让我们开始定义我们的示例JPA实体类，它将为用户建模：

```java
@Entity
@Table(name = "users")
public class User {
     
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;
    private String name;
    private String email;
 
    // standard constructors / setters / getters / toString
     
}
```

- 存储库层

我们需要实现一个基本的存储库层，它允许我们对上面定义的User实体类的实例执行CRUD操作。

由于我们使用的是Spring Data JPA，因此我们不必从头开始创建自己的DAO实现。我们只需要扩展[CrudRepository接口](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html "CrudRepository接口")获得一个工作的存储库实现：

```java
@Repository
public interface UserRepository extends CrudRepository<User, Long> {}
```

- 测试存储库层

最后，我们需要检查我们的编程配置的DataSource是否实际工作。我们可以通过集成测试轻松完成此任务：

```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class UserRepositoryIntegrationTest {
     
    @Autowired
    private UserRepository userRepository;
    
    @Test
    public void whenCalledSave_thenCorrectNumberOfUsers() {
        userRepository.save(new User("Bob", "bob@domain.com"));
        List<User> users = (List<User>) userRepository.findAll();
         
        assertThat(users.size()).isEqualTo(1);
    }    
}
```

UserRepositoryIntegrationTest类是测试用例。它只是运行两个存储库接口的CRUD方法来持久化并查找实体。

**请注意，无论我们是否决定以编程方式配置DataSource实现，或将其拆分为Java配置方法和application.properties文件，我们都应该始终获得有效的数据库连接。**

- 运行示例应用程序

最后，我们可以使用标准的main()方法运行我们的演示应用程序：

```java
@SpringBootApplication
public class Application {
 
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
 
    @Bean
    public CommandLineRunner run(UserRepository userRepository) throws Exception {
        return (String[] args) -> {
            User user1 = new User("John", "john@domain.com");
            User user2 = new User("Julie", "julie@domain.com");
            userRepository.save(user1);
            userRepository.save(user2);
            userRepository.findAll().forEach(user -> System.out.println(user);
        };
    }
}
```

我们已经测试了存储库层，因此我们确信我们的DataSource已经成功配置。因此，如果我们运行示例应用程序，我们应该在控制台输出中看到存储在数据库中的User实体列表。
