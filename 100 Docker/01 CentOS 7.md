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







##### 完整

```dockerfile
FROM centos:7
COPY study.pub /root/.ssh/authorized_keys
COPY id_rsa /root/.ssh/id_rsa
COPY id_rsa.pub /root/.ssh/id_rsa.pub
COPY known_hosts /root/.ssh/known_hosts
ADD jdk-8u251-linux-x64.tar.gz /opt/Java/
ADD apache-maven-3.6.3-bin.tar.gz /opt/Maven/
# ARG JAVA_HOME=/opt/Java/jdk1.8.0_251
# ARG M2_HOME=/opt/Maven/apache-maven-3.6.3
# ENV JAVA_HOME=$JAVA_HOME PATH=$PATH:$JAVA_HOME/bin
# ENV CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
# ENV M2_HOME=$M2_HOME PATH=$PATH:$M2_HOME/bin
RUN yum install -y vim wget curl gcc gcc-c++ openssl openssh-server net-tools git \
	&& mkdir -p /var/run/sshd \
	&& ssh-keygen -q -t rsa -b 2048 -f /etc/ssh/ssh_host_rsa_key -N '' \
	&& ssh-keygen -q -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key -N '' \
	&& ssh-keygen -t dsa -f /etc/ssh/ssh_host_ed25519_key -N '' \
	&& git config --global user.name "huxiaoningsworld" \
	&& git config --global user.email "huxiaoningsworld@sina.com" \
	&& chmod 0600 ~/.ssh/id_rsa \
	&& yum install -y kde-l10n-Chinese glibc-common \
	&& echo 'export LC_ALL="zh_CN.UTF-8"' &>> /etc/profile \
	&& echo 'LANG="zh_CN.UTF-8"' &> /etc/locale.conf \
	&& localedef -c -f UTF-8 -i zh_CN zh_CN.utf8 \
	&& echo ":set encoding=UTF-8" &>> /root/.vimrc \
	&& echo 'export JAVA_HOME=/opt/Java/jdk1.8.0_251' &>> /etc/profile.d/java.sh \
	&& echo 'export PATH=$JAVA_HOME/bin:$PATH' &>> /etc/profile.d/java.sh \
	&& echo 'export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar' &>> /etc/profile.d/java.sh \
	&& echo 'export M2_HOME=/opt/Maven/apache-maven-3.6.3' &>> /etc/profile.d/maven.sh \
	&& echo 'export PATH=$M2_HOME/bin:$PATH' &>> /etc/profile.d/maven.sh \
	&& echo 'export MAVEN_OPTS="-Xms128m -Xmx1024m"' &>> /etc/profile.d/maven.sh
CMD ["/usr/sbin/sshd","-D"]
```

