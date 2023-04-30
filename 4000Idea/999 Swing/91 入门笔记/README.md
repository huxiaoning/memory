# 入门笔记



```java
import javax.swing.*;

public class HelloWorldSwing {

    private static void createAndShowGUI() {
        JFrame frame = new JFrame();
        frame.setTitle("HelloWorldSwing");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JLabel label = new JLabel("Hello World");
        frame.getContentPane().add(label);

        frame.pack(); // 所有组件以首选大小显示
        frame.setVisible(true); // 显示窗体
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                createAndShowGUI();
            }
        });
    }
}
```

