今天我们尝试Spring Boot整合Kotlin，并决定建立一个非常简单的Spring Boot微服务，使用Kotlin作为编程语言进行编码构建。

创建一个简单的Spring Boot应用程序。我会在这里使用maven构建项目：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://maven.apache.org/POM/4.0.0"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.edurt.ski</groupId>
    <artifactId>springboot-kotlin-integration</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <name>springboot kotlin integration</name>
    <description>SpringBoot Kotlin Integration is a open source springboot, kotlin integration example.</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <!-- plugin config -->
        <plugin.maven.kotlin.version>1.2.71</plugin.maven.kotlin.version>
    </properties>

    <dependencies>
        <!-- spring boot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- kotlin -->
        <dependency>
            <groupId>com.fasterxml.jackson.module</groupId>
            <artifactId>jackson-module-kotlin</artifactId>
        </dependency>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-stdlib-jdk8</artifactId>
        </dependency>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-reflect</artifactId>
        </dependency>
    </dependencies>

    <build>
        <sourceDirectory>${project.basedir}/src/main/kotlin</sourceDirectory>
        <testSourceDirectory>${project.basedir}/src/test/kotlin</testSourceDirectory>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <artifactId>kotlin-maven-plugin</artifactId>
                <groupId>org.jetbrains.kotlin</groupId>
                <configuration>
                    <args>
                        <arg>-Xjsr305=strict</arg>
                    </args>
                    <compilerPlugins>
                        <plugin>spring</plugin>
                        <plugin>jpa</plugin>
                        <plugin>all-open</plugin>
                    </compilerPlugins>
                    <pluginOptions>
                        <option>all-open:annotation=javax.persistence.Entity</option>
                    </pluginOptions>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>org.jetbrains.kotlin</groupId>
                        <artifactId>kotlin-maven-allopen</artifactId>
                        <version>${plugin.maven.kotlin.version}</version>
                    </dependency>
                    <dependency>
                        <groupId>org.jetbrains.kotlin</groupId>
                        <artifactId>kotlin-maven-noarg</artifactId>
                        <version>${plugin.maven.kotlin.version}</version>
                    </dependency>
                </dependencies>
                <executions>
                    <execution>
                        <id>kapt</id>
                        <goals>
                            <goal>kapt</goal>
                        </goals>
                        <configuration>
                            <sourceDirs>
                                <sourceDir>src/main/kotlin</sourceDir>
                            </sourceDirs>
                            <annotationProcessorPaths>
                                <annotationProcessorPath>
                                    <groupId>org.springframework.boot</groupId>
                                    <artifactId>spring-boot-configuration-processor</artifactId>
                                    <version>${project.parent.version}</version>
                                </annotationProcessorPath>
                            </annotationProcessorPaths>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

</project>

```

添加所有必需的依赖项：

- kotlin-stdlib-jdk8 kotlin jdk8的lib包
- kotlin-reflect kotlin反射包

一个简单的应用类：

```kotlin
package com.edurt.ski

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication

@SpringBootApplication
class SpringBootKotlinIntegration

fun main(args: Array<String>) {
    runApplication<SpringBootKotlinIntegration>(*args)
}

```

#### 添加Rest API接口功能

---

创建一个HelloController Rest API接口,我们只提供一个简单的get请求获取**hello,kotlin**输出信息:

```kotlin
package com.edurt.ski.controller

import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RestController

@RestController
class HelloController {

    @GetMapping(value = "hello")
    fun hello(): String {
        return "hello,kotlin"
    }

}
```

修改**SpringBootKotlinIntegration**文件增加以下设置扫描路径

```kotlin
@ComponentScan(value = [
    "com.edurt.ski",
    "com.edurt.ski.controller"
])
```

#### 添加页面功能

---

- 修改pom.xml文件增加以下页面依赖

```xml
<!-- mustache -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mustache</artifactId>
</dependency>
```

- 在`src/main/resources`路径下创建**templates**文件夹

- 在**templates**文件夹下创建一个名为`hello.mustache`的页面文件

```html
<h1>Hello, Kotlin</h1>
```

- 创建页面转换器**HelloView**

```kotlin
package com.edurt.ski.view

import org.springframework.stereotype.Controller
import org.springframework.ui.Model
import org.springframework.web.bind.annotation.GetMapping

@Controller
class HelloView {

    @GetMapping(value = "hello_view")
    fun helloView(model: Model): String {
        return "hello"
    }

}
```

- 浏览器访问http://localhost:8080/hello_view即可看到页面内容

#### 添加数据持久化功能

---

- 修改pom.xml文件增加以下依赖(由于测试功能我们使用h2内存数据库)

```kotlin
<!-- data jpa and db -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

