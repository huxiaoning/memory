# `MySQL`

```powershell
$ docker run --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d --restart=always -p 127.0.0.1:3306:3306 -v /c/Volumes/mysql:/var/lib/mysql mysql:5.7.34
```

