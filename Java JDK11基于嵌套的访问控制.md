Java（和其他语言）通过内部类支持嵌套类。要使其正常工作，需要编译器执行一些技巧。这是一个例子：

```java
public class Outer {
    private int outerInt;

     class Inner {
       public void printOuterInt() {
         System.out.println("Outer int = " + outerInt);
       }
    }
}
```

在执行编译之前，编译器会修改它以创建类似的东西：

```java
public class Outer {
  private int outerInt;

  public int access$000() {
    return outerInt; 
  }

}

class Inner$Outer {

  Outer outer;

  public void printOuterInt() {
    System.out.println("Outer int = " + outer.access$000());
  }
}
```

虽然从逻辑上讲，内部类是与外部类相同的代码实体的一部分，但它被编译为一个单独的类。因此，它需要编译器创建合成桥接方法，以提供对外部类的私有字段的访问。

这个JEP引入了巢的概念，其中同一巢的两个成员（我们的例子中的外部和内部）是同窝。为类文件格式NestHost和NestMembers定义了两个新属性。这些更改对于支持嵌套类并编译为字节码的其他语言非常有用。

此功能为java.lang.Class引入了三个新方法：

- Class getNestHost()
- Class[] getNestMembers()
- boolean isNestmateOf(Class)

此功能还需要更改Java虚拟机规范（JVMS），特别是第5.4.4节“访问控制”。