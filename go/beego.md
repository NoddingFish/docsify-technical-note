# beego 安装和 bee 工具的使用

### beego 简介

`beego` 是一个快速开发 Go 应用的 HTTP 框架，他可以用来快速开发 API、Web 及后端服务等各种应用，是一个 RESTful 的框架，主要设计灵感来源于 tornado、sinatra 和 flask 这三个框架，但是结合了 Go 本身的一些特性（interface、struct 嵌入等）而设计的一个框架。



### beego 安装

Beego 的安装是典型的 Go 安装包的形式：

```shell
$ go get github.com/astaxie/beego
```

常见问题：

- git 没有安装，请自行安装不同平台的 git，如何安装请自行搜索。

- git https 无法获取，请配置本地的 git，关闭 https 验证：

  ```shell
  $ git config --global http.sslVerify false
  ```

> 参考：[Beego 安装](https://beego.me/docs/install/)

---



### bee 工具简介

bee 工具是一个为了协助快速开发 beego 项目而创建的项目，通过 bee 您可以很容易的进行 beego 项目的创建、热编译、开发、测试、和部署。

### bee 工具的安装

您可以通过如下的方式安装 bee 工具：

```shell
$ go get github.com/beego/bee
```

安装完之后，`bee` 可执行文件默认存放在 `$GOPATH/bin` 里面，所以您需要把 `$GOPATH/bin` 添加到您的环境变量中，才可以进行下一步。



### 常见问题

1. golang.org/x/net/html

   ```shell
   $ bee run
   ______
   | ___ \
   | |_/ /  ___   ___
   | ___ \ / _ \ / _ \
   | |_/ /|  __/|  __/
   \____/  \___| \___| v1.10.0
   2019/03/19 13:37:27 INFO     ▶ 0001 Using 'dingdian' as 'appname'
   2019/03/19 13:37:27 INFO     ▶ 0002 Initializing watcher...
   ..\github.com\andybalholm\cascadia\parser.go:11:2: cannot find package "golang.org/x/net/html" in any of:
           E:\Go\src\golang.org\x\net\html (from $GOROOT)
           C:\Users\Admin\go\src\golang.org\x\net\html (from $GOPATH)
   2019/03/19 13:37:28 ERROR    ▶ 0003 Failed to build the application: ..\github.com\andybalholm\cascadia\parser.go:11:2: cannot find package "golang.org/x/net/html" in any of:
           E:\Go\src\golang.org\x\net\html (from $GOROOT)
           C:\Users\Admin\go\src\golang.org\x\net\html (from $GOPATH)
   ```

##### 解决（[golang.org/x/net 安装方法](https://blog.csdn.net/xie1xiao1jun/article/details/79421136)）：

```shell
$ mkdir -p $GOPATH/src/golang.org/x/
```

```shell
$ cd $GOPATH/src/golang.org/x/
```

```shell
$ git clone https://github.com/golang/net.git net
```

```shell
$ go install net
```

执行 `go install` 之后没有提示，就说明安装好了。

> 参考：[Bee 工具使用](https://beego.me/docs/install/bee.md)