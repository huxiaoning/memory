# `declare`声明变量类型

`declare [+/-][选项] 变量名`

## `declare`选项

| 选项 | 说明                        |
| ---- | --------------------------- |
| `-`  | 给变量设定类型属性          |
| `+`  | 取消变量的类型属性          |
| `-a` | 将变量声明为数组型          |
| `+a` | 取消变量为数组型            |
| `-i` | 将变量声明为整数型(integer) |
| `-x` | 将变量声明为环境变量        |
| `-r` | 将变量声明为只读变量        |
| `-p` | 显示指定变量的被声明的类型  |



## 把变量声明为数值型

```bash
$ aa=11
$ bb=12
$ declare -i cc=$aa+$bb
$ echo $cc	#输出23
```



## 声明数组变量

```bash
$ movie[0]=zp
$ movie[1]=tp
$ declare -a movie[2]=live		#其实这里不声明成数组类型也可以，movie[2]=alive,系统会检测中括号
# 查看数组
$ echo ${movie}		# zp,(相当于echo ${movie[0]})
$ echo ${movie[2]}	# live
$ echo ${movie[*]}	# zp tp live
```



## 声明环境变量

```bash
# 和export作用相似,但其实是declare命令的作用 declare -p查询所有变量的声明
$ declare -x test=123
```



## 声明变量只读属性

```bash
# 给test赋予只读属性，但是只读属性会让变量不能修改，不能删除，甚至不能取消只读属性
$ declare -r test
```



## 查询变量的属性

```bash
# 查询所有变量的属性
$ declare -p
# 查询指定变量的属性
$ declare -p 变量名
```

