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

