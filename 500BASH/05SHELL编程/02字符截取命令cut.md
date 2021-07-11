# 字符截取命令`cut`

`cut [选项] 文件名`

- 选项
  - `-f` 列号，提取第几列。
  - `-d` 分隔符，按照指定的分隔符分割列（缺省默认为`Tab`制表符）。
    - 不支持空格作为分隔符(" ")，支持的不够友好。



## 示例1

```bash
$ vim student.txt
ID	Name	gender	Mark
1	furong	F	85
2	fengj	F	60
3	cang	F	70
# 我更喜欢这种写法 - cat student.txt | cut -f 2,4
$ cut -f 2,4 student.txt
Name	Mark
furong	85
fengj	60
cang	70
```

## 示例2

```bash
# 先添加两个第三方用户
$ useradd user1
$ useradd user2
$ cat /etc/passwd | grep -v "root" | grep "/bin/bash"
user1:x:1000:1000::/home/user1:/bin/bash
user2:x:1001:1001::/home/user2:/bin/bash
# 查找所有的第三方用户
$ cat /etc/passwd | grep -v "root" | grep "/bin/bash" | cut -f 1 -d ":"
user1
user2

# 删除第三方用户脚本
$ vim deleteuser.sh	#删除第三方用户的脚本
#!/bin/bash
users=$(cat /etc/passwd | grep -v "root" | grep "/bin/bash" | cut -f 1 -d ":")
for user in $users
	do
		userdel $user
	done
$ chmod 755 deleteuser.sh
$ ./deleteuser.sh
# 再次查看所有第三方用户，发现没有第三方用户了
$ cat /etc/passwd | grep -v "root" | grep "/bin/bash" | cut -f 1 -d ":"
```



## `cut`命令的局限性

命令输出的各列之间用多个空格分隔，此时不好提取列。

- `df -h`
- `ps aux` 或 `ps -ef`

这些格式的输出应该使用`awk`命令来提取列。



