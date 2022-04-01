# Apache 常见问题

<!-- toc -->

#### 1、权限不足（`Permission denied`）

> `thinkphp5` 中设置了 `runtime` 文件夹权限，但还会出现权限不足的情况

```shell
# 设置 runtime 的777权限
chmod -R 777 runtime

# 设置 runtime 的用户组
chown -R www runtime
```

若还是不可以，请查看 `httpd.conf` 下的配置：

```
# 位置一般在：/usr/local/apache/conf/httpd.conf
# 找到 User 和 Group 修改如下，我这边修改的是 www，也可是apache
User www
Group www
```

```shell
# 重启 apache
/etc/init.d/httpd restart
```

