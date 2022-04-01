# gocolly

##### 简介：

> `colly` 是快如闪电而优雅的爬虫框架，提供简洁的 `API` 能够帮助你构建爬虫应用。使用 `Colly` ，你可以轻松地从网站中提取结构化数据，这些数据可用于广泛的应用程序，如数据挖掘，数据处理或归档。

##### 特性：

>- `- 简单的API`
>- `- 快速（单核上> 1k请求/秒）`
>- `- 控制请求延迟和每个域名的最大并发数`
>- `- 自动cookie和session处理`
>- `- 同步/异步/并行抓取`
>- `- 高速缓存`
>- `- 对非unicode响应自动编码`
>- `- Robots.txt支持`
>- `- 分布式抓取`
>- `- 支持通过环境变量配置`
>- `- 随意扩展`

##### 参考：

> Github：[https://github.com/gocolly/colly](https://github.com/gocolly/colly)
>
> 官网：[http://go-colly.org/](http://go-colly.org/)



## 1、安装&使用

##### 安装：

在终端上键入以下命令，然后按Enter键安装Colly。

```shell
go get -u github.com/gocolly/colly/...
```

##### 提示：

> 因为 `墙` 的原因，有些包下载不了，需要手动处理一下：

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
