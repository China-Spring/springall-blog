Java JDK 11删除了CORBA，Java EE和JavaFX支持，但添加了十几个主要新功能。

Java Development Kit（JDK）11现已普遍可用，可供生产使用，提高了工作效率，并提供了实现HTTP/2的HTTP客户端API。

Java Standard Edition（SE）11有16个主要功能更改。Java 11还通过删除CORBA和[Java EE](https://www.infoworld.com/article/3226777/java/java-ee-8-is-here-what-you-need-to-know.html)（最近[更名为Jakarta EE](https://www.infoworld.com/article/3254191/java/vote-now-for-enterprise-javas-new-name.html)）模块以及[删除JavaFX](https://www.infoworld.com/article/3261066/java/javafx-will-be-removed-from-the-java-jdk.html)而失去了一些功能 ，[JavaFX](https://www.infoworld.com/article/3261066/java/javafx-will-be-removed-from-the-java-jdk.html)现在可作为独立技术使用。

在Java 11中，Oracle已将主线存储库**jdk/jdk**分叉到**jdk/jdk11**稳定存储库。推送到**jdk/jdk**或**jdk/client**的更改现在标记为JDK 12.稳定存储库可以接受选定的错误修复，如果获得批准，则可以根据[JDK发布过程](http://openjdk.java.net/jeps/3)接受后期增强。

Oracle标准Java实现的最新版本是一个[长期支持(LTS)版本](https://www.infoworld.com/article/3223690/java/java-9-will-not-receive-long-term-support.html)，它将得到Oracle至少8年的商业支持。到2026年将提供Bug修复和安全更新。新的LTS版本每三年发布一次，JDK 17将于2021年发布，这将是下一个LTS版本。中期版本将每六个月发布一次。

> 哪里可以下载JDK 11

您可以从Oracle Technology Network [下载JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。

> Java 11 JDK中的新功能

JDK 11有16个新功能：

- 通过**lang.Math**在Aarch64处理器上实现sin，cos和log函数的新内在函数，改进Aarch64内在函数。该提案强调专用的CPU体系结构特定的代码模式，可提高应用程序和基准性能。
- 基于嵌套的访问控制引入了嵌套，这是一种与Java语言中嵌套类型概念一致的访问控制上下文。嵌套允许逻辑上属于同一代码实体的类，但编译为不同的类文件以访问彼此的私有成员，而无需编译器插入可访问性扩展桥接方法。
- [传输层安全性（TLS）1.3](https://www.infoworld.com/article/3098868/encryption/reworked-openssl-on-track-for-government-validation.html)，其中TLS协议的这种大修将适用于JDK 11，从而提供显着的安全性和性能优势。但是，没有目标支持TLS 1.3的每个功能。为了最大限度地降低不兼容的风险，TLS 1.3默认会实现向后兼容模式。应用程序可以根据需要关闭或打开此模式。
- [Nashorn JavaScript](https://www.infoworld.com/article/3279893/javascript/nashorn-javascript-engine-for-jvm-could-be-axed.html)引擎与JJS工具的弃用，旨在将来删除它们。鉴于ECMAScript语言构建和API的快速调整和修改，Oracle发现Nashorn难以维护。
- HTTP客户端（标准），它标准化了JDK 9中引入并在JDK 10中更新的孵化HTTP API客户端。API提供非阻塞请求和响应语义CompleteableFutures，可以链接到触发器依赖操作。在JDK 9和10中孵化之后，现在异步的实现几乎已经完全重写.RX Flow概念已经被推入实现中，消除了支持HTTP / 2所需的许多自定义概念。现在可以更轻松地跟踪数据流，从用户级请求发布者和响应发布者到底层套接。这降低了复杂性并最大化了HTTP / 1和HTTP / 2之间重用的可能性。
- 被称为“无操作”收集器的[Epsilon垃圾收集器](https://www.infoworld.com/article/3176322/java/java-garbage-collector-proposal-aimed-at-performance-testing.html)将处理内存分配，而不实现任何实际的内存回收机制。Epsilon的用例包括性能测试，内存压力和虚拟机接口。它也可以用于短期工作。
- lambda参数的局部变量语法应该将隐式类型表达式中的形式参数声明的语法与局部变量声明的语法对齐。这将允许var 在声明隐式类型的lambda表达式的形式参数时使用。
- 将扩展Java类文件格式以支持新的常量池形式**CONSTANT_Dynamic**。目标是降低开发新形式的可实现类文件约束的成本和中断。
- 与现有的椭圆曲线Diffie-Hellman方案相比，与Curve25519和Curve448密码学的密切协议应该更加有效和安全。根据IETF的说法，两条椭圆曲线Curve25510和Curve448可以实现恒定时间实现和无异常的标量乘法，这种乘法更能抵抗一系列的旁道攻击，包括定时和缓存攻击。该提案的目标包括API和密钥协商方案的实现，以及独立于平台的全Java实现的开发。但是，作为提案的一部分，模块化算法实现的复杂性和微妙性存在风险。
- Flight Recorder将提供一个低开销的数据收集框架，用于对Java应用程序和HotSpot JVM进行故障排除。Flight Recorder已成为Oracle商业JDK的一项功能，但其源代码将移至开放式存储库以使该功能普遍可用。Iclouded将是用于生成和使用数据作为事件的API，提供缓冲机制和二进制数据格式以及启用事件的配置和过滤。该提案还要求为OS，HotSpot和JDK库提供事件。
- 升级平台API以支持[Unicode版本10.0](http://unicode.org/versions/Unicode10.0.0/)，从而使Java保持最新。预计将在以下类别中提供支持：

    - Character, String在lang包中
    - NumericShaper在awt.font包中
    - Bidi，BreakIterator和Normalizer在text包

- 实施ChaCha20和Poly1305加密算法。ChaCha2020是一种相对较新的流密码，可以取代旧的，不安全的R4流密码。ChaCha20将与Poly1305验证器配对。将提供ChaCha20和ChaCha20-Poly1305密码实现，使用crypto.CipherSpiAPI 在SunJCE（Java密码术扩展）提供程序中实现算法。
- 增强Java启动程序以运行作为Java源代码的单个文件提供的程序，因此这些程序可以直接从源代码运行。在学习Java的早期阶段编写小实用程序或开发人员时，单文件程序很常见。此外，单个源文件可能会编译为多个类文件，这会增加打包开销。在这些情况下，必须在运行之前编译程序只是基于传统的不必要的步骤。
- 低开销堆分析，提供了一种可以通过JVM工具接口访问Java堆分配的方法。这项工作的目标是以低开销的方式获取有关这些分配的信息，可以通过编程接口访问，并可以对所有分配进行采样。实施独立性和提供关于实时和死堆的数据也是目标。糟糕的堆管理可能导致堆耗尽和垃圾收集颠簸。解决此问题的大多数工具都缺少特定分配的调用站点，这些信息对于调试内存问题至关重要。
- Pack200和Unpack200工具以及Pack200 API的弃用util.jar。Pack200是.jar文件的压缩方案，旨在降低应用程序打包，传输和交付的磁盘和带宽要求。项目负责人表示，维护成本和低使用率并不能证明其保留是合理的。
- [Z垃圾收集器(ZGC)](https://www.infoworld.com/article/3235391/java/zgc-large-heap-java-garbage-collector-may-go-open-source.html)是一种实验性的、低延迟的垃圾收集器，用于处理大小从相对较小的堆到非常大的堆，大小为许多tb级的堆。通过使用ZGC，暂停时间不应超过10ms，与使用G1收集器相比，应用程序吞吐量减少不应超过15%。ZGC还为将来的特性和优化打下基础。Linux/x64将是第一个获得ZGC支持的平台。