# 安装 Go

> 文档：http://www.runoob.com/go/go-environment.html



#### `go get` 慢

```shell
unset GOPROXY

go env -w GO111MODULE=on

go env -w GOPROXY=https://goproxy.io,direct #阿里云 https://mirrors.aliyun.com/goproxy
```

