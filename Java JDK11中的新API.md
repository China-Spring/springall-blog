有关API更改的完整列表，可在[Github](https://gunnarmorling.github.io/jdk-api-diff/jdk10-jdk11-api-diff.html)上获得。

这里列出的是除了java.net.http和jdk.jfr模块之外的所有新方法。没有列出java.security模块中的新方法和类，它们特定于JEP 324和JEP 329的更改（有六个新类和八个新方法）。

> java.io.ByteArrayOutputStream

**void writeBytes(byte[])**: 将参数的所有字节写入输出流

> java.io.FileReader

两个允许指定Charset的新构造函数。

> java.io.FileWriter

四个允许指定Charset的新构造函数。

> java.io.InputStream

**io.InputStream nullInputStream()**: 返回不读取任何字节的InputStream。当你第一次看到这个方法（以及OutputStream，Reader和Writer中的方法）时，你会想知道它们有什么用处。您可以将它们视为/dev/null，以丢弃您不需要的输出或提供始终返回零字节的输入。

> java.io.OutputStream

**io.OutputStream nullOutputStream()**

> java.io.Reader

**io.Reader nullReader()**

> java.io.Writer

**io.Writer nullWriter()**

> java.lang.Character

**String toString(int)**: 这是现有方法的重载形式，但是使用int而不是char。int是Unicode代码点。

> java.lang.CharSequence

**int compare(CharSequence, CharSequence)**: 按字典顺序比较两个CharSequence实例。如果第一个序列按字典顺序小于，等于或大于第二个序列，则返回负值，零或正值。

> java.lang.ref.Reference

**lang.Object clone()**: Reference类不实现Cloneable接口，并且此方法将始终抛出CloneNotSupportedException。必须有一个理由将其包含在内，大概是为了将来的某些东西。

> java.lang.Runtime
> java.lang.System

这里没有新方法，但值得一提的是，现在已从这两个类中删除了runFinalizersOnExit()方法（这可能是兼容性问题）

> java.lang.String

我认为这是JDK 11中新API的亮点之一。这里有几个有用的新方法。

- **boolean isBlank()**: 如果字符串为空或仅包含空格代码点，则返回true，否则返回false。
- **Stream lines()**: 返回从此字符串中提取的行的流，由行终止符分隔。
- **String repeat(int)**: 返回一个字符串，其值是此字符串重复计数次数的串联。
- **String strip()**: 返回一个字符串，其值为此字符串，删除了所有前导和尾随空格。
- **String stripLeading()**: 返回一个字符串，其值为此字符串，并删除所有前导空格。
- **String stripTrainling()**: 返回一个字符串，其值为此字符串，并删除所有尾随空格。

您可能会看看strip()并询问这与现有的trim()方法有何不同？答案是如何定义空白在两者之间有所不同。

> java.lang.StringBuffer
> java.lang.StringBuilder

这两个类都有一个新的compareTo()方法，它接受一个StringBuffer/StringBuilder 并返回一个int。比较方法与CharSequence的新compareTo()方法相同。

> java.lang.Thread

没有其他方法，但已删除destroy()和stop(Throwable) 方法。不带参数的stop()方法仍然存在。这可能会出现兼容性问题。

> java.nio.ByteBuffer
> java.nio.CharBuffer
> java.nio.DoubleBuffer
> java.nio.FloatBuffer
> java.nio.LongBuffer
> java.nio.ShortBuffer

所有这些类现在都有一个mismatch()方法，它找到并返回此缓冲区与给定缓冲区之间第一个不匹配的相对索引。

> java.nio.channels.SelectionKey

- **int interestOpsAnd(int)**:以原子方式将此键的兴趣集设置为现有兴趣集和给定值的按位交集（和）
- **int interestOpsOr(int)**:以原子方式将此键的兴趣集设置为现有兴趣集和给定值的按位联合（或）。

> java.nio.channels.Selector

- **int select(java.util.function.Consumer, long)**: 对相应通道进行I/O操作的键选择并执行操作。long参数是超时时间。
- **int select(java.util.function.Consumer)**:如上所述，除了没有超时时间。
- **int selectNow(java.util.function.Consumer)**:如上所述，除非它是非阻塞的。

> java.nio.file.Files

- **String readString(Path)**:将文件中的所有内容读入字符串，使用UTF-8字符集从字节到字符进行解码。
- **String readString(Path, Charset)**:如上所述，使用指定的Charset从字节到字符的解码。
- **Path writeString(Path, CharSequence, java.nio.file. OpenOption[])**:将CharSequence写入文件。使用UTF-8字符集将字符编码为字节。
- **Path writeString(Path, CharSequence, java.nio.file. Charset, OpenOption[])**: 如上所述，使用指定的Charset将字符编码为字节。

> java.nio.file.Path

- **Path of(String, String[])**: 通过转换路径字符串或连接时形成路径字符串的字符串序列来返回Path。
- **Path of(net.URI)**: 通过转换URI返回路径。

> java.util.Collection

**Object[] toArray(java.util.function.IntFunction)**: 使用提供的生成器函数返回包含此集合中所有元素的数组，以分配返回的数组。

> java.util.concurrent.PriorityBlockingQueue
> java.util.PriorityQueue

- **void forEach(java.util.function.Consumer)**:对Iterable的每个元素执行给定的操作，直到处理完所有元素或操作抛出异常为止。
- **boolean removeAll(java.util.Collection)**:删除此集合的所有元素，这些元素也包含在指定的集合中（可选操作）。
- **boolean removeIf(java.util.function.Predicate)**:删除此集合中满足给定谓词的所有元素。
- **boolean retainAll(java.util.Collection)**:仅保留此集合中包含在指定集合中的元素（可选操作）。

> java.util.concurrent.TimeUnit

**long convert(java.time.Duration)**:将给定的持续时间转换为此单位。

> java.util.function.Predicate

返回一个Predicate，**Predicate**是对所提供Predicate的否定。:

```java
lines.stream()   
         .filter(s -> !s.isBlank())
```

同等于

```java
lines.stream()
         .filter(Predicate.not(String::isBlank))
```

如果我们使用静态导入，它将变为：

```java
lines.stream()
          .filter(not(String::isBlank))
```

就个人而言，我认为这个版本更易读，更容易理解。

> java.util.Optional
> java.util.OptionalInt
> java.util.OptionalDouble
> java.util.OptionalLong

**boolean isEmpty()**:如果某个值不存在，则返回true，否则返回false。

> java.util.regex.Pattern

**Predicate asMatchPredicate(**):我认为这可能是新JDK 11 API中隐藏的内容。创建一个Predicate，测试此模式是否与给定的输入字符串匹配。

> java.util.zip.Deflater

- **int deflate(ByteBuffer)**:压缩输入数据并用压缩数据填充指定的缓冲区。
- **int deflate(ByteBuffer, int)**:压缩输入数据并用压缩数据填充指定的缓冲区。返回压缩的实际数据字节数。
- **void setDictionary(ByteBuffer)**:将压缩的预设字典设置为给定缓冲区中的字节。这是现有方法的重载形式，现在可以接受ByteBuffer而不是字节数组。
- **void setInput(ByteBuffer)**:设置压缩的输入数据。也是现有方法的重载形式。

> java.util.zip.Inflater

- **int inflate(ByteBuffer)**:将字节解压缩到指定的缓冲区中。返回未压缩的实际字节数。
- **void setDictionary(ByteBuffer)**:将预设字典设置为给定缓冲区中的字节。现有方法的重载形式。
- **void setInput(ByteBuffer)**:设置解压缩的输入数据。现有方法的重载形式。

> javax.print.attribute.standard.DialogOwner

这是JDK 11中的一个新类，它是一个属性类，用于支持请求打印或页面设置对话框保持显示在所有窗口或某个特定窗口的顶部。

> javax.swing.DefaultComboBoxModel
> javax.swing.DefaultListModel

- **void addAll(Collection)**:添加集合中存在的所有元素。
- **void addAll(int, Collection)**:从指定的索引开始添加集合中存在的所有元素。

> javax.swing.ListSelectionModel

- **int[] getSelectedIndices()**:按递增顺序返回选择模型中所有选定索引的数组。
- **int getSelectedItemsCount()**:返回所选项的数量。

> jdk.jshell.EvalException

**jshell.JShellException getCause()**:返回此EvalException表示的执行客户端中throwable的包装原因，如果原因不存在或未知，则返回null。