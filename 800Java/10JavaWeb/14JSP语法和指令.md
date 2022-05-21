

# JSP语法和指令

- Jsp的page指令
- Jsp的taglib指令 
- Jsp的局部代码块 
- Jsp的全局代码块 
- Jsp的注释
- Jsp的静态引入(JSP的include指令)
- Jsp的动态引入
- 页面转发(forword 标签)



### Jsp的page指令

##### 作用

配置jsp转译为servlet的java文件相关的参数。

```
<%@ page 属性名="属性值" 属性名="属性值" 属性名="属性值" %>
```

```
<%@ page contentType="text/html;charset=UTF-8" %>
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8" %>
```

##### 属性

- language
  - 声明JSP要被转译的语言
- import
  - 声明转译后的java文件要导入的包，不同的包用逗号隔开。
- pageEncoding
  - 设置jsp文件的数据编码格式。
- contentType
  - 设置响应格式，相当于`resp.setContentType("text/html;charset=UTF-8");`
- session
  - 默认true,在转译的servlet中开启session支持。
- errorPage
  - 设置jsp运行错误跳转的页面。
- extends
  - 设置jsp转译的java文件要继承的父类(包名+类名)。



### Jsp的局部代码块 

##### 示例

```jsp
<%
    // 在这里写Java代码
    int a = 2;
    if (a > 3) {
%>
        <b>JSP很简单</b>
<%
    } else {
%>
        <b>JSP很不简单</b>
<%
    }
%>
```

局部代码块中声明的java代码会被原样转译到jsp对应的servlet文件的＿JspService方法中。

缺点是逻辑判断书写麻烦，阅读困难。



### Jsp的全局代码块 

```jsp
<%!
    // 这是一个全局代码块声明

    // 在转译的servlet类中添加一个属性x
    private int x;

    // 在转译的servlet类中添加一个test()方法
    public void test() {
        System.out.println("这是一个全局代码块声明");
    }
%>
```

全局代码块中声明的属性和方法，可以在当前jsp文件中的任意局部代码块中使用。



### JSP的脚本段语句

- <%=表达式%>

```java
<%
    String str = "xx";
%>
<h1><%=str%></h1>
```



`<%=str%>`相当于`<%out.write(str);%>`

`str`可以是任务的变量、方法或其它的表达式。



### Jsp的静态引入(JSP的include指令)

```jsp
<%@ include file="includeStatic.jsp" %>
```

##### `includeStatic.jsp`

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<b>我是静态引入页面</b>
```

会将引入的jsp文件和当前jsp文件转译成一个java文件使用。

在网页中也就显示了合并后的显示效果。

静态引入的jsp文化不会单独转译成java文件。

当前文件和静态引入的jsp文件中不能够使用java代码块声明同名变量。

### Jsp的动态引入

```jsp
<jsp:include page="includeDynamic.jsp"/>
```

##### `includeDynamic.jsp`

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<i>我是动态引入页面</i>
```

会将引入的jsp文件单独转译，在当前文件转译好的java文件中调用引入的jsp文件的转译文件。

在网页中显示合并后的显示效果。

动态引入允许文件中声明同名变量。



### 页面转发(forword标签)

##### 转发示例

a.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head>
    <title>学习JSP动态引入</title>
</head>
<body>
<b>这些内容不会显示，只会显示下面转发的页面,上面的标题也不会显示，只会显示转发页面的标题</b>
<jsp:forward page="forward.jsp"/>
</body>
</html>
```

forward.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head>
    <title>我是转发页面</title>
</head>
<body>
<b>我是转发页面</b>
</body>
</html>
```

浏览器地址不会变化，始终是a.jsp,但显示内容却是forward.jsp里面的内容。

其本质是Servlet的请求转发，那么就可以在jsp页面之间流转数据。

##### 数据流转示例

a.jsp

```jsp
<jsp:forward page="forward.jsp">
    <jsp:param name="xxx" value="aaa"/>
    <jsp:param name="yyy" value="bbb"/>
</jsp:forward>
```

forward.jsp (request是jsp的内置对象可以直接使用)

```jsp
<b><%=request.getParameter("xxx")%></b>
```

其实就是串的参数。`forward.jsp?xxx=aaa&yyy=bbb`



### Jsp的注释

`<%--  --%>`

