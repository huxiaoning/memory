# JSTL标签库

[菜鸟教程](https://www.runoob.com/jsp/jsp-jstl.html)

需要引入依赖`jstl-1.2.jar`

```xml
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
```

JSTL是apache对EL表达式的扩展（也就是说JSTL依赖 EL），JSTL 是标签语言！JSTL标签使用以来非常方便，

它与JSP动作标签一样，只不过它不是JSP内置的标签，需要我们自己导包，以及指定标签库而已！



- JSTL的核心标签库(重点)
- JSTL的格式化标签库
- JSTL的SQL标签库
- JSTL的函数标签库
- JSTL的XML标签库



### JSTL的核心标签库

- 引入jar包

  - ```xml
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>jstl</artifactId>
                <version>1.2</version>
            </dependency>
    ```

- 在jsp文件中声明核心标签库

  - ```jsp
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
    ```

- [内容](https://www.runoob.com/jsp/jsp-jstl.html)



| 标签                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [<c:out>](https://www.runoob.com/jsp/jstl-core-out-tag.html) | 用于在JSP中显示数据，就像<%= ... >                           |
| [<c:set>](https://www.runoob.com/jsp/jstl-core-set-tag.html) | 用于保存数据                                                 |
| [<c:remove>](https://www.runoob.com/jsp/jstl-core-remove-tag.html) | 用于删除数据                                                 |
| [c:catch](https://www.runoob.com/jsp/jstl-core-catch-tag.html) | 用来处理产生错误的异常状况，并且将错误信息储存起来           |
| [<c:if>](https://www.runoob.com/jsp/jstl-core-if-tag.html)   | 与我们在一般程序中用的if一样                                 |
| [<c:choose>](https://www.runoob.com/jsp/jstl-core-choose-tag.html) | 本身只当做<c:when>和<c:otherwise>的父标签                    |
| [<c:when>](https://www.runoob.com/jsp/jstl-core-choose-tag.html) | <c:choose>的子标签，用来判断条件是否成立                     |
| [<c:otherwise>](https://www.runoob.com/jsp/jstl-core-choose-tag.html) | <c:choose>的子标签，接在<c:when>标签后，当<c:when>标签判断为false时被执行 |
| [<c:import>](https://www.runoob.com/jsp/jstl-core-import-tag.html) | 检索一个绝对或相对 URL，然后将其内容暴露给页面               |
| [<c:forEach>](https://www.runoob.com/jsp/jstl-core-foreach-tag.html) | 基础迭代标签，接受多种集合类型                               |
| [<c:forTokens>](https://www.runoob.com/jsp/jstl-core-foreach-tag.html) | 根据指定的分隔符来分隔内容并迭代输出                         |
| [<c:param>](https://www.runoob.com/jsp/jstl-core-param-tag.html) | 用来给包含或重定向的页面传递参数                             |
| [<c:redirect>](https://www.runoob.com/jsp/jstl-core-redirect-tag.html) | 重定向至一个新的URL.                                         |
| [c:url](https://www.runoob.com/jsp/jstl-core-url-tag.html)   | 使用可选的查询参数来创造一个URL                              |



### JSTL的格式化标签库

```jsp
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
```

| 标签                                                         | 描述                                     |
| :----------------------------------------------------------- | :--------------------------------------- |
| [<fmt:formatNumber>](https://www.runoob.com/jsp/jstl-format-formatnumber-tag.html) | 使用指定的格式或精度格式化数字           |
| [<fmt:parseNumber>](https://www.runoob.com/jsp/jstl-format-parsenumber-tag.html) | 解析一个代表着数字，货币或百分比的字符串 |
| [<fmt:formatDate>](https://www.runoob.com/jsp/jstl-format-formatdate-tag.html) | 使用指定的风格或模式格式化日期和时间     |
| [<fmt:parseDate>](https://www.runoob.com/jsp/jstl-format-parsedate-tag.html) | 解析一个代表着日期或时间的字符串         |
| [<fmt:bundle>](https://www.runoob.com/jsp/jstl-format-bundle-tag.html) | 绑定资源                                 |
| [<fmt:setLocale>](https://www.runoob.com/jsp/jstl-format-setlocale-tag.html) | 指定地区                                 |
| [<fmt:setBundle>](https://www.runoob.com/jsp/jstl-format-setbundle-tag.html) | 绑定资源                                 |
| [<fmt:timeZone>](https://www.runoob.com/jsp/jstl-format-timezone-tag.html) | 指定时区                                 |
| [<fmt:setTimeZone>](https://www.runoob.com/jsp/jstl-format-settimezone-tag.html) | 指定时区                                 |
| [<fmt:message>](https://www.runoob.com/jsp/jstl-format-message-tag.html) | 显示资源配置文件信息                     |
| [<fmt:requestEncoding>](https://www.runoob.com/jsp/jstl-format-requestencoding-tag.html) | 设置request的字符编码                    |


