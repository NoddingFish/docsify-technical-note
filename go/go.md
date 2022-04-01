# Go

**Go** 是一个开源的编程语言，它能让构造简单、可靠且高效的软件变得容易。

第一个 `Go` 程序 `hello.go` ：

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

执行以上代码输出：

```shell
go run hello.go # 输出：Hello, World!
```



## 安装

> 文档：http://www.runoob.com/go/go-environment.html



#### `go get` 慢

```shell
unset GOPROXY

go env -w GO111MODULE=on

go env -w GOPROXY=https://goproxy.io,direct #阿里云 https://mirrors.aliyun.com/goproxy
```

