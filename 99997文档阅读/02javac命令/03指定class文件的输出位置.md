# 指定class文件的输出位置

##### 看下源码

```bash
$ cat source/a/HelloWorld.java 
package a;

/**
 *  * @author huxiaoning
 *   */
public class HelloWorld {
    public String hello() {
        return "Hello World";
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
$ javac -d class source/a/HelloWorld.java source/b/Main.java

$ ls class/a/HelloWorld.class 
class/a/HelloWorld.class
$ ls class/b/Main.class 
class/b/Main.class

```

##### 执行

```bash
$ cd class
$ java b/Main
```

执行位置必须在class目录下

(有jar包了就不需要这么麻烦了)