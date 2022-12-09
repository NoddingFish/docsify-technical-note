# Hyperf

## 安装

我们使用 `Docker` 安装

```shell
docker pull hyperf/hyperf:8.0-alpine-v3.16-swoole
```

运行镜像

```shell
docker run --name hyperf \
-v G:\Docker:/data/project \
-p 9501:9501 -it \
--privileged -u root \
--entrypoint /bin/sh \
hyperf/hyperf:8.0-alpine-v3.16-swoole

# docker run --name hyperf -v G:\Docker:/data/project -p 9501:9501 -it --privileged -u root --entrypoint /bin/sh hyperf/hyperf:8.0-alpine-v3.16-swoole
```

这样我们就进入到容器了，接下来可以使用 `Composer` 安装 `Hyperf`

```shell
cd /data/project
composer create-project hyperf/hyperf-skeleton
```

安装默认的配置后可以启动

启动命令

```shell
php bin/hyperf.php start
```

