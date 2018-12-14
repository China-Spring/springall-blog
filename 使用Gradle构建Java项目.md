本指南将引导您使用Gradle构建一个简单的Java项目。

> 你要构建什么

您将创建一个简单的应用程序，然后使用Gradle构建它。

> 你需要什么

- 大约15分钟
- 最喜欢的文本编辑器或IDE
- [JDK 6](http://www.oracle.com/technetwork/java/javase/downloads/index.html)或更高版本

> 如何完成本指南

与大多数[Spring入门指南](https://www.springall.com.cn/zhuan_lan/spring_ru_men_zhi_nan)一样，您可以从头开始并完成每个步骤，或者您可以绕过您已熟悉的基本设置步骤。无论哪种方式，您最终都会使用工作代码。

要**从头开始**，请继续使用Gradle构建。

要**跳过基础知识**，请执行以下操作：

- [下载](https://github.com/spring-guides/gs-gradle/archive/master.zip)并解压缩本指南的源存储库，或使用Git克隆它：

```bash
git clone https://github.com/spring-guides/gs-gradle.git
```

- 进入**gs-gradle/initial**
- 跳转到安装Gradle。

**完成后**，可以根据**gs-gradle/complete**中的代码检查结果。

> 设置项目

首先，您要为Gradle建立一个Java项目。为了保持对Gradle的关注，让项目尽可能简单。

> 创建目录结构

在您选择的项目目录中，创建以下子目录结构;例如，在\*nix系统上使用**mkdir -p src/main/java/hello**:

```bash
└── src
    └── main
        └── java
            └── hello
```

在**src/main/java/hello**目录中，您可以创建所需的任何Java类。为了简单起见并与本指南的其余部分保持一致，Spring建议您创建两个类：**HelloWorld.java**和**Greeter.java**。

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

> 安装Gradle

现在您有了一个可以使用Gradle构建的项目，您就可以安装Gradle了。

强烈建议使用安装程序：

- [SDKMAN](http://sdkman.io/)
- [Homebrew](https://brew.sh/)（brew install gradle）

最后，如果这些工具都不适合您的需要，您可以从[http://www.gradle.org/downloads](http://www.gradle.org/downloads)下载二进制文件。只有二进制文件是必需的，所以要查找到gradle-version-bin.zip 的链接。(您也可以选择gradle-version-all.zip以获取源代码和文档以及二进制文件。）

将文件解压缩到您的计算机，然后将bin文件夹添加到您的路径中。

要测试Gradle安装，请从命令行运行Gradle：

```bash
gradle
```

如果一切顺利，您会看到一条欢迎信息：

```bash
:help

Welcome to Gradle 2.3.

To run a build, run gradle <task> ...

To see a list of available tasks, run gradle tasks

To see a list of command-line options, run gradle --help

BUILD SUCCESSFUL

Total time: 2.675 secs
```

您现在安装了Gradle。

> 了解Gradle可以做些什么

现在已经安装了Gradle，看看它能做什么。在为项目创建build.gradle文件之前，您可以询问它可用的任务：

```bash
gradle tasks
```

您应该看到可用任务列表。假设您在没有build.gradle文件的文件夹中运行Gradle ，您将看到一些非常基本的任务，例如：

```bash
:tasks

== All tasks runnable from root project

== Build Setup tasks
setupBuild - Initializes a new Gradle build. [incubating]

== Help tasks
dependencies - Displays all dependencies declared in root project 'gs-gradle'.
dependencyInsight - Displays the insight into a specific dependency in root project 'gs-gradle'.
help - Displays a help message
projects - Displays the sub-projects of root project 'gs-gradle'.
properties - Displays the properties of root project 'gs-gradle'.
tasks - Displays the tasks runnable from root project 'gs-gradle'.

To see all tasks and more detail, run with --all.

BUILD SUCCESSFUL

Total time: 3.077 secs
```

即使这些任务可用，但如果没有项目构建配置，它们也不会提供太多价值。当你充实**build.gradle**文件时，一些任务会更有用。在添加**build.gradle**插件时，任务列表会增加，因此您偶尔会想要再次运行任务以查看可用的任务。

说到添加插件，接下来添加一个启用基本Java构建功能的插件。

> 构建Java代码

从简单的**build.gradle**开始，在本指南开头创建的中创建一个非常基本的文件。只给它一行

```bash
apply plugin: 'java'
```

构建配置中的这一行带来了大量功率。再次运行**gradle tasks**，您会看到添加到列表中的新任务，包括构建项目，创建JavaDoc和运行测试的任务。

您将经常使用**gradle build**。此任务将代码编译，测试并组装到JAR文件中。你可以像这样运行它：

```bash
gradle build
```

几秒钟后，“BUILD SUCCESSFUL”表示构建已完成。

要查看构建工作的结果，请查看构建文件夹。在那里你会找到几个目录，包括这三个值得注意的文件夹：

- classes：该项目已编译的.class文件。
- reports：构建生成的报告（例如测试报告）。
- libs：汇编的项目库（通常是JAR和/或WAR文件）。

classes文件夹具有通过编译Java代码生成的.class文件。具体来说，您应该找到HelloWorld.class和Greeter.class。

此时，项目没有任何库依赖项，因此**dependency_cache**文件夹中没有任何内容。

reports文件夹应包含项目运行单元测试的报告。由于该项目尚未进行任何单元测试，因此该报告无效。

libs文件夹应包含以项目文件夹命名的JAR文件。再往下，您将看到如何指定JAR的名称及其版本。

> 声明依赖项

简单的Hello World示例是完全独立的，不依赖于任何其他库。但是，大多数应用程序依赖于外部库来处理常见或复杂的功能。

例如，假设除了说“Hello World！”之外，您还希望应用程序打印当前日期和时间。您可以使用本机Java库中的日期和时间工具，但是通过使用Joda Time库可以使事情变得更有趣。

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

这里**HelloWorld**使用Joda Time的**LocalTime**类来获取和打印当前时间。

如果您现在运行**gradle build**来构建项目，那么构建将失败，因为您没有在构建中将Joda Time声明为编译依赖项。

对于初学者，您需要为第三方库添加源。

```java
repositories {
    mavenCentral()
}
```

repositories块指示构建应从Maven Central存储库解析其依赖关系。Gradle严重依赖Maven构建工具建立的许多约定和工具，包括使用Maven Central作为库依赖关系源的选项。

现在我们已经为第三方库做好了准备，让我们宣布一些。

```java
sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile "joda-time:joda-time:2.2"
    testCompile "junit:junit:4.12"
}
```

使用dependencies块，您可以为Joda Time声明一个依赖项。具体来说，你在joda-time组中要求（从右到左阅读）joda-time库2.2版。

关于这种依赖关系的另一个注意事项是它是一个compile依赖项，表明它应该在编译时可用（如果你正在构建一个WAR文件，包含在WAR的/WEB-INF/libs文件夹中）。其他值得注意的依赖类型包括：

- **providedCompile**。编译项目代码所需的依赖项，但是运行代码的容器将在运行时提供该依赖项（例如，Java Servlet API）。
- **testCompile**。用于编译和运行测试的依赖项，但不是构建或运行项目的运行时代码所必需的。

最后，让我们指定JAR工件的名称。

```java
jar {
    baseName = 'gs-gradle'
    version =  '0.1.0'
}
```

jar块指定如何命名JAR文件。在这种情况下，它将呈现**gs-gradle-0.1.0.jar**。

现在，如果您运行**gradle build**，Gradle应该从Maven Central存储库解析Joda Time依赖项，并且构建将成功。

> 使用Gradle Wrapper构建项目

Gradle Wrapper是启动Gradle构建的首选方式。它由Windows的批处理脚本和OS X和Linux的shell脚本组成。这些脚本允许您运行Gradle构建，而无需在系统上安装Gradle。这曾经是添加到您的构建文件中的东西，但它已被折叠到Gradle中，因此不再需要。相反，您只需使用以下命令。

```bash
$ gradle wrapper --gradle-version 2.13
```

完成此任务后，您会注意到一些新文件。这两个脚本位于文件夹的根目录中，而包装jar和属性文件已添加到新**gradle/wrapper**文件夹中。

```java
└── <project folder>
    └── gradlew
    └── gradlew.bat
    └── gradle
        └── wrapper
            └── gradle-wrapper.jar
            └── gradle-wrapper.properties
```

Gradle Wrapper现在可用于构建项目。将它添加到您的版本控制系统，克隆项目的每个人都可以构建它。它可以与安装的Gradle版本完全相同的方式使用。运行包装器脚本来执行构建任务，就像之前一样：

```bash
./gradlew build
```

第一次运行指定版本的Gradle的包装器时，它会下载并缓存该版本的Gradle二进制文件。Gradle Wrapper文件旨在提交源代码控制，以便任何人都可以构建项目，而无需先安装和配置特定版本的Gradle。

在此阶段，您将构建代码。你可以在这里看到结果：

```java
build
├── classes
│   └── main
│       └── hello
│           ├── Greeter.class
│           └── HelloWorld.class
├── dependency-cache
├── libs
│   └── gs-gradle-0.1.0.jar
└── tmp
    └── jar
        └── MANIFEST.MF
```

包括的是两个预期类文件**Greeter**和**HelloWorld**，以及JAR文件。快速浏览一下：

```java
$ jar tvf build/libs/gs-gradle-0.1.0.jar
  0 Fri May 30 16:02:32 CDT 2014 META-INF/
 25 Fri May 30 16:02:32 CDT 2014 META-INF/MANIFEST.MF
  0 Fri May 30 16:02:32 CDT 2014 hello/
369 Fri May 30 16:02:32 CDT 2014 hello/Greeter.class
988 Fri May 30 16:02:32 CDT 2014 hello/HelloWorld.class
```

类文件被捆绑在一起。值得注意的是，即使您将joda-time声明为依赖项，此处也不包含该库。并且JAR文件也不可运行。

为了使这段代码可以运行，我们可以使用gradle的**application**插件。将其添加到您的**build.gradle**文件中。

```java
apply plugin: 'application'

mainClassName = 'hello.HelloWorld'
```

然后你可以运行该应用程序！

```java
$ ./gradlew run
:compileJava UP-TO-DATE
:processResources UP-TO-DATE
:classes UP-TO-DATE
:run
The current local time is: 16:16:20.544
Hello world!

BUILD SUCCESSFUL

Total time: 3.798 secs
```

捆绑依赖关系需要更多思考。例如，如果我们构建一个WAR文件，一种通常与第三方依赖关系打包相关的格式，我们可以使用gradle的[WAR插件](http://www.gradle.org/docs/current/userguide/war_plugin.html)。如果您使用的是Spring Boot并且想要一个可运行的JAR文件，那么[spring-boot-gradle-plugin](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-gradle)非常方便。在这个阶段，gradle对您的系统不了解，无法做出选择。但就目前而言，这应该足以开始使用gradle了。

要完成本指南的内容，这里是完整的**build.gradle**文件：

```java
apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'application'

mainClassName = 'hello.HelloWorld'

// tag::repositories[]
repositories {
    mavenCentral()
}
// end::repositories[]

// tag::jar[]
jar {
    baseName = 'gs-gradle'
    version =  '0.1.0'
}
// end::jar[]

// tag::dependencies[]
sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile "joda-time:joda-time:2.2"
    testCompile "junit:junit:4.12"
}
// end::dependencies[]

// tag::wrapper[]
// end::wrapper[]
```

**此处嵌入了许多开始/结束注释。这使得可以将构建文件的位提取到本指南中，以获得上述详细说明。您在生产构建文件中不需要它们。**

> 概要

恭喜！您现在已经创建了一个简单而有效的Gradle构建文件，用于构建Java项目。