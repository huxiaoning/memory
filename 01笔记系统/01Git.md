## `Git`


##### 生成密钥对

```bash
$ sudo apt update
$ sudo apt install -y git
$ git --version
$ git config --global user.name "huxiaoningsworld"
$ git config --global user.email "huxiaoningsworld@sina.com"
$ ssh-keygen -t rsa -C "huxiaoningsworld@sina.com"
$ cat ~/.ssh/id_rsa.pub
```

##### GitHub端口配置和代理配置

```bash
$ cat ~/.ssh/config
Host xxxx.com
        port xxx
        IdentityFile C:\Users\aidan\.ssh\id_rsa
Host ssh.github.com
        User    git
        Hostname        ssh.github.com
        Port    443
        ProxyCommand    connect -S 127.0.0.1:60002 -a none %h %p
        IdentityFile C:\Users\aidan\.ssh\id_rsa
 Host github.com
        User    git
        Hostname        github.com
        Port    22
        ProxyCommand    connect -S 127.0.0.1:60002 -a none %h %p
        IdentityFile C:\Users\aidan\.ssh\id_rsa
```



##### 常用别名

```bash
$ alias add='git add .'
# alias commit='vim ~/info.txt && git commit -m "$(cat ~/info.txt)"'
$ alias commit='git commit -m "UP"'
$ alias pull='git pull'
$ alias push='git push'
$ alias status='git status'

$ vim /etc/bash.bashrc
alias add='git add .'
alias commit='git commit -m "UP"'
alias pull='git pull'
alias push='git push'
alias status='git status'
```



##### `Git`版本控制工具

```shell
echo "# memory" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:huxiaoning/memory.git
git push -u origin master
```



##### git status 中文乱码

```bash
$ git config --global core.quotepath false
＃ 在使用git log出现乱码上面一个设置不能解决问题需要再做以下设置(其实上面那条命令就足够用了)
git config--global gui.encoding utf-8
git config--global i18n.commit.encoding utf-8
git config --global i18n.logoutputencoding utf-8
export LESSCHARSET＝utf-8 ＃ 添加到环境变量
```

