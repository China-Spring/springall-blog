本指南将引导您使用IntelliJ IDEA构建其中一个入门指南。

> 你要构建什么

您将选择一个Spring指南并将其导入IntelliJ IDEA。然后，您可以阅读指南，处理代码并运行项目。

> 你需要什么

- 大约15分钟
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download/)
- [JDK 6](http://www.oracle.com/technetwork/java/javase/downloads/index.html)或更高版本

> 安装IntelliJ IDEA

如果您还没有安装IntelliJ IDEA（Ultimate Edition），请访问上面的链接。从那里，您可以下载适用于您的平台的副本。要安装它，只需解压缩下载的存档。

完成后，继续启动IntelliJ IDEA。

> 导入入门指南

要导入现有项目，您需要一些代码，因此克隆或复制其中一个入门指南，例如[构建RESTful Web服务](https://www.springall.com.cn/java/gou_jian_restful_web_fu_wu.html)：

```bash
$ git clone https://github.com/spring-guides/gs-rest-service.git
```

启动并运行IntelliJ IDEA后，单击**Import Project**上的**File | Open**：

![](http://image.cdn.ttxit.com/15357058119468.png)

在弹出的对话框中，确保在完整文件夹下选择[Maven](https://www.springall.com.cn/java/shi_yong_maven_gou_jian_java_xiang_mu.html)的pom.xml或[Gradle](https://www.springall.com.cn/java/shi_yong_gradle_gou_jian_java_xiang_mu.html)的build.gradle文件：

![](http://image.cdn.ttxit.com/15357059649932.png)

IntelliJ IDEA将创建一个项目，指南中的所有代码都可以运行。

> 从头开始创建项目

如果您想从一个空项目开始并通过指南复制并粘贴您的方式，请在项目向导中创建一个新的**Maven**或**Gradle**项目：

![](http://image.cdn.ttxit.com/15357059986422.png)