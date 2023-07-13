# 编译依赖第三方jar包的程序

##### 先看下代码

```bash
$ cat source/a/HelloWorld.java 
package a;

import org.apache.commons.lang3.StringUtils;

/**
 *  * @author huxiaoning
 *   */
public class HelloWorld {
    public String hello() {
        String s = "Hello World";
        boolean blank = StringUtils.isBlank(s);
        if (blank) {
            return "Hello Blank";
        }
        return s;
    }
}

$ cat source/b/Main.java 
package b;

import a.HelloWorld;

/**
 *  * @author huxiaoning
 *   */
public class Main {
    public static void main(String[] args) {
        HelloWorld helloWorld = new HelloWorld();
        String hello = helloWorld.hello();
        System.out.println("hello = " + hello);
    }
}
```



##### 编译

```bash
$ javac -cp lib/* -d class source/a/HelloWorld.java source/b/Main.java
$ javac -cp lib/commons-lang3-3.8.1.jar -d class source/a/HelloWorld.java source/b/Main.java
```

可以用`*`通配符，也可以指定`jar`包名称，

多个`jar`包用`;`或`:`分隔。

- windows用`;`
- linux用`:`

##### 执行

```bash
$ cd class
$ java -cp .:../lib/* b/Main
hello = Hello World
$ java -cp .:../lib/commons-lang3-3.8.1.jar b/Main
hello = Hello World
```

