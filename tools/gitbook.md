## 一键部署至 GitHub Pages

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



## 插件

!> 在 `book.json` 中的 `plugins` 中加入：



#### Gitbook 的不同主题配色方案

```json
{
    "theme-default": {
        "showLevel": true
      },
	"plugins": ["theme-comscore"],
}
```

#### expandable-chapters-small 可扩展导航章节

支持多层目录，比 [Toggle Chapters](https://plugins.gitbook.com/plugin/toggle-chapters) 好用，点击箭头才能实现收放目录。不如 `chapter-fold` 好用。

```json
{
    "plugins": [
         "expandable-chapters-small"
    ]
}
```

#### chapter-fold 导航目录折叠

支持多层目录，点击导航栏的标题名就可以实现折叠扩展。

```json
{
    "plugins": ["chapter-fold"]
}
```

#### github 在右上角添加github图标

```json
{
    "plugins": [ 
        "github" 
    ],
    "pluginsConfig": {
        "github": {
            "url": "https://github.com/zhangjikai"
        }
    }
}
```


> [!TIP]
>
> 以下是该 `Gitbook` 文档所使用的 `book.json` ：

[book](../book.json ':include :type=code')



