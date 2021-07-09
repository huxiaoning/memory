# `SHELL`基础

## 什么是`Shell`

`Shell`是一个命令行解释器，它为用户提供了一个向`Linux`内核发送请求以便运行程序的界面系统级程序。
用户可以用Shell来启动、挂起、停止甚至是编写一些程序。
`Shell`还是一个功能相当强大的编程语言，易编写，易调试，灵活性较强。

`Shell`是解释执行的脚本语言，在`Shell`中可以直接调用Linux系统命令。

echo $SHELL

bash   	-- 进入子Shell<父Shell子Shell的概念:个人猜测，登录mysql后的提示符界面也是一个子Shell>

cat /etc/shells

```bash
# 输出当前的默认SHELL(是Bash)
$ echo $SHELL
/bin/bash

# 进入子Shell
$ bash

# 查看系统支持哪些Shell
$ cat /etc/shells
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
```

