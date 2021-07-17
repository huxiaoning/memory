# `for`循环语法二



```bash
for (( 初始值;循环控制条件;变量变化 ))
	do
		程序
	done
```



## 例子1 - 从1加到100

```bash
$ vim for3.sh
#!/bin/bash
s=0
for (( i=1;i<=100;i++ ))
	do
		s=$(($s+$i))
	done
echo "从1加到100得：$s"
$ chmod 755 for3.sh
$ ./for3.sh
从1加到100得：5050
```



## 例子2 - 批量添加指定数量的用户

```bash
$ vim for4.sh
#!/bin/bash
read -p "请输入用户名：" -t 30 name
read -p "请输入添加的用户数量：" -t 30 num
read -p "请输入用户密码：" -t 30 pass
if [ ! -z "$name" -a ! -z "$num" -a ! -z "$pass" ]
	then
		y=$(echo $num | sed 's/[0-9]//g')
		if [ -z "$y" ]
			then
				for (( i=1;i<=$num;i++ ))
					do
						/usr/sbin/useradd $name$i &> /dev/null
						echo $pass | /usr/bin/passwd --stdin $name$i &> /dev/null
					done
		fi
fi
$ chmod 755 for4.sh
$ ./for4.sh
$ cat /etc/passwd | grep -v root | grep /bin/bash 
jack1:x:1000:1000::/home/jack1:/bin/bash
jack2:x:1001:1001::/home/jack2:/bin/bash
jack3:x:1002:1002::/home/jack3:/bin/bash
jack4:x:1003:1003::/home/jack4:/bin/bash
jack5:x:1004:1004::/home/jack5:/bin/bash
```



## 例子3 - 删除所有普通用户

```bash
$ vim for5.sh
#!/bin/bash
for name in $(cat /etc/passwd | grep -v "root" | grep "/bin/bash" | cut -f 1 -d ":")
	do
		/usr/sbin/userdel -r $name &> /dev/null
	done
$ chmod 755 for5.sh
$ ./for5.sh
$ cat /etc/passwd | grep -v root | grep /bin/bash
# 无输出，普通用户都删除了。
```

