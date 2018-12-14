本指南将引导您完成使用Spring访问关系数据的过程。

> 你要构建什么

您将使用Spring构建一个**JdbcTemplate**应用程序来访问存储在关系数据库中的数据。

> 你需要什么

- 大约15分钟
- 最喜欢的文本编辑器或IDE
- [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html)或更高版本
- [Gradle 4+](http://www.gradle.org/downloads)或[Maven 3.2+](https://maven.apache.org/download.cgi)
- 您还可以将代码直接导入IDE：

    - [使用STS构建/导入入门指南](https://www.springall.com.cn/spring/shi_yong_sts_gou_jian_dao_ru_ru_men_zhi_nan.html)
    - [使用IntelliJ IDEA导入入门指南](https://www.springall.com.cn/spring/shi_yong_intellij_idea_dao_ru_ru_men_zhi_nan.html)

> 如何完成本指南

与大多数[Spring入门指南](https://www.springall.com.cn/zhuan_lan/spring_ru_men_zhi_nan)一样，您可以从头开始并完成每个步骤，或者您可以绕过您已熟悉的基本设置步骤。无论哪种方式，您最终都会使用工作代码。

要**从头开始**，请继续使用Gradle构建。

要**跳过基础知识**，请执行以下操作：

- [下载](https://github.com/spring-guides/gs-relational-data-access/archive/master.zip)并解压缩本指南的源存储库，或使用Git克隆它：

```bash
git clone https://github.com/spring-guides/gs-relational-data-access.git
```

- 进入**gs-relational-data-access/initial**
- 跳转到创建Customer对象。

**完成后**，可以根据**gs-relational-data-access/complete**中的代码检查结果。

### 使用Gradle构建

首先，设置一个基本的构建脚本。在使用Spring构建应用程序时，您可以使用任何您喜欢的构建系统，但此处包含了使用[Gradle](http://gradle.org/)和[Maven](https://maven.apache.org/)所需的代码。如果您不熟悉这两者，请参阅[使用Gradle构建Java项目](hhttps://www.springall.com.cn/java/shi_yong_gradle_gou_jian_java_xiang_mu.html)或[使用Maven构建Java项目](https://www.springall.com.cn/java/shi_yong_maven_gou_jian_java_xiang_mu.html)。

> 创建目录结构

在您选择的项目目录中，创建以下子目录结构;例如，在\*nix系统上使用**mkdir -p src/main/java/hello**:

```bash
└── src
    └── main
        └── java
            └── hello
```

> 创建Gradle构建文件

下面是[最初的Gradle构建文件](https://github.com/spring-guides/gs-relational-data-access/blob/master/initial/build.gradle)。

```bash
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:2.0.3.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
    baseName = 'gs-relational-data-access'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter")
    compile("org.springframework:spring-jdbc")
    compile("com.h2database:h2")
    testCompile("junit:junit")
}
```

在[Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html)提供了许多便捷的功能：

- 它收集类路径上的所有jar并构建一个可运行的“über-jar”，这使得执行和传输服务更加方便。
- 它搜索**public static void main()**标记为可运行类的方法。
- 它提供了一个内置的依赖项解析器，它设置版本号以匹配[Spring Boot依赖项](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)。您可以覆盖任何您希望的版本，但它将默认为Boot的所选版本集。

### 使用Maven构建

首先，设置一个基本的构建脚本。在使用Spring构建应用程序时，您可以使用任何您喜欢的构建系统，但此处包含了使用[Maven](https://maven.apache.org/)所需的代码。如果您不熟悉Maven，请参阅[使用Maven构建Java项目](https://www.springall.com.cn/java/shi_yong_maven_gou_jian_java_xiang_mu.html)。

> 创建目录结构

在您选择的项目目录中，创建以下子目录结构;例如，在\*nix系统上使用**mkdir -p src/main/java/hello**:

```bash
└── src
    └── main
        └── java
            └── hello
```

**pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-relational-data-access</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.3.RELEASE</version>
    </parent>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
        </dependency>
    </dependencies>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

在[Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html)提供了许多便捷的功能：

- 它收集类路径上的所有jar并构建一个可运行的“über-jar”，这使得执行和传输服务更加方便。
- 它搜索**public static void main()**标记为可运行类的方法。
- 它提供了一个内置的依赖项解析器，它设置版本号以匹配[Spring Boot依赖项](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)。您可以覆盖任何您希望的版本，但它将默认为Boot的所选版本集。

### 使用IDE构建

- 阅读如何将本指南直接导入[使用STS构建/导入入门指南](https://www.springall.com.cn/spring/shi_yong_sts_gou_jian_dao_ru_ru_men_zhi_nan.html)。
- 阅读[使用IntelliJ IDEA导入入门指南](https://www.springall.com.cn/spring/shi_yong_intellij_idea_dao_ru_ru_men_zhi_nan.html)中如何使用本指南。

> 创建一个Customer对象

您将在下面使用的简单数据访问逻辑管理客户的名字和姓氏。要在应用程序级别表示此数据，请创建一个Customer类。

```java
package hello;

public class Customer {
    private long id;
    private String firstName, lastName;

    public Customer(long id, String firstName, String lastName) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
    }

    @Override
    public String toString() {
        return String.format(
                "Customer[id=%d, firstName='%s', lastName='%s']",
                id, firstName, lastName);
    }

    // getters & setters omitted for brevity
}
```

> 存储和检索数据

Spring提供了一个名为**JdbcTemplate**的模板类，可以轻松使用SQL关系数据库和JDBC。大多数JDBC代码都陷入资源获取，连接管理，异常处理和一般错误检查之中，这与代码要实现的内容完全无关,JdbcTemplate负责这一切。您所要做的就是专注于手头的任务。

**src/main/java/hello/Application.java**

```java
package hello;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.jdbc.core.JdbcTemplate;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

@SpringBootApplication
public class Application implements CommandLineRunner {

    private static final Logger log = LoggerFactory.getLogger(Application.class);

    public static void main(String args[]) {
        SpringApplication.run(Application.class, args);
    }

    @Autowired
    JdbcTemplate jdbcTemplate;

    @Override
    public void run(String... strings) throws Exception {

        log.info("Creating tables");

        jdbcTemplate.execute("DROP TABLE customers IF EXISTS");
        jdbcTemplate.execute("CREATE TABLE customers(" +
                "id SERIAL, first_name VARCHAR(255), last_name VARCHAR(255))");

        // Split up the array of whole names into an array of first/last names
        List<Object[]> splitUpNames = Arrays.asList("John Woo", "Jeff Dean", "Josh Bloch", "Josh Long").stream()
                .map(name -> name.split(" "))
                .collect(Collectors.toList());

        // Use a Java 8 stream to print out each tuple of the list
        splitUpNames.forEach(name -> log.info(String.format("Inserting customer record for %s %s", name[0], name[1])));

        // Uses JdbcTemplate's batchUpdate operation to bulk load data
        jdbcTemplate.batchUpdate("INSERT INTO customers(first_name, last_name) VALUES (?,?)", splitUpNames);

        log.info("Querying for customer records where first_name = 'Josh':");
        jdbcTemplate.query(
                "SELECT id, first_name, last_name FROM customers WHERE first_name = ?", new Object[] { "Josh" },
                (rs, rowNum) -> new Customer(rs.getLong("id"), rs.getString("first_name"), rs.getString("last_name"))
        ).forEach(customer -> log.info(customer.toString()));
    }
}
```

@SpringBootApplication 是一个便利注释，添加了以下所有内容：

- **@Configuration** 标记该类作为应用程序上下文的bean定义的源。
- **@EnableAutoConfiguration** 告诉Spring Boot开始根据类路径设置，其他bean和各种属性设置添加bean。
- **@ComponentScan**告诉Spring在包中寻找其他组件，配置和服务，允许它找到控制器。

**main()**方法使用Spring Boot的**SpringApplication.run()**方法来启动应用程序。您是否注意到没有一行XML？也没有web.xml文件。此Web应用程序是100％纯Java，您无需处理配置任何管道或基础结构。

main()方法使用Spring Boot的SpringApplication.run()方法来启动应用程序。您是否注意到没有一行XML？也没有web.xml文件。此Web应用程序是100％纯Java，您无需处理配置任何管道或基础结构。

Spring Boot支持**H2**，一种内存中的关系数据库引擎，并自动创建连接。因为我们使用的是spring-jdbc，Spring Boot会自动创建一个JdbcTemplate。**@Autowired**自动加载**JdbcTemplate**并使其可用。

这个Application类实现了Spring Boot **CommandLineRunner**，这意味着它将run()在加载应用程序上下文后执行该方法。

首先，使用**JdbcTemplate’s `execute**方法安装一些DDL 。

其次，您获取字符串列表并使用Java 8流，将它们拆分为Java数组中的firstname/lastname对。

然后使用**JdbcTemplate’s `batchUpdate**方法在新创建的表中安装一些记录。方法调用的第一个参数是查询字符串，最后一个参数（Objects 的数组）包含要替换为“？”字符的查询的变量。

**对于单个插入语句，JdbcTemplate’s `insert方法很好。但对于多个，最好使用batchUpdate。**

**使用?的参数，以避免[SQL注入攻击](https://en.wikipedia.org/wiki/SQL_injection)通过指示JDBC来绑定变量。**

最后，使用query方法在表中搜索与条件匹配的记录。您再次使用“?”参数为查询创建参数，在进行调用时传入实际值。最后一个参数是用于将每个结果行转换为新Customer对象的Java 8 lambda 。

**Java 8 lambdas很好地映射到单个方法接口，如Spring的RowMapper。如果您使用的是Java 7或更早版本，则可以轻松插入匿名接口实现，并具有与lambda expresion正文所包含的相同的方法体，并且它可以毫不费力地使用Spring。**

> 构建可执行的JAR

您可以使用Gradle或Maven从命令行运行该应用程序。或者，您可以构建一个包含所有必需依赖项，类和资源的可执行JAR文件，并运行该文件。这使得在整个开发生命周期中，跨不同环境等将服务作为应用程序发布，版本和部署变得容易。

如果您使用的是Gradle，则可以使用运行该应用程序**./gradlew bootRun**。或者您可以使用构建JAR文件**./gradlew build**。然后你可以运行JAR文件：

```bash
java -jar build/libs/gs-relational-data-access-0.1.0.jar
```

如果您使用的是Maven，则可以使用该应用程序运行该应用程序**./mvnw spring-boot:run**。或者您可以使用构建JAR文件**./mvnw clean package**。然后你可以运行JAR文件：

```bash
java -jar target/gs-relational-data-access-0.1.0.jar
```

**上面的过程将创建一个可运行的JAR。您也可以选择[构建经典WAR文件](https://www.springall.com.cn/java/gou_jian_jing_dian_war_wen_jian.html)。**

显示日志输出，您可以从日志中看到它在后台线程上。您应该每隔5秒钟看到计划任务：

```bash
2015-06-19 10:58:31.152  INFO 67731 --- [           main] hello.Application                        : Creating tables
2015-06-19 10:58:31.219  INFO 67731 --- [           main] hello.Application                        : Inserting customer record for John Woo
2015-06-19 10:58:31.220  INFO 67731 --- [           main] hello.Application                        : Inserting customer record for Jeff Dean
2015-06-19 10:58:31.220  INFO 67731 --- [           main] hello.Application                        : Inserting customer record for Josh Bloch
2015-06-19 10:58:31.220  INFO 67731 --- [           main] hello.Application                        : Inserting customer record for Josh Long
2015-06-19 10:58:31.230  INFO 67731 --- [           main] hello.Application                        : Querying for customer records where first_name = 'Josh':
2015-06-19 10:58:31.242  INFO 67731 --- [           main] hello.Application                        : Customer[id=3, firstName='Josh', lastName='Bloch']
2015-06-19 10:58:31.242  INFO 67731 --- [           main] hello.Application                        : Customer[id=4, firstName='Josh', lastName='Long']
2015-06-19 10:58:31.244  INFO 67731 --- [           main] hello.Application                        : Started Application in 1.693 seconds (JVM running for 2.054)
```

> 概要

恭喜！您刚刚使用Spring开发了一个简单的JDBC客户端。