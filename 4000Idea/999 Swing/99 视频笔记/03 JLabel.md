# JLabel

JLabel，标签控件，用于显示文本

```java
    public static void main(String[] args) {
        JFrame frame = new JFrame();
        frame.setTitle("Java 第一个 GUI 程序");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel root = new JPanel();
        frame.setContentPane(root);

        JLabel label = new JLabel("你好");
        root.add(label);

        frame.setSize(400, 300);
        frame.setVisible(true);
    }
```



![image-20230426230205045](https://raw.githubusercontent.com/huxiaoning/img/master/20230426230626.png)