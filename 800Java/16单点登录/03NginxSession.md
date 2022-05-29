# `Nginx Session`

nginx 中的 ip_hash 技术能够将某个 ip 的请求定向到同一台后端，这样一来这个 ip 下的 某个客户端和某个后端就能建立起稳固的 session，ip_hash 是在 upstream 配置中定义的，具 体如下



```nginx
upstream nginx.example.com
{
    server 127.0.0.1:8080;
    server 127.0.0.1:808;
    ip_hash;
}
server
{
    listen 80;
    location /
    {
        proxy_pass
        http://nginx.example.com;
        proxy_set_header Host $http_host;
        proxy_set_header Cookie $http_cookie;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        client_max_body_size 100m;
    }
}

```



不推荐使用，万一某台机器挂了没法整。

