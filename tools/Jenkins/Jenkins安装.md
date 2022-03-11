# Jenkins安装

> [Jenkins安装](https://www.jenkins.io/zh/doc/book/installing/#setup-wizard)

##### Docker 中下载并允许 Jenkins

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