<!-- toc -->

# Nginx 常见问题

### Nginx转发的服务请求头header中不能含有下划线

> <https://www.cnblogs.com/huchong/p/10246031.html>

#### 解决：

1. header中自定义变量名时不要用下划线；
2. 在 `nginx.conf` 中加上 `underscores_in_headers on` 配置：

```nginx
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    underscores_in_headers on;
    keepalive_timeout  65;
}
```
