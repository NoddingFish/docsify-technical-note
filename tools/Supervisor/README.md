 

[Supervisor](http://supervisord.org/introduction.html#overview)是用 `Python` 开发的一个 `client/server` 服务，是 `Linux/Unix` 系统下的一个进程管理工具，不支持Windows系统。它可以很方便的监听、启动、停止、重启一个或多个进程。用 `Supervisor` 管理的进程，当一个进程意外被杀死，`Supervisort` 监听到进程死后，会自动将它重新拉起，很方便的做到进程自动恢复的功能，不再需要自己写 `shell` 脚本来控制。