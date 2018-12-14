本指南将指导您使用Maven构建一个简单的Java项目。

> 你要构建什么

您将创建一个提供一天中时间的应用程序，然后使用Maven构建它。

> 你需要什么

- 大约15分钟
- 最喜欢的文本编辑器或IDE
- [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html)或更高版本

> 如何完成本指南

与大多数[Spring入门指南](https://www.springall.com.cn/zhuan_lan/spring_ru_men_zhi_nan)一样，您可以从头开始并完成每个步骤，或者您可以绕过您已熟悉的基本设置步骤。无论哪种方式，您最终都会使用工作代码。

要**从头开始**，请继续使用Gradle构建。

要**跳过基础知识**，请执行以下操作：

- [下载](https://github.com/spring-guides/gs-maven/archive/master.zip)并解压缩本指南的源存储库，或使用Git克隆它：

```bash
git clone https://github.com/spring-guides/gs-maven.git
```

- 进入**gs-maven/initial**
- 跳到最开始。

**完成后**，可以根据**gs-maven/complete**中的代码检查结果。

> 设置项目

首先，您需要为Maven设置一个Java项目来构建。为了保持对Maven的关注，现在让项目尽可能简单。在您选择的项目文件夹中创建此结构。

> 创建目录结构

在您选择的项目目录中，创建以下子目录结构;例如，在\*nix系统上使用**mkdir -p src/main/java/hello**:

```bash
└── src
    └── main
        └── java
            └── hello
```

在src/main/java/hello目录中，您可以创建所需的任何Java类。为了与本指南的其余部分保持一致，请创建以下两个类：**HelloWorld.java**和**Greeter.java**。

**src/main/java/hello/HelloWorld.java**

```java
package hello;

public class HelloWorld {
    public static void main(String[] args) {
        Greeter greeter = new Greeter();
        System.out.println(greeter.sayHello());
    }
}
```

**src/main/java/hello/Greeter.java**

```java
package hello;

public class Greeter {
    public String sayHello() {
        return "Hello world!";
    }
}
```

现在您已经准备好使用Maven构建项目，下一步是安装Maven。

Maven可以在[http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi)下载为zip文件。只需要二进制文件，因此请查找指向apache-maven-{version}-bin.zip或apache-maven-{version}-bin.tar.gz的链接。

下载zip文件后，将其解压缩到您的计算机上。然后将bin文件夹添加到路径中。

要测试Maven安装，请从命令行运行mvn：

```bash
mvn -v
```

如果一切顺利，您应该会看到有关Maven安装的一些信息。它看起来类似于（尽管可能略有不同）以下内容：

```bash
Apache Maven 3.0.5 (r01de14724cdef164cd33c7c8c2fe155faf9602da; 2013-02-19 07:51:28-0600)
Maven home: /usr/share/maven
Java version: 1.7.0_09, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.7.0_09.jdk/Contents/Home/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "mac os x", version: "10.8.3", arch: "x86_64", family: "mac"
```

恭喜！你现在安装了Maven。

> 定义一个简单的Maven pom

现在已经安装了Maven，您需要创建一个Maven项目定义。Maven项目使用名为pom.xml的XML文件定义。除此之外，该文件还提供了项目在外部库上的名称，版本和依赖关系。

在项目的根目录下创建一个名为pom.xml的文件（即将其放在src文件夹旁边），并为其提供以下内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-maven</artifactId>
    <packaging>jar</packaging>
    <version>0.1.0</version>

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.1</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>hello.HelloWorld</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

除了可选**\**元素之外，这是构建Java项目所必需的最简单的pom.xml文件。它包括项目配置的以下详细信息：

- **\**。POM模型版本（总是4.0.0）。
- **\**。项目所属的组或组织。通常表示为反向域名。
- **\**。要赋予项目库工件的名称（例如，其JAR或WAR文件的名称）。
- **\**。正在构建的项目的版本。
- **\** - 如何打包项目。对于JAR文件打包，默认为“jar”。使用“war”进行WAR文件打包。

**在选择版本控制方案时，Spring建议采用[语义版本控制](http://semver.org/)方法。**

此时，您已定义了一个最小但功能强大的Maven项目。

> 构建Java代码

Maven现在已准备好构建该项目。您现在可以使用Maven执行多个构建生命周期目标，包括编译项目代码的目标，创建库包（例如JAR文件），以及在本地Maven依赖库中安装库。

要尝试构建，请在命令行中发出以下命令：

```bash
mvn compile
```

这将运行Maven，告诉它执行编译目标。完成后，您应该在target/classes目录中找到已编译的.class文件。

由于您不太可能希望直接分发或使用.class文件，因此您可能希望改为运行package：

```bash
mvn package
```

**package**将编译Java代码，运行任何测试，并通过包装内的一个JAR文件中的代码终止上升的目标目录。JAR文件的名称将基于项目\和\。例如，给定之前的最小pom.xml文件，JAR文件将命名为gs-maven-0.1.0.jar。

**如果您已将\“jar” 的值更改为“war”，则结果将是目标目录中的WAR文件而不是JAR文件。**

Maven还在本地计算机上维护了一个依赖存储库（通常位于主目录中的.m2/repository目录中），以便快速访问项目依赖项。如果您想将项目的JAR文件安装到该本地存储库，那么您应该调用install目标：

```bash
mvn install
```

**install**将编译，测试和打包项目的代码，然后将其复制到本地依赖性库，准备好另一个项目中引用它作为一个依赖。

说到依赖关系，现在是时候在Maven构建中声明依赖关系了。

> 声明依赖关系

简单的Hello World示例是完全独立的，不依赖于任何其他库。但是，大多数应用程序依赖外部库来处理常见和复杂的功能。

例如，假设除了说“Hello World！”之外，您还希望应用程序打印当前日期和时间。虽然您可以使用本机Java库中的日期和时间工具，但您可以使用Joda Time库使事情变得更有趣。

首先，将HelloWorld.java更改为如下所示：

```java
package hello;

import org.joda.time.LocalTime;

public class HelloWorld {
	public static void main(String[] args) {
		LocalTime currentTime = new LocalTime();
		System.out.println("The current local time is: " + currentTime);
		Greeter greeter = new Greeter();
		System.out.println(greeter.sayHello());
	}
}
```

这里HelloWorld使用Joda Time的**LocalTime**类来获取和打印当前时间。

如果您现在要运行**mvn compile**以构建项目，则构建将失败，因为您未在构建中将Joda Time声明为编译依赖项。您可以通过将以下行添加到pom.xml（在\元素内）来解决此问题：

```xml
<dependencies>
		<dependency>
			<groupId>joda-time</groupId>
			<artifactId>joda-time</artifactId>
			<version>2.9.2</version>
		</dependency>
</dependencies>
```

这个XML块声明了项目的依赖项列表。具体来说，它声明了Joda Time库的单一依赖项。在\元素内，依赖关系坐标由三个子元素定义：

- **\** - 依赖项所属的组或组织。
- **\** - 所需的库。
- **\** - 所需库的特定版本。

默认情况下，所有依赖项都作为**compile**依赖项确定范围。也就是说，它们应该在编译时可用（如果您正在构建WAR文件，包括在WAR的/WEB-INF/libs文件夹中）。此外，您可以指定一个\元素以指定以下范围之一：

- **provided** - 编译项目代码所需的依赖关系，但是将在运行时由运行代码的容器（例如，Java Servlet API）提供。
- **test** - 用于编译和运行测试的依赖关系，但不是构建或运行项目的运行时代码所必需的依赖关系。

现在，如果您运行**mvn compile**或**mvn package**，Maven应该从Maven Central存储库解析Joda Time依赖关系，并且构建将成功。

> 写一个测试

首先在测试范围中将JUnit添加为pom.xml的依赖项：

```xml
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.12</version>
	<scope>test</scope>
</dependency>
```

然后创建一个这样的测试用例：

**src/test/java/hello/GreeterTest.java**

```java
package hello;

import static org.hamcrest.CoreMatchers.containsString;
import static org.junit.Assert.*;

import org.junit.Test;

public class GreeterTest {

	private Greeter greeter = new Greeter();

	@Test
	public void greeterSaysHello() {
		assertThat(greeter.sayHello(), containsString("Hello"));
	}

}
```

Maven使用一个名为“surefire”的插件来运行单元测试。此插件的默认配置**src/test/java**使用名称匹配编译并运行所有***Test**类。您可以在命令行上运行测试

```bash
mvn test
```

或者只使用**mvn install**上面已经显示的步骤（有一个生命周期定义，其中“test”作为“安装”中的一个阶段包含在内）。

这是完成的**pom.xml**文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>org.springframework</groupId>
	<artifactId>gs-maven</artifactId>
	<packaging>jar</packaging>
	<version>0.1.0</version>

	<properties>
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>

	<dependencies>
		<!-- tag::joda[] -->
		<dependency>
			<groupId>joda-time</groupId>
			<artifactId>joda-time</artifactId>
			<version>2.9.2</version>
		</dependency>
		<!-- end::joda[] -->
		<!-- tag::junit[] -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>
		<!-- end::junit[] -->
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-shade-plugin</artifactId>
				<version>2.1</version>
				<executions>
					<execution>
						<phase>package</phase>
						<goals>
							<goal>shade</goal>
						</goals>
						<configuration>
							<transformers>
								<transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
									<mainClass>hello.HelloWorld</mainClass>
								</transformer>
							</transformers>
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>

</project>
```

**完成的pom.xml文件使用[Maven Shade插件](https://maven.apache.org/plugins/maven-shade-plugin/)，以简化JAR文件的可执行性。本指南的重点是Maven的入门，而不是使用这个特定的插件。**

> 概要

恭喜！您已经为构建Java项目创建了一个简单而有效的Maven项目定义。