# Gitbook 常用插件

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



