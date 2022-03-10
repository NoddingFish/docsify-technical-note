# Elasticsearch遇到的问题

##### 1、分页查询，页数很大时，遇到 `max_result_window` 的问题：

查询报错：

```reStructuredText
Error： Result window is too large, from + size must be less than or equal to: [10000] but was [32900]. See the scroll api for a more efficient way to request large data sets. This limit can be set by changing the [index.max_result_window] index level setting.
```

解决办法：

```shell
curl -XPUT 192.168.16.186:30001/index/_settings -H 'Content-Type: application/json' -d '{ "index.max_result_window" :"100000000"}'
# index 是指定所以，需要全部修改，替换成 _all
```

修改成功返回：

```json
{
    "acknowledged": true
}
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



##### 2、Data too large

解决方法一：

```shell
curl  -H "Content-Type:application/json" -XPOST 'http://192.168.16.186:30001/_cache/clear' -d '{ "fielddata": "true" }'
```

返回结果：

```json
{"_shards":{"total":274,"successful":137,"failed":0}}
```

解决方法二：

```html
elasticsearch 默认的 jvm 堆内存大小是 1G ，需要根据自己的机器的场景设置：

一般最大不能超过 32G ，和不能超过物流内存的 50%

设置完成，重启 elasticsearch 即可
```

