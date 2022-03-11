# 安装 Swoole

Swoole 是 PHP 的一个扩展，可以通过 PHP 扩展的方式进行安装和启用。

#### 本地安装

##### 1、Laradock

在本地安装的话，以 Laradock 为例，需要在 `laradock` 目录下的 `.env`  中将下面两行配置值设置为 `true` ：

```.env
WORKSPACE_INSTALL_SWOOLE=true
PHP_FPM_INSTALL_SWOOLE=true
```

然后运行:

```bash
$ docker-compose build php-fpm workspace
```

重新构建 Docker 容器，构建完成后重启这两个容器，进入 `workspace` 容器，运行 `php -m`  查看 `swoole` 是否安装成功，如果扩展列表包含 `swoole` 则表示安装成功。



##### 2、Windows/Mac

如果是本地 Windows/Mac 系统上安装的话，直接执行以下命令安装接口：

```bash
$ pecl install swoole
```

前提是 `pecl` 命令在系统路径中可用。然后运行 `php -m` 看到扩展列表包含  `swoole`  则表明安装成功。



---

#### 线上安装

如果是在服务器安装的话，以 Ubuntu 系统为例，通过执行下列命令安装即可：

```bash
$ pecl install swoole
```

然后通过 `php -i | grep php.ini` 定位 `php.ini` 文件所在位置，并打开该配置文件，在文件末尾追加如下内容：

```ini
[swoole]
extension=swoole.so
```

保存并退出，在终端运行 `php -m`，如果看到扩展里包含 `swoole`，说明安装启用成功。

> 注：以上服务器安装方式也适用于 Homestead 和其他环境 Ubuntu 系统。
>
> 本文摘自 [基于 Swoole 构建高性能 Laravel 应用系列 —— Swoole 安装与入门](https://laravelacademy.org/post/9780.html)