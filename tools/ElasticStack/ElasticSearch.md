 `Elasticsearch ` 是一个分布式、RESTful 风格的搜索和数据分析引擎，能够解决不断涌现出的各种用例。 作为 Elastic Stack 的核心，它集中存储您的数据，帮助您发现意料之中以及意料之外的情况。

> [使用Filebeat采集日志结合logstash过滤出你想要的日志](https://www.jianshu.com/p/c801ec3a64e5)
>
> [go-ElasticSearch入门看这一篇就够了(一)](https://my.oschina.net/u/4113533/blog/4549279)
>
> [go-ElasticSearch实战篇(二)](https://my.oschina.net/u/4113533/blog/4562494)



## 安装

> [Getting started with the Elastic Stack](https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-elastic-stack.html)

 `Linux` :

```shell
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.1.1-linux-x86_64.tar.gz

tar -xzvf elasticsearch-8.1.1-linux-x86_64.tar.gz

cd elasticsearch-8.1.1

./bin/elasticsearch # 控制台运行

./bin/elasticsearch -d #后台运行
```

```shell
# 以下是运行之后的信息
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Elasticsearch security features have been automatically configured!
✅ Authentication is enabled and cluster connections are encrypted.

ℹ️  Password for the elastic user (reset with `bin/elasticsearch-reset-password -u elastic`):
  tKb_RyYZYOx0ZKTUSJCs

ℹ️  HTTP CA certificate SHA-256 fingerprint:
  9e85520dad78300062fee8f6d1d1b7f8d7b20a8b7976cc54729f4e2f1bd0bf8a

ℹ️  Configure Kibana to use this cluster:
• Run Kibana and click the configuration link in the terminal when Kibana starts.
• Copy the following enrollment token and paste it into Kibana in your browser (valid for the next 30 minutes):
  eyJ2ZXIiOiI4LjEuMSIsImFkciI6WyIxMC4wLjQuNjo5MjAwIl0sImZnciI6IjllODU1MjBkYWQ3ODMwMDA2MmZlZThmNmQxZDFiN2Y4ZDdiMjBhOGI3OTc2Y2M1NDcyOWY0ZTJmMWJkMGJmOGEiLCJrZXkiOiJ3OXFBMkg4QkVxYzhSRnBGTGpfWjpMTnFsOFVXRlNYcTRfdHNMa1hJYmF3In0=

ℹ️  Configure other nodes to join this cluster:
• On this node:
  ⁃ Create an enrollment token with `bin/elasticsearch-create-enrollment-token -s node`.
  ⁃ Uncomment the transport.host setting at the end of config/elasticsearch.yml.
  ⁃ Restart Elasticsearch.
• On other nodes:
  ⁃ Start Elasticsearch with `bin/elasticsearch --enrollment-token <token>`, using the enrollment token that you generated.
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

  `curl localhost:9200` 访问不到，控制台提示：

```shell
received plaintext http traffic on an https channel, closing connection Netty4HttpChannel{localAddress=/10.0.4.6:9200, remoteAddress=/115.206.19.220:3005}
```

原因是因为 `ES8` 默认开启了 `ssl` 认证。解决：

```shell
vim ./config/elasticsearch.yml
```

```yaml
xpack.security.enabled: true # 改为 false 后，重启 es
```

运行成功：

```shell
[root@VM-4-6-centos code]# curl 127.0.0.1:9200
{
  "name" : "VM-4-6-centos",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "QgPzSU-qRUmp3T45_B_VVQ",
  "version" : {
    "number" : "8.1.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "d0925dd6f22e07b935750420a3155db6e5c58381",
    "build_date" : "2022-03-17T22:01:32.658689558Z",
    "build_snapshot" : false,
    "lucene_version" : "9.0.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
```



## 登录认证

开启 `ssl` ：

```yaml
xpack.security.enabled: true

# xpack.security.http.ssl.enabled: true 这也是 ES8 默认的配置
xpack.security.http.ssl:
  enabled: true # false 则不需要 https 访问
  keystore.path: certs/http.p12
```

登录则需要：

```shell
curl --cacert /usr/local/elasticsearch-8.1.1/config/certs/http_ca.crt -u elastic https://localhost:9200
```

设置 `elastic` 用户的密码:

?> `elasticsearch-setup-passwords` 在 8.0 中已弃用。将在未来版本中删除。要手动重置内置用户（包括`elastic`用户）的密码，请使用该 [`elasticsearch-reset-password`](https://www.elastic.co/guide/en/elasticsearch/reference/current/reset-password.html) 工具、 `Elasticsearch` 更改密码 `API` 或 `Kibana` 中的用户管理功能。

```shell
# 使用此命令重置本地领域中的任何用户或任何内置用户的密码。
./bin/elasticsearch-reset-password -u elastic # 默认情况下，系统会为您生成一个强密码。
./bin/elasticsearch-reset-password -u elastic -i # 要显式设置密码，请使用以交互模式运行该工具 -i
```

密码登录：

```shell
curl -u elastic http://localhost:9200
```



## 常见命令

```shell
GET _cat/indices # 查看所有的数据集
```



## 常见问题

1. ###  `max_result_window` 

   分页查询，页数很大时，遇到 `max_result_window` 的问题查询报错：

   ```tex
   Error： Result window is too large, from + size must be less than or equal to: [10000] but was [32900]. See the scroll api for a more efficient way to request large data sets. This limit can be set by changing the [index.max_result_window] index level setting.
   ```

   解决办法：

   ```shell
   curl -XPUT 192.168.16.186:30001/index/_settings -H 'Content-Type: application/json' -d '{ "index.max_result_window" :"100000000"}'
   # index 是指定所以，需要全部修改，替换成 _all
   ```

   修改成功返回：

   ```json
   {"acknowledged": true}
   ```

   查询索引的配置信息：

   ```shell
   curl -XGET 192.168.16.186:30001/index/_settings # index 是查询指定索引，查询所有替换成 _all
   ```

   查询结果：

   ```json
   {
       "order_huijiedan_2203438183813": {
           "settings": {
               "index": {
                   "number_of_shards": "1",
                   "provided_name": "order_huijiedan_2203438183813",
                   "max_result_window": "100000000", #修改成功
                   "creation_date": "1586923057123",
                   "number_of_replicas": "1",
                   "uuid": "5kfni0pEQ3aygfws2mYWOw",
                   "version": {
                       "created": "7050199"
                   }
               }
           }
       }
   }
   ```

2. ###  `Data too large`

   解决方法一：

   ```shell
   curl  -H "Content-Type:application/json" -XPOST 'http://192.168.16.186:30001/_cache/clear' -d '{ "fielddata": "true" }'
   ```

   返回结果：

   ```json
   {"_shards":{"total":274,"successful":137,"failed":0}}
   ```

   解决方法二：

   ?> `elasticsearch` 默认的 `jvm` 堆内存大小是 `1G` ，需要根据自己的机器的场景设置：一般最大不能超过 `32G` ，和不能超过物流内存的 `50%` 设置完成，重启 `elasticsearch` 即可
   
3.  `Killed` 

   ```shell
   ./bin/elasticsearch # 启动后立马报错 killed （已杀死）
   ```

   解决：

   ```
   # 修改 config/jvm.options 的参数
   -Xms512m
   -Xmx512m
   ```

   
