 `Kibana` 是一个免费且开放的用户界面，能够让您对 `Elasticsearch` 数据进行可视化，并让您在 `Elastic Stack` 中进行导航。您可以进行各种操作，从跟踪查询负载，到理解请求如何流经您的整个应用，都能轻松完成。



## 安装

 `deb` , `rpm` , or `linux` :

```shell
curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-8.1.1-linux-x86_64.tar.gz

tar xzvf kibana-8.1.1-linux-x86_64.tar.gz

cd kibana-8.1.1-linux-x86_64/

./bin/kibana # 控制台运行

nohup ./bin/kibana & # 后台运行
```

启动之后，可以访问 `http://localhost:5601` ，但是远程访问可以还是不行，需要：

```yaml
# config/kibana.yml 配置为
server.host: "0.0.0.0"
```

基础配置：

```yaml
i18n.locale: "zh-CN" # 设置为中文

# 若 elastic 开启密码，则需要配置
elasticsearch.username: "kibana_system" # 注意不可使用 elastic 账户，这是超级用户账户，可以使用 es 修改 kibana_system 密码使用
elasticsearch.password: "pass"
```