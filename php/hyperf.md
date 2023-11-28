# Hyperf

## 安装

我们使用 `Docker` 安装

```shell
docker pull hyperf/hyperf

docker pull hyperf/hyperf:8.0-alpine-v3.16-swoole
```

运行镜像

```shell
docker run --name hyperf \
-v G:\Docker:/data/project \
-p 9501:9501 -it \
--privileged -u root \
--entrypoint /bin/sh \
hyperf/hyperf:8.0-alpine-v3.16-swoole

# docker run --name hyperf -v G:\Docker:/data/project -p 9501:9501 -it --privileged -u root --entrypoint /bin/sh hyperf/hyperf:8.0-alpine-v3.16-swoole

#docker run --name hyperf -v D:\Docker\dnmp\www:/data/project -p 9501-9503:9501-9503/tcp -it --privileged -u root --entrypoint /bin/sh hyperf/hyperf:8.0-alpine-v3.16-swoole
```

这样我们就进入到容器了，接下来可以使用 `Composer` 安装 `Hyperf`

```shell
cd /data/project
composer create-project hyperf/hyperf-skeleton
```

安装默认的配置后可以启动

启动命令

```shell
php bin/hyperf.php start
```



## 微服务

> [PHP写微服务之Hyperf](https://developer.aliyun.com/article/1279660)
>
> [GRPC与JSON-RPC区别 ](https://www.cnblogs.com/zccst/p/17070821.html)
>
> [微服务该如何划分？](https://www.zhihu.com/question/406526550/answer/3053339638?utm_id=0)
>
> [Hyperf服务注册之consul](https://www.imooc.com/article/322509)
>
> [使用consul注册中心，Hyperf框架之间进行RPC互相调用](https://juejin.cn/post/7125274400676380709)
>
> [Hyperf框架 - 基于Json-Rpc与Consul的微服务搭建](https://blog.csdn.net/raoxiaoya/article/details/107718941)
>
> [6、Hyperf 3 微服务系列 - 异常处理，返回相关格式的数据](https://ziruchu.com/art/661)



```shell
docker run --name=consul -p 8500:8500 -e CONSUL_BIND_INTERFACE=eth0 -d consul:1.15.4
```

单独访问服务：
```shell
curl http://localhost:9504

{
    "jsonrpc": "2.0",
    "method": "notify/add",
    "params": {
        "a": 100,
        "b": 202
    },
    "id": "",
    "context": []
}
```



## 推荐组件

### 行为验证码组件

?>   行为验证码采用嵌入式集成方式，接入方便，安全，高效。抛弃了传统字符型验证码展示-填写字符-比对答案的流程，采用验证码展示-采集用户行为-分析用户行为流程，用户只需要产生指定的行为轨迹，不需要键盘手动输入，极大优化了传统验证码用户体验不佳的问题；同时，快速、准确的返回人机判定结果。目前对外提供两种类型的验证码，其中包含滑动拼图、文字点选。

>  [GitHub AJ-Captcha](https://github.com/anji-plus/captcha)
>
> [文档](https://ajcaptcha.beliefteam.cn/captcha-doc/)

`composer` 安装：

```shell
composer require fastknife/ajcaptcha
```

