# 输入、过滤器和输出

 `Logstash` 能够动态地采集、转换和传输数据，不受格式或复杂度的影响。利用 `Grok` 从非结构化数据中派生出结构，从 `IP` 地址解码出地理坐标，匿名化或排除敏感字段，并简化整体处理过程。



## 安装

```shell
curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-8.1.1-linux-x86_64.tar.gz

tar xzvf logstash-8.1.1-linux-x86_64.tar.gz

./bin/logstash -f ./config/test.conf
```

