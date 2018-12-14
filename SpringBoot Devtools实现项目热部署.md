我们在开发SpringBoot项目的时候，有些时候修改了一些Controller或者Service等组件，那么每次修改都需要去重启服务，这样的话严重的导致我们的开发效率降低，那么SpringBoot为我们提供了该问题的解决方案，那就是进行热部署，我们热部署使用到的组件是devtools。

- 修改pom文件增加maven的devtools依赖

```xml
<!-- 引入热部署依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```
    
> 注意：`true`只有设置为true时才会热启动，即当修改了html、css、js等这些静态资源后不用重启项目直接刷新即可。

- 修改springboot插件配置

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <fork>true</fork>
    </configuration>
</plugin>
```

> 配置了`true`后在修改java文件后也就支持了热启动，不过这种方式是属于项目重启（速度比较快的项目重启），会清空session中的值，也就是如果有用户登陆的话，项目重启后需要重新登陆。

- 设置IDEA编辑器自动编译功能,进入IDEA的配置项中,选择顶部菜单的 IntelliJ IDEA -> Perferences... 会弹出一个设置对话框

![](http://image.cdn.ttxit.com/15295982124268.jpg)
    
- 在弹出的对话框中点击`Build, Execution, Deployment`选项下的`Compiler`选项

![](http://image.cdn.ttxit.com/15296011761635.jpg)

- 勾选`Compiler`选项中的`Build project automatically`选项开启IDEA自动编译项目,然后点击`OK`即可

![](http://image.cdn.ttxit.com/15296012541833.jpg)