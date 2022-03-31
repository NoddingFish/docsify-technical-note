# 轻量型日志采集器

无论您是从安全设备、云、容器、主机还是 `OT` 进行数据收集，`Filebeat` 都将为您提供一种轻量型方法，用于转发和汇总日志与文件，让简单的事情不再繁杂。

> [Filebeat 官方文档](https://www.elastic.co/guide/en/beats/filebeat/index.html)



## 安装

 `Linux` :

```shell
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.1.1-linux-x86_64.tar.gz

tar xzvf filebeat-8.1.1-linux-x86_64.tar.gz
```

 `test.yml` :

```yaml
filebeat.inputs:
- type: filestream
  enabled: true
  paths:
    - /usr/local/filebeat-8.1.1/log/*.log

filebeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: false

setup.template.settings:
  index.number_of_shards: 1

setup.kibana:
  host: "localhost:5601"

output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "password"

processors:
  - add_host_metadata:
      when.not.contains.tags: forwarded
  - add_cloud_metadata: ~
  - add_docker_metadata: ~
  - add_kubernetes_metadata: ~
```

运行：

```shell
./filebeat -e -c test.yml # test.yml 可以自定义
```



