# 自动修改root密码



```bash
$ vim change_root_password.sh
#!/bin/bash
root_password=$1
/usr/bin/expect <<EOF
spawn passwd root
expect {
"New password:" {send "$root_password\r";exp_continue}
"Retype new password:" {send "$root_password\r"}
}
expect eof
EOF
$ chmod 755 change_root_password.sh
$ ./change_root_password.sh 123456
```



