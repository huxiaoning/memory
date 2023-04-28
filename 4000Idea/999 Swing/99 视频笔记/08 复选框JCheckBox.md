# 复选框JCheckBox

```java
JCheckBox checkBox = new JCheckBox("同意用户协议");
checkBox.setSelected(true);
boolean selected = checkBox.isSelected();
checkBox.addActionListener(...); // 勾选或取消获取事件
```



##### 示例

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

        JCheckBox checkBox = new JCheckBox("同意用户协议");
        root.add(checkBox);


        JButton button = new JButton("下一步");
        root.add(button);

        // 初始化界面
        checkBox.setSelected(false);
        button.setEnabled(false);

        checkBox.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                boolean selected = checkBox.isSelected();
                button.setEnabled(selected);
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

![image-20230428224621124](https://raw.githubusercontent.com/huxiaoning/img/master/20230428224621.png)

当用户勾选时，下一步按钮就启用了。