- 创建**User**实体

```kotlin
package com.edurt.ski.model

import javax.persistence.Entity
import javax.persistence.GeneratedValue
import javax.persistence.Id

@Entity
//class UserModel(
//        @Id
//        @GeneratedValue
//        private var id: Long? = 0,
//        private var name: String
//)
class UserModel {

        @Id
        @GeneratedValue
        var id: Long? = 0
                get() = field
                set

        var name: String? = null
                get() = field
                set

}
```

- 创建**UserSupport** dao数据库操作工具类

```kotlin
package com.edurt.ski.support

import com.edurt.ski.model.UserModel
import org.springframework.data.repository.PagingAndSortingRepository

interface UserSupport : PagingAndSortingRepository<UserModel, Long> {

}
```

- 创建**UserService**服务类

```kotlin
package com.edurt.ski.service

import com.edurt.ski.model.UserModel

interface UserService {

    /**
     * save model to db
     */
    fun save(model: UserModel): UserModel

}
```

- 创建**UserServiceImpl**实现类

```kotlin
package com.edurt.ski.service

import com.edurt.ski.model.UserModel
import com.edurt.ski.support.UserSupport
import org.springframework.stereotype.Service

@Service(value = "userService")
class UserServiceImpl(private val userSupport: UserSupport) : UserService {

    override fun save(model: UserModel): UserModel {
        return this.userSupport.save(model)
    }

}
```

- 创建用户**UserController**进行持久化数据

```kotlin
package com.edurt.ski.controller

import com.edurt.ski.model.UserModel
import com.edurt.ski.service.UserService
import org.springframework.web.bind.annotation.PathVariable
import org.springframework.web.bind.annotation.PostMapping
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController

@RestController
@RequestMapping(value = "user")
class UserController(private val userService: UserService) {

    @PostMapping(value = "save/{name}")
    fun save(@PathVariable name: String): UserModel {
        val userModel = UserModel()
//        userModel.id = 1
        userModel.name = name
        return this.userService.save(userModel)
    }

}
```

- 使用控制台窗口执行以下命令保存数据

```bash
curl -X POST http://localhost:8080/user/save/qianmoQ
```

收到返回结果

```bash
{"id":1,"name":"qianmoQ"}
```

表示数据保存成功

#### 增加数据读取渲染功能

---

- 修改**UserService**增加以下代码

```kotlin
/**
 * get all model
 */
fun getAll(page: Pageable): Page<UserModel>
```

- 修改**UserServiceImpl**增加以下代码

```kotlin
override fun getAll(page: Pageable): Page<UserModel> {
    return this.userSupport.findAll(page)
}
```

- 修改**UserController**增加以下代码

```kotlin
@GetMapping(value = "list")
fun get(): Page<UserModel> = this.userService.getAll(PageRequest(0, 10))
```

- 创建**UserView**文件渲染User数据

```kotlin
package com.edurt.ski.view

import com.edurt.ski.service.UserService
import org.springframework.data.domain.PageRequest
import org.springframework.stereotype.Controller
import org.springframework.ui.Model
import org.springframework.ui.set
import org.springframework.web.bind.annotation.GetMapping

@Controller
class UserView(private val userService: UserService) {

    @GetMapping(value = "user_view")
    fun helloView(model: Model): String {
        model["users"] = this.userService.getAll(PageRequest(0, 10))
        return "user"
    }

}
```

- 创建**user.mustache**文件渲染数据(自行解析返回数据即可)

```html
{{users}}
```

- 浏览器访问http://localhost:8080/user_view即可看到页面内容

#### 增加单元功能

---

- 修改pom.xml文件增加以下依赖

```xml
<!-- test -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <scope>test</scope>
</dependency>
```

- 创建**UserServiceTest**文件进行测试**UserService**功能

```kotlin
package com.edurt.ski

import com.edurt.ski.service.UserService
import org.junit.jupiter.api.AfterAll
import org.junit.jupiter.api.Test
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.context.SpringBootTest
import org.springframework.data.domain.PageRequest

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class UserServiceTest(@Autowired private val userService: UserService) {

    @Test
    fun `get all`() {
        println(">> Assert blog page title, content and status code")
        val entity = this.userService.getAll(PageRequest(0, 1))
        print(entity.totalPages)
    }

}
```

源码地址：[GitHub](https://github.com/SpringBootIntegration/springboot-kotlin-integration.git)
