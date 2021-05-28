#### `CentOS 7`

##### 基础镜像

```powershell
$ docker pull centos:7
```

##### `Dockerfile`

```dockerfile
FROM centos:7
COPY id_rsa_2048.pub /root/.ssh/authorized_keys 
RUN yum install -y openssh-server \
	&& ssh-keygen -q -t rsa -b 2048 -f /etc/ssh/ssh_host_rsa_key -N '' \
	&& ssh-keygen -q -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key -N '' \
	&& ssh-keygen -t dsa -f /etc/ssh/ssh_host_ed25519_key -N '' 
CMD ["/usr/sbin/sshd","-D"]
```

##### 构建运行

```powershell
$ docker build -t mycentos7:1.0 .
```



##### 调试命令

```powershell
$ docker run -it --rm centos:7 bash
$ docker exec -it mycentos bash
```

##### 正式命令

```powershell
$ docker run --rm -it -p 127.0.0.1:2222:22 --name mycentos -d mycentos7:1.0
```



##### 说明

1. `id_rsa_2048.pub`是用`XSehll`生成的`SSH`密钥对中的公钥

2. 使用`XSehll`连接`mycentos7:1.0`容器

![image-20210528104325084](https://raw.githubusercontent.com/huxiaoning/img/master/image-20210528104325084.png)

![image-20210528104349463](https://raw.githubusercontent.com/huxiaoning/img/master/image-20210528104349463.png)

