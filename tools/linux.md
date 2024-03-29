# Linux

常用命令

> [Linux - Shell - 在多个文件中查找关键字](https://www.cnblogs.com/xy14/p/11735343.html)

```shell
# 查看当前文件夹下的所有文件/文件夹大小
du -sh * 

# 查看磁盘挂载和使用情况
df -lh

# CP 强制覆盖文件
\cp -fr ./copy/* ./to

# 不理解 find 的同学, 可以用 ls 代替
# 我就不怎么理解
# -name 支持 通配符, 我的目录下只有三个文件, 所以也可以不带
find . -type f -name "*" | xargs grep "createChannelEmpower"
```



#### tar命令：压缩、解压
linux的压缩格式：

1. `*.tar` 打包，大小不变

2. `*.tar.gz` 打包并压缩文件大小

   参数：

   - `-c` 建立一个压缩文件的参数指令-压缩
   - `-x` 解开一个压缩文件的参数指令-解压
   - `-z` 是否需要用 `gzip` 压缩
   - `-v` 压缩的过程中显示文件
   - `-f` 使用新名档名（给压缩文件取名字）在 `f` 之后接档名

常用组合：

```shell
# 压缩：
tar -zcvf 2308.tar.gz request-2023-08*

# 解压：
tar -zxvf 2308.tar.gz -C 指定路径
```



## Q&A

### 1、CURL 中特殊字符的处理

> [CURL命令详解及@&特殊字符处理](https://www.cnblogs.com/eternityz/p/14392214.html)

遇到的问题是，使用 `github` 的 `actions` 自动登录签到，修改了复杂密码后，使用 `Curl` 密码正常，但是一直登录不上

```shell
# cURL> 7.18.0具有选项--data-urlencode，它可以解决此问题。 使用这个，我可以简单地发送一个POST请求作为

curl -d name=john --data-urlencode passwd=@31&3*J https://www.mysite.com
```

