# `until`循环



`until`循环，和`whil`循环相反，`until`循环时只要条件判断式不成立则进行循环，并执行循环程序。一旦循环条件成立，则循环终止。也就是一直循环到条件成立为止。



```bash
until [ 条件判断式 ]
	do
		程序
	done
```



## 例子 - 从1加到100

```bash
$ vim until.sh
#!/bin/bash
i=1
s=0
until [ $i -gt 100 ]
	do
		s=$(($s+$i))
		i=$(($i+1))
	done
echo "从1加到100的和是:$s"
$ chmod 755 until.sh
$ ./until.sh
从1加到100的和是:5050
```

