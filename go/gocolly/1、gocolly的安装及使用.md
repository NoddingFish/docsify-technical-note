# 1、gocolly 的安装及使用

##### 安装：

在终端上键入以下命令，然后按Enter键安装Colly。

```shell
go get -u github.com/gocolly/colly/...
```

##### 提示：

> 因为 `长城` 的原因，有些包下载不了，需要手动处理一下：

```shell
git clone https://github.com/golang/net.git  $GOPATH/src/golang.org/x/net 

git clone https://github.com/golang/text.git  $GOPATH/src/golang.org/x/text     

git clone https://github.com/golang/protobuf.git  $GOPATH/src/github.com/golang/protobuf         

git clone https://github.com/golang/appengine.git $GOPATH/src/google.golang.org/appengine

git clone https://github.com/protocolbuffers/protobuf-go.git $GOPATH/src/google.golang.org/protobuf
```

##### 运行测试：

`colly` 中有一些示例代码，在 `$GOPATH/src/github.com/gocolly/colly/_examples` 路径下

```shell
go run rate_limit/rate_limit.go
```

没有报错 `cannot find package` 即可