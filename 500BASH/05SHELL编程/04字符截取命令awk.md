# 字符截取命令`awk`

`awk`命令:默认使用空格或制表符作为字段分隔符

`awk '条件1{动作1}条件2{动作2}...' 文件名`

- 条件(`Pattern`)

  - 一般使用关系表达式作为条件

    - x > 10判断变量x是否大于10
    
    - x>=10大于等于
    
    - x<=10小于等于

- 动作(Action):

  - 格式化输出
  - 流程控制语句



## 例子

```bash
$ vim student.txt
ID	Name	gender	Mark
1	furong	F	85
2	fengj	F	60
3	cang	F	70
# 下面两个awk语句是等价的
$ awk '{printf $2 "\t" $4 "\n"}' student.txt
Name	Mark
furong	85
fengj	60
cang	70
$ awk '{print $2 "\t" $4}' student.txt
Name	Mark
furong	85
fengj	60
cang	70
$ df -h | awk '{print $5}'
Use%
3%
0%
0%
0%
3%
0%
0%
$ df -h | grep -v Use% | awk '{print $5}'
3%
0%
0%
0%
3%
0%
0%
$ df -h | grep -v Use% | awk '{print $5}' | cut -f 1 -d "%"
3
0
0
0
3
0
0
# 取ssh进程号
$ ps -ef | grep -v grep | grep 'sshd -D' | awk '{print $2}'
1

# 杀进程脚本
$ vim kill.sh
#!/bin/bash
pids=$(ps -ef | grep -v grep | grep -v $0 | grep "$*" | awk '{print $2}')
for pid in $pids
	do
		echo $pid
		# kill -9 $pid
	done
$ chmod 755 kill.sh
$ ./kill.sh /usr/sbin/sshd
1
$ ./kill.sh "sshd -D"
1

```



## BEGIN

```bash
$ awk 'BEGIN{print "This is a transcript"}{print $2 "\t" $4}' student.txt
This is a transcript
Name	Mark
furong	85
fengj	60
cang	70
```

## END

```bash
$ awk 'END{print "The End"}{print $2 "\t" $4}' student.txt
Name	Mark
furong	85
fengj	60
cang	70
The End
```



## `FS`内置变量

`FS`内置变量的作用是指定分隔符。

```bash
$ useradd user1
$ useradd user2
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
user1:x:1000:1000::/home/user1:/bin/bash
user2:x:1001:1001::/home/user2:/bin/bash
$ cat /etc/passwd | grep -v "root" | grep "/bin/bash" | awk 'BEGIN{FS=":"}{print $1 "\t" $3}'
user1	1000
user2	1001
```



## `awk`关系运算符

```bash
$ cat student.txt
ID	Name	gender	Mark
1	furong	F	85
2	fengj	F	60
3	cang	F	70

$ cat student.txt | grep -v Name | awk '$4>=70{print $2}'
furong
cang
```

