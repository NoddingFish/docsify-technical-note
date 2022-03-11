# PhpStorm使用

#### 1、在 `Terminal` 中使用 `git-bash` ：

打开 `phpstorm` 设置，修改 Tooles -> Terminal 中的 Shell path 为：

```shell
D:\SoftWare\Git\bin\sh.exe #修改为自己安装 git-bash 的位置
```

#### 2、在 `Terminal` 使用中可能会出现乱码的问题：

在 `~/.bashrc` 文件中加入一下代码，重启 `IDEA` 

```bash
# 解决IDEA下的terminal中文Unicode编码问题
export LANG="zh_CN.UTF-8"
export LC_ALL="zh_CN.UTF-8"
```

