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

```shell
curl -L -O https://golang.google.cn/dl/go1.18.linux-amd64.tar.gz

tar -C /usr/local -xzf go1.18.linux-amd64.tar.gz

export PATH=$PATH:/usr/local/go/bin

#以上只能暂时添加 PATH，关闭终端下次再登录就没有了。
#我们可以编辑 ~/.bash_profile 或者 /etc/profile，并将以下命令添加该文件的末尾，这样就永久生效了：
export PATH=$PATH:/usr/local/go/bin

# 添加后执行：
source ~/.bash_profile
#或
source /etc/profile
```





## 常见问题

### `go get` 慢

```shell
unset GOPROXY

go env -w GO111MODULE=on

go env -w GOPROXY=https://goproxy.io,direct #阿里云 https://mirrors.aliyun.com/goproxy
```



###  `Windows` 打 `Linux` 包

```
go env
# set GOOS=windows

set GOOS=linux
```

