## 安装

##### `windows` 安装 `node`： [官方网址]( https://nodejs.org/en/ )

`npm` 是 `node.js` 的包管理器，安装 `node.js` 会连同 `node` 和 `npm` 一起安装。

##### `windows` 安装 `nvm` ：

`nvm` 是 `Node Version Manager` ，[网址](https://github.com/nvm-sh/nvm)

`windows` 可以直接下载 `nvm-windows` 的安装包程序，[nvm-windows](https://github.com/coreybutler/nvm-windows/releases)

##### `windows` 安装 `vue-cli` ：

```shell
npm install -g vue-cli
npm install -g @vue/cli # vue3.0后的安装方式，推荐使用
```

##### 注意：

​	若 `nvm` 下载的速度很慢，可以更换镜像源：

```shell
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```



#### 创建 `vue` 项目

##### 命令行的方式

```shell
vue create 项目名称
```

##### `GUI` 的方式

```shell
vue ui
#浏览器访问 http://localhost:8000
```

##### 注意：

`windows` 下 `Git Bash` 运行 `vue create project-name` 出现无法通过上下按键选择安装方式：

则需要在 `Git Bash` 的安装目录找到 `etc/bash.bashrc` 的文件并再末尾增加：

```shell
alias vue='winpty vue.cmd'
```

重新启动 `Git Bash` 即可。



## 常见问题

#### Expected indentation of 0 spaces but found 2 & Missing space before function parentheses

> [学习地址 ：https://blog.csdn.net/shaotaiban1097/article/details/90452001 ]( https://blog.csdn.net/shaotaiban1097/article/details/90452001 )

在 `.eslintrc.js` 文件中的 `rules` 中添加：

```json
{
    "no-tabs": "off",
    "indent": "off",
    "space-before-function-paren": 0
}
```

之后**重新启动**任务即可：

```shell
npm run dev
```
