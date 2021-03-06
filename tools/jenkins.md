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
```

```shell
docker run -u root --rm -d -p 8080:8080 -p 50000:50000 -v jenkins-data:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean
```

直接访问：`http://localhost:8080`