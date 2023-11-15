# Jenkins

##### 开始使用 Jenkins 

本导读将向您介绍使用 `Jenkins` 、 `Jenkins`  的主要特性和 Jenkins Pipeline 的基本知识。 本导读使用“独立”的 Jenkins 发行版，它可以在您自己本地的机器上运行。

##### 准备工作

第一次使用 `Jenkins` ，您需要：

- 机器要求：
  - 256 MB 内存，建议大于 512 MB
  - 10 GB 的硬盘空间（用于 Jenkins 和 Docker 镜像）
- 需要安装以下软件：
  - Java 8 ( JRE 或者 JDK 都可以)
  - [Docker](https://www.docker.com/) （导航到网站顶部的Get Docker链接以访问适合您平台的Docker下载）

##### 下载并运行 Jenkins

1. [下载 Jenkins](http://mirrors.jenkins.io/war-stable/latest/jenkins.war).
2. 打开终端进入到下载目录.
3. 运行命令 `java -jar jenkins.war --httpPort=8080`.
4. 打开浏览器进入链接 `http://localhost:8080`.
5. 按照说明完成安装.

安装完成后，您可以开始使用 `Jenkins` ！

![img](jenkins.assets/6464255-cc56d3af1fdd96df.png)

## 安装

> [Jenkins安装](https://www.jenkins.io/zh/doc/book/installing/#setup-wizard)

#### Docker 中下载并允许 Jenkins

```shell
docker run ^
  -u root ^
  --rm ^
  -d ^
  -p 8080:8080 ^
  -p 50000:50000 ^
  -v jenkins-data:/var/jenkins_home ^
  -v /var/run/docker.sock:/var/run/docker.sock ^
  jenkinsci/blueocean
  
# 特别说明：--rm 自动删除该容器
```

```shell
docker run -u root --rm -d --name=jenkins -p 8089:8080 -p 50000:50000 -v jenkins-data:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean
```

直接访问：`http://localhost:8080`



## 运行

```shell
# 启动容器
docker start jenkins

# 进入容器
docker exec -it jenkins /bin/sh
```



## 使用

> [自动化部署Jenkins](https://blog.csdn.net/Adda_Chen/article/details/128058014)
>
> [Jenkins + Gitee(码云) 实现代码自动化构建](https://blog.csdn.net/qq_34272964/article/details/93747652)

### 部署到远程服务

> [Jenkins通过Publish Over SSH实现前端项目部署到远程服务全纪录](https://juejin.cn/post/7000534516410351646)

上篇文章[centos+jenkins+nginx+gitlab前端自动化部署全记录](https://juejin.cn/post/6997560212924137485)聊了通过`Jenkins` `Nginx`实现前端自动化部署的整个过程,不过上篇文章部署的场景是`Jenkins`和`Nginx`服务在一台机器上.

但是很多情况下,我们的`Jenkins`可能是单独在一台服务器,`Nginx`服务可能根据环境的不同在多台服务器上,这时就需要`Jenkins`

远程推送前端代码到不同的服务器上.

这篇文章将带大家实践一下`Jenkins`自动化构建前端项目到远程服务器的过程.其中很多过程和上篇文章的内容是重叠的,所以我们这

次将重点聊一下`Jenkins`推送前端代码到不同服务器的过程,其他步骤参考上篇文章.

以下步骤的前提是 `Jenkins`已经部署好,目标机器的`Nginx`已部署

说一下`Jenkins`推送代码到其他服务器的大致步骤:

主要依赖`Jenkins`的一个插件[Publish Over SSH](https://link.juejin.cn?target=https%3A%2F%2Fplugins.jenkins.io%2Fpublish-over-ssh),这个插件主要是通过SSH链接到其他Linux机器,远程传输文件以及执行Shell命令.

基于这个插件,整个过程大概如下:

- 配置`Linux`系统的`SSH`服务免密登录. `Jenkins`服务可以免密访问目标服务器
- 创建`Jenkins`任务,配置构建触发器,当`Gitlab`仓库`push`代码时,自动将代码更新到Jenkins服务上
- 更新完代码后 进行代码的依赖包安装,打包
- 通过脚本,打包前端代码,推送到目标机器的指定文件夹
- 更新目标机器`Nginx`中前端代码,并重启`Nginx`服务

#### 1. 安装插件Publish Over SSH

在`Jenkins`中找到 Manage Jenkins -> Manage Plgins->可选插件中搜索`Publish Over SSH`

安装 重启`Jenkins`

![image-20210825162628539.png](jenkins.assets/88b1723b0a8e4e719974bd99404cccfatplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

#### 2. 配置SSH秘钥

这一步是为了`Jenkins`所在的服务器通过`SSH`访问目标机器时获得免密授权

登录`Jenkins`所在的服务器,执行 `ssh-keygen`

![image-20210820170509941.png](jenkins.assets/bd61873774bb4ab09dae4501c564798ctplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

**将公钥拷贝到authorized_keys文件**

```shell
cat id_rsa.pub >> authorized_keys # 追加写入的，不放心可以复制一份再操作
```

执行完 会生成一个`authorized_keys`文件,如下:

![image-20210825163633307.png](jenkins.assets/ee52e88dd479405090da84c31f35a799tplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

**将生成的authorized_keys拷贝到要连接的linux机器上的对应用户或者直接root下的.ssh文件夹下**

```shell
scp authorized_keys root@目标及其IP:/root/.ssh

# 如果 ssh 的端口修改了，则使用以下命令， -P 2201 则代表端口
scp -P 2201 authorized_keys root@42.192.249.199:/root/.ssh
```

执行上面命令 会提示你输入目标机器的密码 确定后 就会将存有`Jenkins`所在服务的公钥文件`authorized_keys`存在目标机器的`/root/.ssh`下了,并且在`Jenkins`所在服务器的`/root/.ssh`下也会新增一个`known_hosts`文件

![image-20210820172638608.png](jenkins.assets/d1f53d4cf465425fadd2d0b2dbcb6c7atplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

在服务器中配置完秘钥之后,接下来需要在`Jenkins`中做配置:

Jenkins -->系统管理-->系统设置

找到`Publish over SSH`

![image-20210821103433593.png](jenkins.assets/20cc8a659ded429bbeede88c050f7a1btplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

`Publish over SSH`公共配置:

- Passphrase  刚才在Jenkins服务生成秘钥时如果有输入密码 则填写 没有填写则空着
- Path to key 秘钥的路径
- Key 刚才生成的私钥 也就是生成的`id_rsa`文件 可以在服务器上通过命令`cat id_rsa`获取到

`SSH Server`私有配置

- SSH Server 服务名称 自定义 方便区分即可

- Hostname 目标服务器的IP地址

- Username 目标服务器的用户名

- Remote Directory 目标服务器的目录名称 `Jenkins`推送过来的包会放在这个位置(注: **刚才填写的用户名需要有权限对此文件目录有读写权限**)

  其他的不必配置

  **如果 `ssh` 端口不是 22 ，则需要在高级中修改一下正确的端口号**
  
  配置完成后,点击 `Test Configuation `测试一下

报错:

```tex
jenkins.plugins.publish_over.BapPublisherException: Failed to add SSH key. Message [invalid privatekey: [B@ba77e81]]
```

![image-20210821094844397.png](jenkins.assets/0850cead5fd84e0d96f2a30a83c60cd5tplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

当前秘钥首行

```vbnet
-----BEGIN OPENSSH PRIVATE KEY——
```

发现如上报错 原来是因为Jenkins当前版本不支持这种秘钥格式

解决报错:

我们需要在Jenkins服务上生成指定格式的秘钥

```shell
ssh-keygen -m PEM -t rsa -b 4096
```

说明: -m 参数指定密钥的格式，PEM是rsa之前使用的旧格式  -b 指定密钥长度。对于RSA密钥，最小要求768位，默认是2048位。

把之上的流程再走一遍

如果测试结果是`Success`,则配置成功了

#### 3. Jenkins构建前端项目,并推送到目标机器

接下来我们来创建一个`Jenkins`任务,并关联`Gitlab`项目仓库.

具体步骤,可以参考上篇[centos+jenkins+nginx+gitlab前端自动化部署全记录](https://juejin.cn/post/6997560212924137485)这篇文章,有详细过程.这里一笔带过.

假设在`Jenkins`任务中配置`git`权鉴,以及构建触发器已经配置完成.

截至到目前这一步 我们能实现的是  当我们`push`指定的分支后,`Jenkins`会自动更新代码到`Jenkins`服务器上

接下来我们要做的就是 将前端代码安装依赖 再进行构建

然后通过SSH将打包后的文件推送到目标机器上

再目标机器上重启`Nginx`服务来达到自动部署的目的

以下是我们刚创建的`jenkins`任务的部分配置:

![image-20210825201356151.png](jenkins.assets/3214abcd1dfe4204a42224cd74c74371tplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

![image-20210825201243348.png](jenkins.assets/2761ba6cb42f42659cf23090061a3ad7tplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

如果以上配置没问题,接下来我们需要配置构建

`Execute shell`里写脚本,对前端代码进行打包压缩具体代码如下:

```shell
# 默认在workspace目录 既仓库的根目录
# 安装依赖
npm install
# 删除旧的dist文件
rm -rf dist
# 重新build代码
npm run build
# 将build好的文件 打成tar包方便传送
cd dist
tar -zcvf teacher.tar.gz *
cd ..
echo '打包完毕'
```

至此,我们已经将前端构建好的代码打成`tar`包,放在了`dist`文件夹下,接下来我们需要把这个`tar`包推送到目标服务器上

![image-20210825202319043.png](jenkins.assets/51c9f193780746bebd93ca569cc2a561tplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

选择 增加构建步骤-->`Send files or execute commands over SSH`

![image-20210825203318076.png](jenkins.assets/eb3a271b6026494fa054e21d3c092ff7tplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

SSH Server配置:

- Name 选择服务名称 我们刚才在Jenkins -->系统管理-->系统设置中已经配置 选择即可
- Source files 源文件目录 这个目录对应的是我们仓库代码的根目录 这个地址必须是基于根目录的相对路径 不可以放到根目录外面的曾经 不可以用`../../XXX`这样的路径来访问 否则会出现`SSH: Transferred 0 file(s)`的问题 文件传送不过去!!!!! 大坑大坑大坑大坑
- Remove prefix 要去除的前缀 把`Source files`文件目录地址去掉即可 否则会在目标机器上创建对应的目录
- Remote directory 要传送文件到的目标机器的目录 即我们刚才在Jenkins -->系统管理-->系统设置中配置的目录 填写/即可
- Exec command 传输代码完成后 执行的脚本 可以用来做对包的解压 重启`Nginx`等操作

我们保存一下`Jenkins`配置, 我们构建一下`jenkins`任务,来验证一下是否能正常将代码推送到目标机器

我们可以查看`jenkis`任务的控制台输出,如果是以下提示:

![image-20210825204115156.png](jenkins.assets/80efbb98340649dc9e81d0083c73cc98tplv-k3u1fbpfcp-zoom-in-crop-mark1512000.webp)

已经传输了1个文件 说明我们前面的工作都生效了!!

我们也可以去目标机器 我们刚才配置的地址上去看一下 是否有文件

```shell
cd /usr/local/fe/smartmag/
ll
```

当我们看到有`teacher.tar.gz`包存在时 说明已经成功

接下来就剩下最后一步了 需要将我们的包解压 并且将代码移动到`Nginx`指定的文件下,并重启`Nginx`

具体的`Nginx`服务配置,在上一篇文章中也有详细说明

接下来 我们需要在刚才未填写的`Exec command`中写脚本了

```shell
shell复制代码# 将teacher.tar.gz包解压到Nginx指定的目录下
tar -zxvf teacher.tar.gz -C '/usr/local/nginx/html/teacher'
# 删除tar包
rm -rf teacher.tar.gz
# 重启Nginx
cd /usr/local/nginx/sbin
./nginx -s reload
echo '重启完毕'
```

然后再次保存`jenkins`配置

重新构建一下`Jenkins`任务,来看一下我们的前端代码是否更新.

#### 总结

至此,整个`Jenkins`自动化构建前端项目到远程服务器的整个流程已经走完.其中省略了一些步骤,可以在上一篇文章中查看. 这篇文章的核心就是`Jenkins` `SSH`免密登录远程服务的配置,当我们首次做的时候,基本会一步一个坑,会碰到很多莫名其妙的问题,我们可以把这些过程记录下来,也是我们的积累.

文章可能会有一些瑕疵的地方,如果在实践中遇到问题,也欢迎大家留意讨论,一起学习进步.

另外,这两篇文章漏说了一点就是,对`Nginx`服务设置开机重启,我们的测试环境有可能还有后端的其他服务,有可能会有人对测试服务进行重启,这样我们的`Nginx`可能会挂掉,手动重启就太low了,下面的参考资料里有开机自动重启的设置.大家自行查阅.

最近我们也总结了一套前端面试题,大概有将近600道题,有题有详细答案,欢迎近期需要面试的同学访问.具体访问方式请查看个人简介.

作者：前端面试题宝典
链接：https://juejin.cn/post/7000534516410351646
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



### Jenkins（Docker）执行宿主机的脚本



![image-20231111143001343](jenkins.assets/image-20231111143001343.png)



### 利用 `Tag` 标签部署和回滚

> [jenkins利用tag标签回滚](https://blog.csdn.net/qq_42157883/article/details/124096046)
>
> [Jenkins+Gitlab通过脚本自动部署回滚web项目版本至服务器集群（测试环境和正式环境），图文详细](https://www.ywbj.cc/?p=615)

### 使用插件 `NodeJS`

> [安装nodeJs插件](https://blog.csdn.net/cm_pq/article/details/129666621)