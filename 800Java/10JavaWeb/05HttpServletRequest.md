# HttpServletRequest

### 获取所有请求头信息

```java
@WebServlet("/requestServlet")
public class RequestServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Enumeration<String> headerNames = req.getHeaderNames();
        while (headerNames.hasMoreElements()) {
            String headKey = headerNames.nextElement();
            Enumeration<String> headValues = req.getHeaders(headKey);
            while (headValues.hasMoreElements()) {
                String value = headValues.nextElement();
                System.out.println(headKey + " --> " + value);
            }
        }
    }
}

host --> localhost:8080
connection --> keep-alive
cache-control --> max-age=0
sec-ch-ua --> " Not A;Brand";v="99", "Chromium";v="100", "Google Chrome";v="100"
sec-ch-ua-mobile --> ?0
sec-ch-ua-platform --> "Windows"
upgrade-insecure-requests --> 1
user-agent --> Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36
accept --> text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
sec-fetch-site --> none
sec-fetch-mode --> navigate
sec-fetch-user --> ?1
sec-fetch-dest --> document
accept-encoding --> gzip, deflate, br
accept-language --> zh-CN,zh;q=0.9
cookie --> JSESSIONID=B724BEF33A2A1375E7A3B009B6B2607B; _ga=GA1.1.835850808.1623414864
```



### 获取一些常用信息

```java
@WebServlet("/requestServlet")
public class RequestServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1 获取请求头数据
        // 获取请求方式
        String method = req.getMethod();
        System.out.println(method); // GET
        // 获取请求URL
        StringBuffer url = req.getRequestURL();
        System.out.println(url); // http://localhost:8080/web-demo/requestServlet
        String uri = req.getRequestURI();
        System.out.println(uri); // /web-demo/requestServlet
        // 获取协议
        String scheme = req.getScheme();
        System.out.println(scheme); // http
        String protocol = req.getProtocol();
        System.out.println(protocol); // HTTP/1.1
    }
}
```

