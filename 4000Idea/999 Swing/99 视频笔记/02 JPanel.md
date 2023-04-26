# JPanel

```java
    public static void main(String[] args) {
        JFrame frame = new JFrame();
        frame.setTitle("Java 第一个 GUI 程序");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel root = new JPanel();
        frame.setContentPane(root);
        root.add(new JButton("测试"));

        frame.setSize(400, 300);
        frame.setVisible(true);
    }
```



![image-20230426230658990](https://raw.githubusercontent.com/huxiaoning/img/master/20230426230659.png)
