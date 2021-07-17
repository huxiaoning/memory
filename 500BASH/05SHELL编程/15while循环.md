# `while`循环

`while`循环是不定循环，也称作条件循环。只要条件判断式成立，循环就会一直继续，直到条件判断式不成立，循环才会停止。这就和`for`的固定循环不太一样了。

```bash
while [ 条件判断式 ]
	do
		程序
	done
```



## 例子 - 从1加到100

```bash
$ vim while1.sh
#!/bin/bash
i=1
s=0
while [ $i -le 100 ]
	do
		s=$(($s+$i))
		i=$(($i+1))
	done
echo "从1加到100的和是:$s"
$ chmod 755 while1.sh
$ ./while1.sh
从1加到100的和是:5050
```

