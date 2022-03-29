 

`Elasticsearch ` 是一个分布式、RESTful 风格的搜索和数据分析引擎，能够解决不断涌现出的各种用例。 作为 Elastic Stack 的核心，它集中存储您的数据，帮助您发现意料之中以及意料之外的情况。

> [使用Filebeat采集日志结合logstash过滤出你想要的日志](https://www.jianshu.com/p/c801ec3a64e5)



## 安装

> [Getting started with the Elastic Stack](https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-elastic-stack.html)

Linux：

```shell
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.1.1-linux-x86_64.tar.gz

tar -xzvf elasticsearch-8.1.1-linux-x86_64.tar.gz

cd elasticsearch-8.1.1

./bin/elasticsearch
```

