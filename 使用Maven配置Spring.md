这篇文章说明了如何通过Maven配置Spring依赖项。最新的Spring版本可以在[Maven Central](http://search.maven.org/#search|ga|1|g%3A%22org.springframework%22)上找到。

> Maven中的Spring基本依赖关系

Spring的设计是高度模块化的 - 使用Spring的一部分不应该而且不需要另一部分。例如，基本的Spring Context可以没有Persistence或MVC Spring库。

让我们先从一个基本Maven配置，将只使用了**spring-context**依赖：

```xml
<properties>
    <org.springframework.version>3.2.8.RELEASE</org.springframework.version>
    <!-- <org.springframework.version>4.0.2.RELEASE</org.springframework.version> -->
</properties>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${org.springframework.version}</version>
    <scope>runtime</scope>
</dependency>
```

这个依赖项 - **spring-context** - 定义了实际的Spring Injection Container，并且有少量的依赖项：spring-core，spring-expression，spring-aop和spring-beans。通过支持一些**核心Spring技术**来扩充容器：Core Spring实用程序，[Spring表达式语言（SpEL）](http://static.springsource.org/spring/docs/3.2.x/spring-framework-reference/html/expressions.html)，[面向对象编程](http://static.springsource.org/spring/docs/3.2.x/spring-framework-reference/html/aop.html#aop-introduction)支持和[JavaBeans机制](http://static.springsource.org/spring/docs/3.2.x/spring-framework-reference/html/beans.html#beans-definition)。

注意我们在**运行时范围**中定义了依赖关系- 这将确保在任何特定于Spring的API上没有编译时依赖性。对于更高级的用例，可以从一些选定的Spring依赖项中删除运行时范围，但是对于更简单的项目，**不需要针对Spring进行编译**以充分利用该框架。

另请注意，从Spring 3.2开始，**不需要定义CGLIB依赖项**（现在已升级到CGLIB 3.0） - 它已被重新打包（所有net.sf.cglib包现在是org.springframework.cglib）并且直接在内部内联spring-core JAR（有关其他详细信息，请参阅[JIRA](https://jira.springsource.org/browse/SPR-9669)）。

> Maven配置Spring Persistence

现在让我们看一下Spring Persistence依赖关系 - 主要是spring-orm：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>${org.springframework.version}</version>
</dependency>
```

这附带了Hibernate和JPA支持 - 例如HibernateTemplate和JpaTemplate - 以及一些额外的，持久性相关的依赖项：spring-jdbc和spring-tx。

JDBC数据访问库定义了[Spring JDBC支持](http://static.springsource.org/spring/docs/3.2.x/spring-framework-reference/html/jdbc.html)以及JdbcTemplate，而spring-tx代表了极其灵活的[事务管理抽象](http://static.springsource.org/spring/docs/3.2.x/spring-framework-reference/html/transaction.html)。

> Maven配置Spring MVC

要使用Spring Web和Servlet支持，除了上面的核心依赖项外，还需要在pom中包含两个依赖项：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>${org.springframework.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>${org.springframework.version}</version>
</dependency>
```

spring-web依赖项包含Servlet和Portlet环境的公共web特定实用程序，而spring-webmvc支持Servlet环境的MVC。

由于spring-webmvc将spring-web作为依赖项，因此在使用spring-webmvc时不需要明确定义spring-web。

> 使用maven配置Spring Security

在[使用Maven配置Spring Security](https://www.springall.com.cn/spring/shi_yong_maven_pei_zhi_spring_security.html)文章中深入讨论了Maven配置Spring Security依赖关系。

> 使用Maven配置Spring Test

Spring Test Framework可以通过以下依赖项包含在项目中：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>${spring.version}</version>
    <scope>test</scope>
</dependency>
```

从Spring 3.2开始，Spring MVC Test项目已经包含在核心测试框架中 - 因此包括spring-test依赖就足够了。

> 使用Milestones

Spring的发布版本托管在Maven Central上。但是，如果项目需要使用Milestones版本，则需要将自定义Spring存储库添加到pom中：

```xml
<repositories>
    <repository>
        <id>repository.springframework.maven.milestone</id>
        <name>Spring Framework Maven Milestone Repository</name>
        <url>http://repo.spring.io/milestone/</url>
    </repository>
</repositories>
```

已定义了一个此存储库，该项目可以定义依赖项，例如：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>3.2.0.RC2</version>
</dependency>
```

> 使用Snapshots

与Milestones类似，Snapshots托管在自定义存储库中：

```xml
<repositories>
    <repository>
        <id>repository.springframework.maven.snapshot</id>
        <name>Spring Framework Maven Snapshot Repository</name>
        <url>http://repo.spring.io/snapshot/</url>
    </repository>
</repositories>
```

在pom.xml中启用SNAPSHOT存储库后，可以引用以下依赖项：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>3.3.0.BUILD-SNAPSHOT</version>
</dependency>
```

对于4.x：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.0.3.BUILD-SNAPSHOT</version>
</dependency>
```
