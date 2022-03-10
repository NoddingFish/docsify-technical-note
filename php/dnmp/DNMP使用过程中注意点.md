# DNMP 使用

## `MySQL` 和 `Redis` 连接问题

```
# MySQL 项目中：
mysql root 123456 # 服务器地址 用户名 密码

# MySQL Navicat连接：
localhost root 123456 # 服务器地址 用户名 密码
    
# Redis 项目中：
redis root  # 服务器地址 用户名 密码为空

# Redis Medis连接：
127.27.0.1 root  # 服务器地址 用户名 密码为空
```



## 进入容器中

在 `windows` 中输入命令需要加 `winpty`

```bash
$ winpty docker exec -it dnmp_mysql_1 bash
```



## 命令

```shell
$ docker-compose up { -d } # 启动命令 -d 后台启动
$ docker-compose down # 关闭
$ docker-compose restart # 重启
$ docker-compose build { php72 } # 重建
```



## 使用 `composer` 

我这里还是在容器中安装 `composer` ，具体如下：

编辑文件： `vim Dockerfile` ，增加以下代码：

```dockerfile
# 安装 composer 并修改为国内镜像
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://packagist.laravel-china.org
```

之后重建 `php` ：

```shell
$ docker-compose build php72
# 注意一点，我这边重建的是 php72 , 也可以重构其他的版本或者全部重建
```

最后启动以下进入容器查看是否安装成功：

```shell
$ docker-compose up -d
$ winpty docker exec -it dnmp_php72_1 bash # windows 中使用 winpty
```

```shell
$ composer config -gl # 可以查看 composer 配置
```



# 使用 `Xdebug` 

配置 `php.ini` ：

```ini
[XDebug]
; Debug Config
xdebug.remote_enable = 1
xdebug.remote_handler = "dbgp"
;xdebug.remote_connect_back = 1
xdebug.remote_port = 9000
xdebug.idekey = PHPSTORM
# host.docker.internal 是 windows 下 docker 获取host
xdebug.remote_host = host.docker.internal
;xdebug.remote_log = "/var/log/dnmp/php.xdebug.log"
```



