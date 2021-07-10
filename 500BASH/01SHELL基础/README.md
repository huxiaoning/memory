# `SHELL`基础

## 什么是`Shell`

`Shell`是一个命令行解释器，它为用户提供了一个向`Linux`内核发送请求以便运行程序的界面系统级程序。
用户可以用Shell来启动、挂起、停止甚至是编写一些程序。
`Shell`还是一个功能相当强大的编程语言，易编写，易调试，灵活性较强。

`Shell`是解释执行的脚本语言，在`Shell`中可以直接调用Linux系统命令。

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



## `Tab`键命令和文件名自动补全

```
Tab键命令和文件名自动补全
```

## 终端常用快捷键

- `ctrl + c` 强制终止当前命令
- `ctrl + l` 清屏
- `ctrl + a` 光标移动到命令行首
- `ctrl + e` 光标移动到命令行尾
- `ctrl + u` 从光标的位置删除到行首
- `ctrl + z` 把命令放入后台
- `ctrl + r` 在历史命令中搜索

