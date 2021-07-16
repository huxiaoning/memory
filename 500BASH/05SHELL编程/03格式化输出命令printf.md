## 格式化输出命令`printf`

`printf '输出类型输出格式' 输出内容`

- 输出类型

  - `%ns`	:输出字符串.n是数字指代输出几个字符。

  - `%ni`	:输出整数。n是数字指代输出几个数字。

  - `%m.nf`	:输出浮点数。m和n是数字，指代输出的整数位数和小数位数,如%8.2f代表共输出8位数，其中2位是小数，6位是整数。

- 输出格式

  - `\a`：输出警告声音
  - `\b`：输出退格键，也就是backspace键
  - `\f`：清除屏幕
  - `\n`：换行
  - `\r`：回车，也就是enter键
  - `\t`：水平输出退格键，也就是Tab键
  - `\v`：垂直输出退格键，也就是Tab键

## 例子

```bash
$ printf 1 2 3 4 5 6 # 输出1(并且不换行)
$ printf %s 1 2 3 4 5 6	# 输出123456(并且不换行)
$ printf %s %s %s 1 2 3 4 5 6	# 输出%s%s123456(并且不换行)
$ printf '%s %s %s' 1 2 3 4 5 6	# 输出1 2 34 5 6(并且不换行)
$ printf '%s\t%s\t%s\n' 1 2 3 4 5 6
1	2	3
4	5	6

$ vim student.txt
ID	Name	gender	Mark
1	furong	F	85
2	fengj	F	60
3	cang	F	70
$ printf '%s' $(cat student.txt)
IDNamegenderMark1furongF852fengjF603cangF70 # 这里不换行
$ printf '%s\t%s\t%s\t%s\n' $(cat student.txt)
ID	Name	gender	Mark
1	furong	F	85
2	fengj	F	60
3	cang	F	70

```



## 在`awk`命令的输出中支持`print`和`printf`命令

- `print`:`print`会在每个输出之后自动加入一个换行符(`Linux`默认没有`prin`t命令)
- `printf`:`printf`是标准格式输出命令，并不会自动加入换行符，如果需要换行，需要手工加入换行符。

