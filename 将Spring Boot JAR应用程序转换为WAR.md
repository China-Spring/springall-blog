Spring Boot附带两个强大的插件：

- spring-boot-gradle-plugin
- spring-boot-maven-plugin

它们本质上都具有功能奇偶校验，并且能够从命令行运行Spring Boot应用程序以及捆绑可运行的JAR。在执行阶段的几乎所有指南中都提到了这个主题。

一个受欢迎的主题是许多人仍然希望生成要在容器内部署的WAR文件。这两个插件都支持这一点。实质上，您必须重新配置项目以生成WAR文件，并将嵌入式容器依赖项声明为“provided”。这可确保相关的嵌入式容器依赖项不包含在WAR文件中。

有关如何配置应用程序以为容器创建WAR文件的详细步骤，请参阅：

- [使用Maven打包可执行jar和war文件](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#build-tool-plugins-maven-packaging)
- [Spring Boot Gradle插件](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#build-tool-plugins-gradle-plugin)
- [Gradle Plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html/#packaging-executable-wars)

**Spring Boot在servlet 3.0规范容器上运行。**