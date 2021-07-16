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

```

