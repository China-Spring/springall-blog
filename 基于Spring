在本文中，我们将展示如何根据Spring Security中定义的用户角色过滤JSON序列化输出。

#### 为什么我们需要过滤？

---

让我们考虑一个简单但常见的用例，我们有一个Web应用程序，为不同角色的用户提供服务。例如，这些角色为User和Admin。

首先，让我们定义一个要求，即**Admin可以完全访问**通过公共REST API公开的对象的内部状态。相反，**User用户应该只看到一组预定义的对象属性**。

我们将使用Spring Security框架来防止对Web应用程序资源的未授权访问。

让我们定义一个对象，我们将在API中作为REST响应返回数据：

```java
class Item {
    private int id;
    private String name;
    private String ownerName;
 
    // getters
}
```

当然，我们可以为应用程序中的每个角色定义一个单独的数据传输对象类。但是，这种方法会为我们的代码库引入无用的重复或复杂的类层次结构。

另一方面，我们可以使用Jackson库的JSON View功能。正如我们将在下一节中看到的那样，它使得自定义JSON表示就像在字段上添加注释一样简单。

#### @JsonView注释

---

Jackson库支持通过使用@JsonView注解标记我们想要包含在JSON表示中的字段来定义**多个序列化/反序列化上下文**。此注解具有Class类型的必需参数，用于区分上下文。

使用@JsonView在我们的类中标记字段时，我们应该记住，默认情况下，序列化上下文包括未明确标记为视图一部分的所有属性。为了覆盖此行为，我们可以禁用DEFAULT_VIEW_INCLUSION映射器功能。

首先，让我们定义一个带有一些内部类的View类，我们将它们用作@JsonView注解的参数：

```java
class View {
    public static class User {}
    public static class Admin extends User {}
}
```

接下来，我们将@JsonView注解添加到我们的类中，使ownerName只能访问admin角色：

```java
@JsonView(View.User.class)
private int id;
@JsonView(View.User.class)
private String name;
@JsonView(View.Admin.class)
private String ownerName;
```

#### 如何将@JsonView注解与Spring Security 集成

---

现在，让我们添加一个包含所有角色及其名称的枚举。之后，让我们介绍JSONView和安全角色之间的映射：

```java
enum Role {
    ROLE_USER,
    ROLE_ADMIN
}
 
class View {
 
    public static final Map<Role, Class> MAPPING = new HashMap<>();
 
    static {
        MAPPING.put(Role.ADMIN, Admin.class);
        MAPPING.put(Role.USER, User.class);
    }
 
    //...
}
```

最后，我们来到了整合的中心点。为了绑定JSONView和Spring Security角色，我们需要定义适用于我们应用程序中所有控制器方法的控制器。

到目前为止，我们唯一需要做的就是覆盖**AbstractMappingJacksonResponseBodyAdvice类的  beforeBodyWriteInternal方法**：

```java
@RestControllerAdvice
class SecurityJsonViewControllerAdvice extends AbstractMappingJacksonResponseBodyAdvice {
 
    @Override
    protected void beforeBodyWriteInternal(
      MappingJacksonValue bodyContainer,
      MediaType contentType,
      MethodParameter returnType,
      ServerHttpRequest request,
      ServerHttpResponse response) {
        if (SecurityContextHolder.getContext().getAuthentication() != null
          && SecurityContextHolder.getContext().getAuthentication().getAuthorities() != null) {
            Collection<? extends GrantedAuthority> authorities
              = SecurityContextHolder.getContext().getAuthentication().getAuthorities();
            List<Class> jsonViews = authorities.stream()
              .map(GrantedAuthority::getAuthority)
              .map(AppConfig.Role::valueOf)
              .map(View.MAPPING::get)
              .collect(Collectors.toList());
            if (jsonViews.size() == 1) {
                bodyContainer.setSerializationView(jsonViews.get(0));
                return;
            }
            throw new IllegalArgumentException("Ambiguous @JsonView declaration for roles "
              + authorities.stream()
              .map(GrantedAuthority::getAuthority).collect(Collectors.joining(",")));
        }
    }
}
```

这样，我们的应用程序的每个响应都将通过这个路由，它将根据我们定义的角色映射找到合适的返回结果。请注意，**此方法要求我们在处理具有多个角色的用户时要小心**。
