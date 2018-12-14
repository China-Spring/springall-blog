本指南将引导您完成使用Spring创建“hello world” [简单理解REST](https://www.springall.com.cn/java/jian_dan_li_jie_rest.html)的过程。

> 你要构建什么

您将在以下位置构建一个接受HTTP GET请求的服务：

```bash
http://localhost:8080/greeting
```

并使用[JSON](https://www.springall.com.cn/java/jian_dan_li_jie_json.html)返回问候语：

```json
{"id":1,"content":"Hello, World!"}
```

您可以在查询字符串中使用**name**可选的名称参数自定义问候语:

```bash
http://localhost:8080/greeting?name=User
```

**name**参数值覆盖“World”的默认值，返回在响应中:

```json
{"id":1,"content":"Hello, User!"}
```

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

- [下载](https://github.com/spring-guides/gs-rest-service/archive/master.zip)并解压缩本指南的源存储库，或者使用[Git](https://spring.io/understanding/Git): 

```bash
git clone https://github.com/spring-guides/gs-rest-service.git
```

- 进入**gs-rest-service/initial**目录
- 跳转到创建资源类。

**完成后**，可以根据**gs-rest-service/complete**中的代码检查结果。

### 使用Gradle构建

首先，设置一个基本的构建脚本。在使用Spring构建应用程序时，您可以使用任何您喜欢的构建系统，但此处包含了使用[Gradle](http://gradle.org/)和[Maven](https://maven.apache.org/)所需的代码。如果您不熟悉这两者，请参阅[使用Gradle构建Java项目](https://www.springall.com.cn/java/shi_yong_gradle_gou_jian_java_xiang_mu.html)或[使用Maven构建Java项目](https://www.springall.com.cn/java/shi_yong_maven_gou_jian_java_xiang_mu.html)。

> 创建目录结构

在您选择的项目目录中，创建以下子目录结构;例如，在\*nix系统上使用**mkdir -p src/main/java/hello**:

```bash
└── src
    └── main
        └── java
            └── hello
```

> 创建Gradle构建文件

下面是[最初的Gradle构建文件](https://github.com/spring-guides/gs-rest-service/blob/master/initial/build.gradle)。

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
    baseName = 'gs-rest-service'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile('org.springframework.boot:spring-boot-starter-test')
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
    <artifactId>gs-rest-service</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.3.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.jayway.jsonpath</groupId>
            <artifactId>json-path</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-releases</id>
            <url>https://repo.spring.io/libs-release</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>spring-releases</id>
            <url>https://repo.spring.io/libs-release</url>
        </pluginRepository>
    </pluginRepositories>
</project>
```

在[Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html)提供了许多便捷的功能：

- 它收集类路径上的所有jar并构建一个可运行的“über-jar”，这使得执行和传输服务更加方便。
- 它搜索**public static void main()**标记为可运行类的方法。
- 它提供了一个内置的依赖项解析器，它设置版本号以匹配[Spring Boot依赖项](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)。您可以覆盖任何您希望的版本，但它将默认为Boot的所选版本集。

### 使用IDE构建

- 阅读如何将本指南直接导入[使用STS构建/导入入门指南](https://www.springall.com.cn/spring/shi_yong_sts_gou_jian_dao_ru_ru_men_zhi_nan.html)
- 阅读[使用IntelliJ IDEA导入入门指南](https://www.springall.com.cn/spring/shi_yong_intellij_idea_dao_ru_ru_men_zhi_nan.html)中如何使用本指南。

> 创建资源表示类

现在您已经设置了项目和构建系统，您可以创建Web服务。

通过考虑服务交互来开始这个过程。

该服务将处理GET请求/greeting，可选地使用name查询字符串中的参数。该GET请求应该返回200 OK在表示JSON响应成功。它应该看起来像这样：

```json
{
    "id": 1,
    "content": "Hello, World!"
}
```

id字段是问候语的唯一标识符，是问候语content的文本表示。

要为问候语表示建模，请创建资源表示形式类。提供一个普通的旧java对象，其中包含id和content数据的字段，构造函数和访问器：

**src/main/java/hello/Greeting.java**

```java
package hello;

public class Greeting {

    private final long id;
    private final String content;

    public Greeting(long id, String content) {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}
```

**正如您在下面的步骤中看到的，Spring使用[Jackson JSON](http://wiki.fasterxml.com/JacksonHome)库自动将类型实例Greeting封送到JSON中。**

接下来，您将创建将用于提供这些问候语的资源控制器。

> 创建资源控制器

在Spring构建基于rest的web服务的方法中，HTTP请求由控制器处理。**@RestController**注释很容易识别这些组件，下面的**GreetingController**通过返回greeting类的新实例来处理GET请求/greeting:

**src/main/java/hello/GreetingController.java**

```java
package hello;

import java.util.concurrent.atomic.AtomicLong;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @RequestMapping("/greeting")
    public Greeting greeting(@RequestParam(value="name", defaultValue="World") String name) {
        return new Greeting(counter.incrementAndGet(),
                            String.format(template, name));
    }
}
```

这个控制器简洁而简单，但引擎盖下有很多东西。让我们一步一步地分解它。

所述@RequestMapping注释可以确保HTTP请求/greeting被映射到greeting()方法。

**上面的示例未指定GETvs. PUT，POST等等，因为@RequestMapping默认情况下映射所有HTTP操作。使用@RequestMapping(method=GET)缩小这种映射。**

@RequestParam将查询字符串参数名的值绑定到greeting()方法的name参数中。如果请求中没有name参数，则使用“World”的defaultValue。

方法体的实现基于来自的下一个值创建并返回Greeting具有id和content属性的新对象，并使用问候counter格式化给定name的格式template。

传统MVC控制器和基于rest的web服务控制器之间的一个关键区别是HTTP响应主体的创建方式。这个基于rest的web服务控制器不依赖于[视图技术](https://www.springall.com.cn/java/jian_dan_le_jie_shi_tu_mo_ban.html)将问候数据执行到HTML的服务器端呈现，而是简单地填充并返回一个问候对象。对象数据将直接以JSON的形式写入HTTP响应。

此代码使用Spring 4的新@RestController注释，它将类标记为控制器，其中每个方法都返回一个域对象而不是视图。它是@Controller和@ResponseBody拼凑在一起的。

Greeting对象必须转换为JSON。由于Spring的HTTP消息转换器支持，您无需手动执行此转换。因为[Jackson 2](http://wiki.fasterxml.com/JacksonHome)在类路径上，所以**MappingJackson2HttpMessageConverter**会自动选择Spring来将Greeting实例转换为JSON。

> 使应用程序可执行

虽然可以将此服务打包为传统的[WAR](https://www.springall.com.cn/java/jian_dan_le_jie_war.html)文件以部署到外部应用程序服务器，但下面演示的更简单的方法创建了一个独立的应用程序。您将所有内容打包在一个可执行的JAR文件中，由一个好的旧Java main()方法驱动。在此过程中，您使用Spring的支持将[Tomcat](https://www.springall.com.cn/java/jian_dan_le_jie_tomcat.html) servlet容器嵌入为HTTP运行时，而不是部署到外部实例。

**src/main/java/hello/Application.java**

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

@SpringBootApplication 是一个便利注释，添加了以下所有内容：

- **@Configuration** 标记该类作为应用程序上下文的bean定义的源。
- **@EnableAutoConfiguration** 告诉Spring Boot开始根据类路径设置，其他bean和各种属性设置添加bean。
- 通常你会添加**@EnableWebMvc**一个Spring MVC应用程序，但Spring Boot会在类路径上看到spring-webmvc时自动添加它。这会将应用程序标记为Web应用程序并激活关键行为，例如设置DispatcherServlet。
- **@ComponentScan**告诉Spring在包中寻找其他组件，配置和服务，允许它找到控制器。

**main()**方法使用Spring Boot的**SpringApplication.run()**方法来启动应用程序。您是否注意到没有一行XML？也没有web.xml文件。此Web应用程序是100％纯Java，您无需处理配置任何管道或基础结构。

> 构建可执行的JAR

您可以使用Gradle或Maven从命令行运行该应用程序。或者，您可以构建一个包含所有必需依赖项，类和资源的可执行JAR文件，并运行该文件。这使得在整个开发生命周期中，跨不同环境等将服务作为应用程序发布，版本和部署变得容易。

如果您使用的是Gradle，则可以使用运行该应用程序**./gradlew bootRun**。或者您可以使用构建JAR文件**./gradlew build**。然后你可以运行JAR文件：

```bash
java -jar build/libs/gs-consuming-rest-0.1.0.jar
```

如果您使用的是Maven，则可以使用该应用程序运行该应用程序**./mvnw spring-boot:run**。或者您可以使用构建JAR文件**./mvnw clean package**。然后你可以运行JAR文件：

```bash
java -jar target/gs-consuming-rest-0.1.0.jar
```

上面的过程将创建一个可运行的JAR。您也可以选择[构建经典WAR文件](https://www.springall.com.cn/java/gou_jian_jing_dian_war_wen_jian.html)。

显示记录输出。该服务应在几秒钟内启动并运行。

> 测试服务

现在该服务已启动，请访问[http://localhost:8080/greeting](http://localhost:8080/greeting)

```json
{"id":1,"content":"Hello, World!"}
```

使用[http://localhost:8080/greeting?name=User](http://localhost:8080/greeting?name=User)提供名称查询字符串参数。注意，content属性的值是如何从“Hello, World!”更改为“Hello User!”

```json
{"id":2,"content":"Hello, User!"}
```

此更改表明该**@RequestParam**安排**GreetingController**正在按预期工作。该name参数已被赋予默认值“World”，但始终可以通过查询字符串显式覆盖。

另请注意id属性如何**更改1为2**。这证明您正在GreetingController跨多个请求针对同一实例工作，并且其counter字段在每次调用时按预期递增。

> 概要

恭喜！您刚刚使用Spring开发了RESTful Web服务。