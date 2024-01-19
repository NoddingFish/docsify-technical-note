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

1. ### max_result_window

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

2. ###   Data too large

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

3. ###   Killed

   ```shell
   ./bin/elasticsearch # 启动后立马报错 killed （已杀死）
   ```

   解决：

   ```
   # 修改 config/jvm.options 的参数
   -Xms512m
   -Xmx512m
   ```

4. ### 分片数量超过限制

   报错信息： `Validation Failed: 1: this action would add [1] total shards, but this cluster currently has [1000]/[1000] maximum shards open;`

   ?>  `elasticsearch7` 版本及以上的，默认只允许 `1000` 个分片，因为集群分片数不足引起的。

   解决：

   ```shell
   # curl -XGET http://localhost:9200/_cluster/settings -u elastic:{password}
   # {"persistent":{},"transient":{}}
   
   curl -XPUT http://es-host:es-port/_cluster/settings -H "Content-Type:application/json" -d '{"transient":{"cluster":{"max_shards_per_node":10000}}}' 
   ```

   返回：

   ```json
   {"acknowledged":true,"persistent":{},"transient":{"cluster":{"max_shards_per_node":"10000"}}}
   ```

5. ###   @timestamp 时间格式

   > [ES 中时间日期类型 “yyyy-MM-dd HH:mm:ss” 的完全避坑指南](https://blog.csdn.net/wlei0618/article/details/123712605)

   ```json
   # ES 查询：2022-04-11T22:24:20+0800 Kibana 查询：Apr 11, 2022 @ 22:24:20.000
   POST log/_doc
   {
     "request_id":"100001",
     "@timestamp":"2022-04-11T22:24:20+0800"
   }
   
   # ES 查询：2022-04-11T22:24:20Z Kibana 查询：Apr 12, 2022 @ 06:24:20.000
   POST log/_doc
   {
     "request_id":"100002",
     "@timestamp":"2022-04-11T22:24:20Z"
   }
   ```

    `ES` 查询接口分别是：

   ```json
   [
       {
           "_index" : "log",
           "_id" : "hIRLGYABEzLZI1HCphCT",
           "_score" : 1.0,
           "_source" : {
               "request_id" : "100001",
               "@timestamp" : "2022-04-11T22:24:20+0800"
           }
       },
       {
           "_index" : "log",
           "_id" : "hYRPGYABEzLZI1HCqRDr",
           "_score" : 1.0,
           "_source" : {
               "request_id" : "100002",
               "@timestamp" : "2022-04-11T22:24:20Z"
           }
       }
   ]
   ```

    `Kibana` 查询结果分别是：

   ```json
   [
       {
           "request_id" : "100001",
           "@timestamp" : "Apr 11, 2022 @ 22:24:20.000"
       },
       {
           "request_id" : "100002",
           "@timestamp" : "Apr 12, 2022 @ 06:24:20.000"
       }
   ]
   ```

   

6. ### 集群已打开最大分片数

   具体报错：

   `Validation Failed: 1: this action would add [1] total shards, but this cluster currently has [1502]/[1000] maximum shards open;`

   ```json
   // 示例
   {
       "params": {
           "body": [
               {
                   "index": {
                       "_index": "order_trade_703918102",
                       "_type": "_doc",
                       "_id": "3741412356055422065"
                   }
               },
               {
                   "tid": "3741412356055422065",
                   "trade_id": "3741412356055422065",
                   "buyer_nick": "t**",
                   "buyer_open_uid": "AAHvbHO_AEFr0sh5MnAkqcox",
                   "payment": "239.98",
                   "consign_time": "1705597024",
                   "status": "106",
                   "tids_status_str": "106",
                   "created": "1705597008",
                   "pay_time": "1705597014",
                   "end_time": "0",
                   "modified": "1705597029",
                   "sys_modified": "1705597029",
                   "seller_rate": "0",
                   "seller_flag": "0",
                   "has_buyer_message": "0",
                   "rate_status": "0",
                   "refund_status": "0",
                   "is_unpack": "0",
                   "is_check": "1",
                   "num": "1",
                   "is_invoice": "0",
                   "undelivered": "0",
                   "is_lock": "0",
                   "print_invoice_num": 0,
                   "print_express_num": 0,
                   "print_invoice_time": "0",
                   "print_express_time": "0",
                   "is_free": "0",
                   "tids": "",
                   "unpack_num": "0",
                   "storehouse": 1,
                   "item_count": 1,
                   "receiver_address": "长*街道**城**园*期***",
                   "receiver_city": "无锡市",
                   "receiver_district": "惠山区",
                   "receiver_state": "江苏省",
                   "receiver_name": "皇**",
                   "receiver_mobile": "*******7193",
                   "is_special": "0",
                   "seller_remarks": "",
                   "buyer_message": "",
                   "print_num": "",
                   "op_res_name": "",
                   "op_express_code": "",
                   "is_print_express": 0,
                   "delivery_type": 0,
                   "delivery_status": -1,
                   "delivery_customer_id": "",
                   "delivery_created": 0,
                   "delivery_get_invoice_time": 0,
                   "delivery_batch_id": 0,
                   "exp_delivery_type": 0,
                   "exp_delivery_status": -1,
                   "exp_delivery_customer_id": "",
                   "exp_delivery_created": 0,
                   "exp_delivery_get_invoice_time": 0,
                   "exp_delivery_batch_id": 0,
                   "customs_type": 1,
                   "customs_status": -1,
                   "customs_name": "",
                   "customs_id_card": "",
                   "customs_batch_id": 0,
                   "customs_created": 0,
                   "item_oid": "3741412356055422065",
                   "item_num_iid": "763699226285",
                   "item_refund_id": "0",
                   "item_refund_status": "0",
                   "item_outer_iid": "0",
                   "item_status": "106",
                   "item_title": "沃尔玛电子卡200元 沃尔玛卡密200面值 全国通用 不刷单谨防诈骗",
                   "item_sku_id": "5255856280349",
                   "item_outer_sku_id": "0",
                   "item_sku_properties_name": "套餐类型:沃尔玛200元电子卡密2326开头",
                   "item_logistics_company": "",
                   "item_invoice_no": "",
                   "item_is_print": "1",
                   "product_title": "沃尔玛电子卡200元 沃尔玛卡密200面值 全国通用 不刷单谨防诈骗",
                   "product_short_name": "",
                   "sku_outer_iid": "",
                   "sku_properties_name": "套餐类型:沃尔玛200元电子卡密2326开头",
                   "sku_short_name": "",
                   "market_stalls_product": "",
                   "market_stalls_sku": "",
                   "market_product": "",
                   "market_sku": ""
               }
           ]
       },
       "response": {
           "took": 0,
           "errors": true,
           "items": [
               {
                   "index": {
                       "_index": "order_trade_703918102",
                       "_type": "_doc",
                       "_id": "3741412356055422065",
                       "status": 400,
                       "error": {
                           "type": "validation_exception",
                           "reason": "Validation Failed: 1: this action would add [1] total shards, but this cluster currently has [1502]/[1000] maximum shards open;"
                       }
                   }
               }
           ]
       }
   }
   ```

   增加分片，具体解决：

   ```shell
   # 增加分片
   curl -X PUT 192.168.16.186:30001/_cluster/settings -H "Content-Type: application/json" -d '{ "persistent": { "cluster.max_shards_per_node": "3000" } }'
   
   # 返回：{"acknowledged":true,"persistent":{"cluster":{"max_shards_per_node":"3000"}},"transient":{}}
   
   # 获取集群中未分配分片的总数
   curl -XGET http://192.168.16.186:30001/_cluster/health\?pretty | grep unassigned_shards
   ```

   

   
