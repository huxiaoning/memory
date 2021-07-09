# `SHELL`

## 构建基于`Docker`的自定义`CentOS`环境

```bash
$ vim Dockfile
FROM centos:7
COPY id_rsa_2048.pub /root/.ssh/authorized_keys 
ARG TZ=Asia/Shanghai
RUN yum install -y vim wget curl gcc gcc-c++ openssl openssh-server net-tools telnet-server telnet git unzip make kde-l10n-Chinese glibc-common \
	&& mkdir -p /var/run/sshd \
	&& ssh-keygen -q -t rsa -b 2048 -f /etc/ssh/ssh_host_rsa_key -N '' \
	&& ssh-keygen -q -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key -N '' \
	&& ssh-keygen -t dsa -f /etc/ssh/ssh_host_ed25519_key -N '' \
	&& echo 'export LC_ALL="zh_CN.UTF-8"' &>> /etc/profile \
	&& echo 'LANG="zh_CN.UTF-8"' &> /etc/locale.conf \
	&& localedef -c -f UTF-8 -i zh_CN zh_CN.utf8 \
	&& echo ":set encoding=UTF-8" &>> /root/.vimrc \
	&& ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
CMD ["/usr/sbin/sshd","-D"]

$ docker build -t mycentos7:1.0 .
$ docker run --rm -it -p 127.0.0.1:2222:22 --name mycentos -d mycentos7:1.0
```

使用`XShell`连接就可以了。

