本文将介绍如何**使用Maven配置Spring Security**，并将介绍使用Spring Security依赖的特定用例。最新的Spring Security版本可以在[Maven Central](http://search.maven.org/#search|ga|1|g%3A%22org.springframework.security%22)上找到。

这是前一篇[使用Maven配置Spring](https://www.springall.com.cn/java/shi_yong_maven_pei_zhi_spring.html)的后续内容，因此对于非安全性的Spring依赖项，这是开始的地方。

### 使用Maven配置Spring Security

> spring-security-core

Core Spring Security支持 - **spring-security-core** - 包含身份验证和访问控制功能，并支持独立（非Web）应用程序，方法级安全性和JDBC：

```xml
<properties>
    <spring-security.version>5.0.6.RELEASE</spring-security.version>
    <spring.version>5.0.6.RELEASE</spring.version>
</properties>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-core</artifactId>
    <version>${spring-security.version}</version>
</dependency>
```

请注意，**Spring和Spring Security处于不同的发布计划中**，因此版本号之间并不总是1：1匹配。

如果您正在使用旧版本的Spring - 同样非常重要的是要理解，事实上，Spring Security 3.1.x并不依赖于Spring 3.1.x版本！这是因为Spring Security 3.1.x在Spring 3.1之前发布。计划是在未来的版本中更紧密地调整这些依赖关系 - 请参阅此[JIRA](https://jira.springsource.org/browse/SEC-2123)以获取更多详细信息 - 但目前，这具有实际意义，我们将在下面介绍。

> spring-security-web

要**为Spring Security添加Web支持**，需要spring-security-web依赖项：

```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>${spring-security.version}</version>
</dependency>
```

它包含过滤器和相关的Web安全基础结构，可在Servlet环境中启用URL访问控制。

> Spring Security和较旧的Spring Core依赖项问题

这个新依赖项显示了**Maven依赖图表现出一个问题** - 如上所述，Spring Security jar不依赖于最新的Spring core jar（但是依赖于以前的版本）。这可能导致这些旧的依赖项位于类路径之上，而不是更新的Spring 4.x组件。

为了理解为什么会发生这种情况，我们需要看看**Maven如何解决这些问题** - 如果版本冲突，Maven将选择最接近根的jar。在我们的例子中，spring-core由spring-orm（使用4.x.RELEASE版本）定义，但也由spring-security-core（使用旧的3.2.8.RELEASE版本）定义 - 所以在这两种情况下，spring-jdbc在我们项目的根pom的深度为1处定义。因此，在我们自己的pom中定义spring-orm和spring-security-core的顺序实际上很重要-第一个将优先考虑**我们最终可能会在类路径上找到任何一个版本**。

为了解决这个问题，我们必须在我们自己的pom中**明确定义一些Spring依赖项**，而不是依赖于隐式Maven依赖项解析机制 - 这样做会将特定的依赖项放在我们pom的深度0处（因为它在pom本身）因此它将优先考虑。以下所有内容属于同一类别，并且都需要直接显式定义，或者对于多模块项目，需要在父类的dependencyManagement元素中显式定义：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>${spring-version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring-version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring-version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>${spring-version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>${spring-version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>${spring-version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-expression</artifactId>
    <version>${spring-version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>${spring-version}</version>
</dependency>
```

> spring-security-config

要使用丰富的Spring Security XML命名空间，需要spring-security-config依赖关系：

```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>${spring-security.version}</version>
    <scope>runtime</scope>
</dependency>
```

没有应用程序代码应针对此依赖项进行编译，因此应将其作为**运行时范围**。

最后，LDAP，ACL，CAS和OpenID支持在Spring Security中有自己的依赖关系：spring-security-ldap，spring-security-acl，spring-security-cas和spring-security-openid。

> 使用快照(Snapshots)和里程碑(Milestones)

Spring提供的自定义Maven存储库中提供了Spring Security [快照(Snapshots)](http://maven.springframework.org/snapshot/org/springframework/security/)和[里程碑(Milestones) ](http://maven.springframework.org/milestone/org/springframework/security/)- 有关如何配置这些内容的更多详细信息，请参阅[如何使用快照(Snapshots)和里程碑(Milestones)](http://www.baeldung.com/spring-with-maven#milestones)。
