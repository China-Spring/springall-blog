通过OpenJDK JDK 11 Early Access Build 20版本在本文中讲解JDK 11 **String**的六个方法：

- String.repeat(int)
- String.lines()
- String.strip()
- String.stripLeading()
- String.stripTrailing()
- String.isBlank()

[GitHub](https://github.com/dustinmarx/javademos/blob/master/src/dustin/examples/strings/String11Demo.java)上提供了这些示例的源代码  。

> String.repeat（INT）

由于在Groovy中体验了这一功能，因此String.repeat(int)方法提供了在Java中看到的便捷功能。正如它的名字所示，这个方法会重复String它将与int参数提供的次数一起运行。下一个代码演示了如何使用**String.repeat(int)**输出生成标题分隔符。

```java
/** 
 * Write provided {@code String} in header. Note that this 
 * implementation uses {@code String.repeat(int)}. 
 * 
 * @param headerText Title of header. 
 */  
private static void writeHeader(final String headerText)  
{  
   final String headerSeparator = "=".repeat(headerText.length()+4);  
   out.println("\n" + headerSeparator);  
   out.println("= " + headerText + " =");  
   out.println(headerSeparator);  
}
```

writeHeader(String)方法使用String.repeat(int)，可以很容易地从“=”字符中生成足够多的“header分隔符”行，以覆盖所提供的headerText长度，外加4个额外的字符，以允许在“header文本”的每一侧增加一个“=”和额外的空间。writeHeader(String)方法是本文中所有其他演示示例使用的，并将通过这些示例进行演示。

> String.lines()

String.lines()方法通过其行终止符分隔字符串，并返回由这些行终止符分隔的字符串流。

```java
/** 
 * Demonstrate method {@code String.lines()} added with JDK 11. 
 */  
public static void demonstrateStringLines()  
{  
   final String originalString = prepareStringWithLineTerminators();  
   final String stringWithoutLineSeparators  
      = originalString.replaceAll("\\n", "\\\\n");  
   writeHeader("String.lines() on '"  + stringWithoutLineSeparators  + "'");  
   final Stream<String> strings = originalString.lines();  
   strings.forEach(out::println);  
}
```

以下是输出：

```java
String.lises() on Test\nTest1

Test
Test1
```

> String.strip() / String.stripLeading() / String.stripTrailing()

String.strip()、String.stripLeading()和String. striptail()方法修剪目标字符串前面、后面或前面和后面的空白(由[Character.isWhiteSpace()](https://docs.oracle.com/javase/10/docs/api/java/lang/Character.html#isWhitespace(int))决定)。

```java
/** 
 * Demonstrate method {@code String.strip()} added with JDK 11. 
 */  
public static void demonstrateStringStrip()  
{  
   final String originalString = prepareStringSurroundedBySpaces();  
   writeHeader("String.strip() on '" + originalString + "'");  
   out.println("'" + originalString.strip() + "'");  
}  
/** 
 * Demonstrate method {@code String.stripLeading()} added with JDK 11. 
 */  
public static void demonstrateStringStripLeading()  
{  
   final String originalString = prepareStringSurroundedBySpaces();  
   writeHeader("String.stripLeading() on '" + originalString + "'");  
   out.println("'" + originalString.stripLeading() + "'");  
}  
/** 
 * Demonstrate method {@code String.stripTrailing()} added with JDK 11. 
 */  
public static void demonstrateStringStripTrailing()  
{  
   final String originalString = prepareStringSurroundedBySpaces();  
   writeHeader("String.stripTrailing() on '" + originalString + "'");  
   out.println("'" + originalString.stripTrailing() + "'");  
}
```

执行上面的代码时，输​​出看起来像下一个屏幕快照中显示的那样：

```java
String.strip() on "    Test Test    "
'Test Test'

String. stripLeading() on"    Test Test    "
'Test Test    '

String.stripTrailig on "    Test Test    "
'    Test Test'
```

> String.isBlank()

String.isBlank()方法指示目标字符串是否为空，或者仅包含由[Character.isWhitespace(int)](https://docs.oracle.com/javase/10/docs/api/java/lang/Character.html#isWhitespace(int))确定的空白字符。

```java
/** 
 * Demonstrate method {@code String.isBlank()} added with JDK 11. 
 */  
public static void demonstrateStringIsBlank()  
{  
   writeHeader("String.isBlank()");  
   final String emptyString = "";  
   out.println("Empty String -> " + emptyString.isBlank());  
   final String onlyLineSeparator = System.getProperty("line.separator");  
   out.println("Line Separator Only -> " + onlyLineSeparator.isBlank());  
   final String tabOnly = "\t";  
   out.println("Tab Only -> " + tabOnly.isBlank());  
   final String spacesOnly = "   ";  
   out.println("Spaces Only -> " + spacesOnly.isBlank());  
}
```

执行上面的代码时，输​​出看起来像下一个屏幕快照中显示的那样：

```java
String. isBlank( )
Empty String -> true
Line Separator Only -> true
Tab Only -> true
Spaces Only -> true
```

上面显示的一些方法称为辅助方法，可以在[GitHub](https://github.com/dustinmarx/javademos/blob/master/src/dustin/examples/strings/String11Demo.java)上看到。

JDK 11的字符串中添加的方法是很小的添加，但是与之前Java字符串相关的某些任务比更容易，并且减少了对第三方库的需求。