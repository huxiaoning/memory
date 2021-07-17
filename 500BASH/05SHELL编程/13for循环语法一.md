# `for`循环语法一



```bash
for 变量 in 值1 值2 值3...
	do
		程序
	done
```

- 例子1
```bash
$ vim for1.sh
#!/bin/bash
for i in 1 2 3 4 5
	do
		echo $i
	done
$ chmod 755 for1.sh
$ ./for1.sh
1
2
3
4
5
```
- 例子2：遍历当前目录下的所有文件
```bash
$ vim for2.sh
#!/bin/bash
for each in $(ls)
	do
		echo $each 
	done
$ chmod 755 for2.sh
$ ./for2.sh
anaconda-ks.cfg
case.sh
for1.sh
for2.sh
if5.sh
if6.sh
```

