 

[Supervisor](http://supervisord.org/introduction.html#overview)是用 `Python` 开发的一个 `client/server` 服务，是 `Linux/Unix` 系统下的一个进程管理工具，不支持Windows系统。它可以很方便的监听、启动、停止、重启一个或多个进程。用 `Supervisor` 管理的进程，当一个进程意外被杀死，`Supervisort` 监听到进程死后，会自动将它重新拉起，很方便的做到进程自动恢复的功能，不再需要自己写 `shell` 脚本来控制。



## 配置

> 参考：[Supervisor使用详解](https://www.jianshu.com/p/0b9054b33db3)

##### 安装：

```shell
yum install supervisor
```

##### 设置开机启动：

```shell
systemctl enable supervisord.service
```

##### 重启：

```shell
supervisorctl reload
```



##### 说明：

​	配置文件：`/etc/supervisord.conf`

​	守护进程目录：`/etc/supervisord.d`

​	启动脚本：`/etc/init.d/supervisord`

​	

启动 Supervisor 

```shell
supervisord -c /etc/supervisord.conf
```

注意：

若执行命令 `supervisord -c /etc/supervisord.conf` 有误

执行：
`supervisorctl?stop?all` 停止所有进程
1、find / -name supervisor.sock
2、unlink /tmp/supervisor.sock 
3、ps -ef | grep supervisord
4、 kill -9 30027 //kill进程 
5、supervisord -c /etc/supervisord.conf