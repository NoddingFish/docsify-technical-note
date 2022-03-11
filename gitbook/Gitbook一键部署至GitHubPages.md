# Gitbook 一键部署至 GitHub Pages

#### 将书籍部署到 gh-pages 分支

> 参考：[Gitbook 一键部署至 GitHub Pages](https://blog.csdn.net/simplehouse/article/details/78766513)

这个步骤我使用了 `gh-pages` 这个工具，它可以将文件夹一键发布到 GitHub 项目下的 `gh-pages` 分支中（其他分支也可以发布，但是在本文下用到的就是 `gh-pages` 这个分支）。

1. 首先先安装 `gh-pages` 工具

   ```shell
   $ npm install -g gh-pages
   ```

2. 输入以下指令，将 `_book` 下的所有文档部署到 `gh-pages` 分支

   ```shell
   $ gh-pages -d _book
   ```



---

#### 附加

#### 1、如果安装速度**太慢**，建议使用淘宝 `npm` 镜像：

- 临时使用：

```shell
$ npm --registry https://registry.npm.taobao.org install express
```

- 持久使用：

```shell
$ npm config set registry https://registry.npm.taobao.org
```

配置后可通过下面方式来验证是否成功

```shell
$ npm config get registry
```

