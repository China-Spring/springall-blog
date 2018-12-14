本指南将指导您完成创建可以接收HTTP多文件上传服务器应用程序的过程。

> 你要构建什么

您将创建一个接受文件上传的Spring Boot Web应用程序。您还将构建一个简单的HTML界面来上传测试文件。

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

- [下载](https://github.com/spring-guides/gs-uploading-files/archive/master.zip)并解压缩本指南的源存储库，或使用Git克隆它：

```bash
git clone https://github.com/spring-guides/gs-uploading-files.git
```

- 进入**gs-uploading-files/initial**
- 跳转到创建Application类。

**完成后**，可以根据**ggs-uploading-files/complete**中的代码检查结果。

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

下面是[最初的Gradle构建文件](https://github.com/spring-guides/gs-uploading-files/blob/master/initial/build.gradle)。

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
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
    baseName = 'gs-uploading-files'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    compile("org.springframework.boot:spring-boot-starter-thymeleaf")
    testCompile("org.springframework.boot:spring-boot-starter-test")
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
    <artifactId>gs-uploading-files</artifactId>
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
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
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

> 创建一个Application类

要启动Spring Boot MVC应用程序，我们首先需要一个启动器; **spring-boot-starter-thymeleaf**和**spring-boot-starter-web**已经添加为依赖关系。要使用Servlet容器上传文件，您需要注册一个**MultipartConfigElement**类（在web.xml中**\**）。感谢Spring Boot，一切都是自动配置的！

您开始使用此应用程序所需的只是以下Application类。

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

作为自动配置Spring MVC的一部分，Spring Boot将创建一个**MultipartConfigElement** bean并为文件上传做好准备。

> 创建文件上传控制器

初始应用程序已经包含一些类来处理在磁盘上存储和加载上传的文件; 它们都位于hello.storage包中。我们将在新的**FileUploadController**中使用它们。

**src/main/java/hello/FileUploadController.java**

```java
package hello;

import java.io.IOException;
import java.util.stream.Collectors;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.io.Resource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.servlet.mvc.method.annotation.MvcUriComponentsBuilder;
import org.springframework.web.servlet.mvc.support.RedirectAttributes;

import hello.storage.StorageFileNotFoundException;
import hello.storage.StorageService;

@Controller
public class FileUploadController {

    private final StorageService storageService;

    @Autowired
    public FileUploadController(StorageService storageService) {
        this.storageService = storageService;
    }

    @GetMapping("/")
    public String listUploadedFiles(Model model) throws IOException {

        model.addAttribute("files", storageService.loadAll().map(
                path -> MvcUriComponentsBuilder.fromMethodName(FileUploadController.class,
                        "serveFile", path.getFileName().toString()).build().toString())
                .collect(Collectors.toList()));

        return "uploadForm";
    }

    @GetMapping("/files/{filename:.+}")
    @ResponseBody
    public ResponseEntity<Resource> serveFile(@PathVariable String filename) {

        Resource file = storageService.loadAsResource(filename);
        return ResponseEntity.ok().header(HttpHeaders.CONTENT_DISPOSITION,
                "attachment; filename=\"" + file.getFilename() + "\"").body(file);
    }

    @PostMapping("/")
    public String handleFileUpload(@RequestParam("file") MultipartFile file,
            RedirectAttributes redirectAttributes) {

        storageService.store(file);
        redirectAttributes.addFlashAttribute("message",
                "You successfully uploaded " + file.getOriginalFilename() + "!");

        return "redirect:/";
    }

    @ExceptionHandler(StorageFileNotFoundException.class)
    public ResponseEntity<?> handleStorageFileNotFound(StorageFileNotFoundException exc) {
        return ResponseEntity.notFound().build();
    }

}
```

这个类带有@Controller注解，因此Spring MVC可以选择并查找路由。每个方法都标记有**@GetMapping**或**@PostMapping**将路径和HTTP操作绑定到特定的Controller操作。

在这种情况下：

- **GET /**查找从StorageService上传的文件的当前列表，并将其加载到Thymeleaf模板中。它使用MvcUriComponentsBuilder计算到实际资源的链接
- **GET /files/{filename}**加载资源（如果存在），并将其发送到浏览器以使用**Content-Disposition**响应头进行下载
- **POST /**适用于处理多部分消息file并将其提供给**StorageService**保存

**在生产场景中，您更有可能将文件存储在临时位置，数据库或[Mongo的GridFS](http://docs.mongodb.org/manual/core/gridfs)之类的NoSQL存储中。最好不要使用内容加载应用程序的文件系统。**

您需要为控制器提供与StorageService存储层（例如文件系统）交互的控件。界面是这样的：

**src/main/java/hello/storage/StorageService.java**

```java
package hello.storage;

import org.springframework.core.io.Resource;
import org.springframework.web.multipart.MultipartFile;

import java.nio.file.Path;
import java.util.stream.Stream;

public interface StorageService {

    void init();

    void store(MultipartFile file);

    Stream<Path> loadAll();

    Path load(String filename);

    Resource loadAsResource(String filename);

    void deleteAll();

}
```

示例应用程序中有一个接口的示例实现。如果您想节省时间，可以复制并粘贴它。

> 创建一个简单的HTML模板

为了构建一些有趣的东西，下面的Thymeleaf模板是上传文件以及显示已上传内容的一个很好的例子。

**src/main/resources/templates/uploadForm.html**

```html
<html xmlns:th="http://www.thymeleaf.org">
<body>

	<div th:if="${message}">
		<h2 th:text="${message}"/>
	</div>

	<div>
		<form method="POST" enctype="multipart/form-data" action="/">
			<table>
				<tr><td>File to upload:</td><td><input type="file" name="file" /></td></tr>
				<tr><td></td><td><input type="submit" value="Upload" /></td></tr>
			</table>
		</form>
	</div>

	<div>
		<ul>
			<li th:each="file : ${files}">
				<a th:href="${file}" th:text="${file}" />
			</li>
		</ul>
	</div>

</body>
</html>
```

该模板有三个部分：

- 顶部的可选消息，其中Spring MVC写入了一个flash范围的消息。
- 允许用户上传文件的表单
- 从后端提供的文件列表

> 调整文件上传限制

配置文件上传时，设置文件大小限制通常很有用。想象一下尝试处理5GB文件上传！使用Spring Boot，我们可以**MultipartConfigElement**使用一些属性设置调整其自动配置。

将以下属性添加到现有属性设置：

**src/main/resources/application.properties**

```bash
spring.servlet.multipart.max-file-size=128KB
spring.servlet.multipart.max-request-size=128KB
spring.http.multipart.enabled=false
```

多部分设置受限制如下：

- **spring.http.multipart.max-file-size** 设置为128KB，意味着总文件大小不能超过128KB。
- **spring.http.multipart.max-request-size** 设置为128KB，表示a的总请求大小**multipart/form-data**不能超过128KB。

> 构建可执行的JAR

虽然可以将此服务打包为传统的[WAR](https://spring.io/understanding/WAR)文件以部署到外部应用程序服务器，但下面演示的更简单的方法创建了一个独立的应用程序。您将所有内容打包在一个可执行的JAR文件中，由一个好的旧Java main()方法驱动。在此过程中，您使用Spring的支持将[Tomcat](https://spring.io/understanding/Tomcat) servlet容器嵌入为HTTP运行时，而不是部署到外部实例。

您还需要一个目标文件夹来上传文件，所以让我们增强基本Application类并添加一个Boot **CommandLineRunner**，它在启动时删除并重新创建该文件夹：

**src/main/java/hello/Application.java**

```java
package hello;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;

import hello.storage.StorageProperties;
import hello.storage.StorageService;

@SpringBootApplication
@EnableConfigurationProperties(StorageProperties.class)
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    CommandLineRunner init(StorageService storageService) {
        return (args) -> {
            storageService.deleteAll();
            storageService.init();
        };
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
java -jar build/libs/gs-uploading-files-0.1.0.jar
```

如果您使用的是Maven，则可以使用该应用程序运行该应用程序**./mvnw spring-boot:run**。或者您可以使用构建JAR文件**./mvnw clean package**。然后你可以运行JAR文件：

```bash
java -jar target/gs-uploading-files-0.1.0.jar
```

**上面的过程将创建一个可运行的JAR。您也可以选择[构建经典WAR文件](http://www.springall.com.cn/article/96)。**

它运行接收文件上传的服务器端部分。显示记录输出。该服务应在几秒钟内启动并运行。

在服务器运行时，您需要打开浏览器并访问[http://localhost:8080/](http://localhost:8080/)以查看上传表单。选择一个（小）文件并按“Upload”，您应该从控制器中看到成功页面。选择一个太大的文件，你会得到一个丑陋的错误页面。

然后，您应该在浏览器窗口中看到类似的内容：

```bash
You successfully uploaded <name of your file>!
```

> 测试您的应用程序

在我们的应用程序中有多种方法可以测试此特定功能。这是一个利用**MockMvc**的示例，因此不需要启动Servlet容器：

**src/test/java/hello/FileUploadTests.java**

```java
package hello;

import java.nio.file.Paths;
import java.util.stream.Stream;

import org.hamcrest.Matchers;
import org.junit.Test;
import org.junit.runner.RunWith;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.mock.web.MockMultipartFile;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.BDDMockito.given;
import static org.mockito.BDDMockito.then;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.fileUpload;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.header;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.model;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import hello.storage.StorageFileNotFoundException;
import hello.storage.StorageService;

@RunWith(SpringRunner.class)
@AutoConfigureMockMvc
@SpringBootTest
public class FileUploadTests {

    @Autowired
    private MockMvc mvc;

    @MockBean
    private StorageService storageService;

    @Test
    public void shouldListAllFiles() throws Exception {
        given(this.storageService.loadAll())
                .willReturn(Stream.of(Paths.get("first.txt"), Paths.get("second.txt")));

        this.mvc.perform(get("/")).andExpect(status().isOk())
                .andExpect(model().attribute("files",
                        Matchers.contains("http://localhost/files/first.txt",
                                "http://localhost/files/second.txt")));
    }

    @Test
    public void shouldSaveUploadedFile() throws Exception {
        MockMultipartFile multipartFile = new MockMultipartFile("file", "test.txt",
                "text/plain", "Spring Framework".getBytes());
        this.mvc.perform(fileUpload("/").file(multipartFile))
                .andExpect(status().isFound())
                .andExpect(header().string("Location", "/"));

        then(this.storageService).should().store(multipartFile);
    }

    @SuppressWarnings("unchecked")
    @Test
    public void should404WhenMissingFile() throws Exception {
        given(this.storageService.loadAsResource("test.txt"))
                .willThrow(StorageFileNotFoundException.class);

        this.mvc.perform(get("/files/test.txt")).andExpect(status().isNotFound());
    }

}
```

在那些测试中，我们使用各种模拟来设置与Controller的交互，以及StorageService使用Servlet容器本身的MockMultipartFile交互。

> 概要

恭喜！您刚刚编写了一个使用Spring处理文件上传的Web应用程序。