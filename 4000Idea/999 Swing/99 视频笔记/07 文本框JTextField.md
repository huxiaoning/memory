# 文本框JTextField

JTextField，单行文本框
例如，

```java
JTextField textField = new JTextField(); 
```

或

```java
JTextField textField = new JTextField(20);
```


其中，20表示初始化为20个英文字符的宽度。

可以在单行文本框中输入内容。

## API

设置文本
```java
textField.setText(str)
```

获取文本

```java
str =textField.getText()
```



```java
package org.example;

import javax.swing.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class MyFrame extends JFrame {

    public MyFrame(String title) {
        super(title);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setSize(400, 300);

        JPanel root = new JPanel();
        setContentPane(root);

        JTextField textField = new JTextField(20);
        root.add(textField);

        textField.setText("你好");

        JButton button = new JButton("获取文本框中的文本显示在下面的Label上");
        root.add(button);

        JLabel label = new JLabel();
        root.add(label);


        button.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String text = textField.getText();
                label.setText(text);
            }
        });
    }
}
```

```java
    public static void main(String[] args) {
        JFrame frame = new MyFrame("我是标题");
        frame.setVisible(true);
    }
```

