# ServletContext

### ServletContext作用

解决不同用户的数据共享问题。

### ServletContext原理

ServletContext 对象由服务器进行创建，一个项目只有一个对象。



### 获取`ServletContext`

##### 1 使用Request对象来获取`ServletContext`

```java
ServletContext servletContext = req.getServletContext();
```

##### 2 使用`Session`对象来获取`ServletContext`

```java
ServletContext servletContext = req.getSession().getServletContext();
```

##### 3 使用`Servlet`对象来获取`ServletContext`

```java
ServletContext servletContext = this.getServletContext();
```

##### 4 使用`ServletConfig`对象来获取`ServletContext`

```java
ServletContext servletContext = this.getServletConfig().getServletContext();
```



### 使用`ServletContext`

##### 1 数据存储与获取

```java
        ServletContext servletContext = req.getServletContext();
        servletContext.setAttribute("", "");
        Object attribute = servletContext.getAttribute("");
```

##### 2 获取项目中`web.xml`文件中的配置

###### 配置`web.xml`

```xml
<web-app>
    <!-- 配置全局数据 -->
    <context-param>
        <param-name>xxx</param-name>
        <param-value>aaa</param-value>
    </context-param>
    <context-param>
        <param-name>yyy</param-name>
        <param-value>bbb</param-value>
    </context-param>
    <!-- 省略其他配置 -->
</web-app>
```

###### 读取`web.xml`中的初始化配置

```java
        ServletContext servletContext = req.getServletContext();
        String aaa = servletContext.getInitParameter("xxx");
        Enumeration<String> initParameterNames = servletContext.getInitParameterNames();
```

##### 3 获取`webapp`目录(WEB-INF目录、index.jsp所在目录)下的资源的绝对路径

```java
@WebServlet("/contextServlet")
public class ContextServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = req.getServletContext();

        // C:\developerInstall\IdeaWorkspace\web-demo\target\web-demo\ 直接使用idea运行时的运行结果
        // C:\developerInstall\Tomcat\apache-tomcat-9.0.63\webapps\web-demo\ 部署在tomcat里面时的运行结果
        String realPath = servletContext.getRealPath("/");
        
        InputStream in = servletContext.getResourceAsStream("/index.jsp");
        System.out.println(realPath);
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(in, "utf-8"));
        for (; ; ) {
            String line = bufferedReader.readLine();
            if (line == null) {
                break;
            }
            System.out.println(line); // 每一行都是index.jsp里面的内容
        }
    }
}
```

