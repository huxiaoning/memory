### 打印一个`int`值

```shell
$ vim main.c
#include <stdio.h>
int main()
{
	int a = 5;
	printf("a = %d\n",a);
	return 0;
}
$ gcc main.c
$ ./a.out
a = 5
```

