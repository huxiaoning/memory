# `case`语句

`case`语句和`if...elif...else`语句一样都是多分支条件语句，不过和`if`多分支条件语句不同的是，`case`语句只能判断一种条件关系，而`if`语句可以判断多种条件关系。



```bash
case $变量名 in
	"值1")
		如果变量的值等于值1，则执行程序1
		;;
	"值2")
		如果变量的值等于值2，则执行程序2
		;;
	... 省略其他分支
	*)
		如果变量的值都不是以上的值，则执行此程序
		;;
esac
```



## 例子

```bash
vim case.sh
#!/bin/bash
read -p "请输入你的选择(yes/no)：" -t 30 cho
case $cho in
	"yes")
		echo "您选择了yes"
		;;
	"no")
		echo "您选择了no"
		;;
	*)
		echo "您的输入有误"
		;;
esac
$ chmod 755 case.sh
$ ./case.sh 
请输入你的选择(yes/no)：no
您选择了no
```