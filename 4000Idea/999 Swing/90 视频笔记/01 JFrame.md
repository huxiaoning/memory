# JFrame

JFrame 下的几个API：

- setTitle （title）设置窗口标题

- setSize（width，hight）设置窗口大小，单位像素 

- setVisiable（visible）显示／隐藏窗口

```java
    public static void main(String[] args) {
        JFrame jFrame = new JFrame();
        jFrame.setTitle("Java 第一个 GUI 程序");
        jFrame.setSize(600, 300);
        jFrame.setVisible(true);
        jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    }
```



![image-20230426230733980](https://raw.githubusercontent.com/huxiaoning/img/master/20230426230734.png)
