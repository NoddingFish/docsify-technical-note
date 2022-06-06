## 简介

![img](canal.assets/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303139313130343130313733353934372e706e67.png)

[**canal [kə'næl]**](https://github.com/alibaba/canal.git)，译意为水道/管道/沟渠，主要用途是基于 MySQL 数据库增量日志解析，提供增量数据订阅和消费

早期阿里巴巴因为杭州和美国双机房部署，存在跨机房同步的业务需求，实现方式主要是基于业务 trigger 获取增量变更。从 2010 年开始，业务逐步尝试数据库日志解析获取增量变更进行同步，由此衍生出了大量的数据库增量订阅和消费业务。

基于日志增量订阅和消费的业务包括

- 数据库镜像
- 数据库实时备份
- 索引构建和实时维护(拆分异构索引、倒排索引等)
- 业务 cache 刷新
- 带业务逻辑的增量数据处理

当前的 canal 支持源端 MySQL 版本包括 5.1.x , 5.5.x , 5.6.x , 5.7.x , 8.0.x

## 工作原理

#### MySQL主备复制原理

![img](canal.assets/687474703a2f2f646c2e69746579652e636f6d2f75706c6f61642f6174746163686d656e742f303038302f333038362f34363863316131342d653761642d333239302d396433642d3434616335303161373232372e6a7067.jpeg)

- MySQL master 将数据变更写入二进制日志( binary log, 其中记录叫做二进制日志事件binary log events，可以通过 show binlog events 进行查看)
- MySQL slave 将 master 的 binary log events 拷贝到它的中继日志(relay log)
- MySQL slave 重放 relay log 中事件，将数据变更反映它自己的数据

#### canal 工作原理

- canal 模拟 MySQL slave 的交互协议，伪装自己为 MySQL slave ，向 MySQL master 发送dump 协议
- MySQL master 收到 dump 请求，开始推送 binary log 给 slave (即 canal )
- canal 解析 binary log 对象(原始为 byte 流)



## 准备工作

- 首先保证服务器的 `jdk` 环境已经就绪，因为 `canal` 是由 `java` 进行开发的，所以必须具备 `java` 运行环境。

##### 查看 `Mysql` 是否开启 `binlog`

```sql
show variables like 'log_bin';

show binary logs;
```



##### 授权 `canal` 链接 `MySQL` 账号具有作为 `MySQL slave` 的权限, 如果已有账户可直接 `grant`

```sql
CREATE USER canal IDENTIFIED BY 'canal';  
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'canal'@'%';
-- GRANT ALL PRIVILEGES ON *.* TO 'canal'@'%' ;

FLUSH PRIVILEGES;
```



##### 安装 JDK

```shell
# 下载 tar.gz 包 https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html

# 解压 目录是 /root/JDK
tar -zxvf jdk-8u202-linux-x64.tar.gz -C JDK

# ~/.bash_profile 添加 JAVA 相关变量，如下：
vi /etc/profile
```

```properties
export JAVA_HOME=/root/JDK/jdk1.8.0_202
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin/$JAVA_HOME:$PATH
```

```shell
source /etc/profile # source ~/.bash_profile 刷新环境变量

java -version # 出现以下信息，说明安装成功
#[root@iZbp1a8nvld17ybfu7cemtZ ~]# java -version
#java version "1.8.0_202"
#Java(TM) SE Runtime Environment (build 1.8.0_202-b08)
#Java HotSpot(TM) 64-Bit Server VM (build 25.202-b08, mixed mode)

#[root@iZbp1a8nvld17ybfu7cemtZ ~]# which java
#/root/JDK/jdk1.8.0_202/bin/java
```



##### 下载 canal.deployer

```shell
# 下载
wget https://github.com/alibaba/canal/releases/download/canal-1.1.6/canal.deployer-1.1.6.tar.gz

# 解压到指定文件夹下
tar -zxvf canal.deployer-1.1.6.tar.gz -C ./canal
```



## 启动

#### 启动 `deployer`

配置：

```properties
canal.instance.master.address=127.0.0.1:3306 # mysql 地址
canal.instance.dbUsername=username # 用户
canal.instance.dbPassword=password # 密码
canal.instance.filter.regex=canal\\..* # 关注的表
#mysql 数据解析关注的表，Perl正则表达式.
#
#多个正则之间以逗号(,)分隔，转义符需要双斜杠(\\)
#
#常见例子：
#
#  1.  所有表：.*   or  .*\\..*
#  2.  canal schema下所有表： canal\\..*
#  3.  canal下的以canal打头的表：canal\\.canal.*
#  4.  canal schema下的一张表：canal\\.test1
#  5.  多个规则组合使用：canal\\..*,mysql.test1,mysql.test2 (逗号分隔)
```

命令：

```shell
sh bin/startup.sh # 启动
sh bin/stop.sh # 关闭
```



#### 启动 `canal-go` 客户端

- canal java 客户端: https://github.com/alibaba/canal/wiki/ClientExample
- canal c# 客户端: https://github.com/dotnetcore/CanalSharp
- canal go客户端: https://github.com/CanalClient/canal-go
- canal Python客户端: https://github.com/haozi3156666/canal-python

修改 `canal-go -> samples -> main.go` ：

```go
connector := client.NewSimpleCanalConnector("172.16.186.82", 11111, "", "", "example", 60000, 60*60*1000)
err := connector.Connect()
if err != nil {
    log.Println(err)
    os.Exit(1)
}

//err = connector.Subscribe(".*\\..*")
err = connector.Subscribe("canal\\..*")
```

命令：

```shell
go run samples/main.go
```



## FAQ / 常见问题解答

1. #### 启动 canal 报错

   ```log
   2022-05-30 13:49:43.647 [destination = example , address = rm-bp108fv564w70h289wo.mysql.rds.aliyuncs.com/47.111.55.63:3306 , EventParser] ERROR c.a.o.c.p.inbound.mysql.rds.RdsBinlogEventParserProxy - dump address rm-bp108fv564w70h289wo.mysql.rds.aliyuncs.com/47.111.55.63:3306 has an error, retrying. caused by 
   com.google.common.util.concurrent.UncheckedExecutionException: com.alibaba.fastjson2.JSONException: read field error : clientDatas
   	at com.google.common.cache.LocalCache$Segment.get(LocalCache.java:2218) ~[guava-22.0.jar:na]
   	at com.google.common.cache.LocalCache.get(LocalCache.java:4147) ~[guava-22.0.jar:na]
   	at com.google.common.cache.LocalCache.getOrLoad(LocalCache.java:4151) ~[guava-22.0.jar:na]
   	at com.google.common.cache.LocalCache$LocalLoadingCache.get(LocalCache.java:5140) ~[guava-22.0.jar:na]
   	at com.google.common.collect.MigrateMap$MigrateConcurrentMap.get(MigrateMap.java:68) ~[canal.common-1.1.6.jar:na]
   	at com.alibaba.otter.canal.meta.MemoryMetaManager.listAllSubscribeInfo(MemoryMetaManager.java:72) ~[canal.meta-1.1.6.jar:na]
   
   ......
   ```
   
   #### 解决方法：
   
   先停止 `canal` ，把 `conf -> example -> meta.dat` 文件删除，再重启 `canal` ;
   重启会重新生成 `meta.dat` 文件，所记录的最新 `binlog` 文件和位置。
   
   ```shell
   # insert
   ================> binlog[mysql-bin.002686 : 257880],name[test_suyoda_net,auth_center_sub_role], eventType: INSERT
   id : 12  update= true
   uid : 1  update= true
   name : XR测试2  update= true
   status : 1  update= true
   menu_ids : 96,25,26,97,98,99,100,101,102,103,104,27,28,105,29  update= true
   ================> binlog[mysql-bin.002686 : 258066],name[test_suyoda_net,auth_center_sub_role], eventType: INSERT
   id : 13  update= true
   uid : 1  update= true
   name : XR测试3  update= true
   status : 1  update= true
   menu_ids : 96,25,26,97,98,99,100,101,102,103,104,27,28,105,29  update= true
   
   
   # update
   ================> binlog[mysql-bin.002686 : 264579],name[test_suyoda_net,auth_center_sub_role], eventType: UPDATE
   -------> before
   id : 13  update= false
   uid : 1  update= false
   name : XR测试3  update= false
   status : 1  update= false
   menu_ids : 96,25,26,97,98,99,100,101,102,103,104,27,28,105,29  update= false
   -------> after
   id : 13  update= false
   uid : 1  update= false
   name : XR测试3  update= false
   status : 1  update= false
   menu_ids : 96,25,26,97,98,99,100,101,102,103,104,27,28,105,30  update= true
   ================> binlog[mysql-bin.002686 : 264838],name[test_suyoda_net,auth_center_sub_role], eventType: UPDATE
   -------> before
   id : 12  update= false
   uid : 1  update= false
   name : XR测试2  update= false
   status : 1  update= false
   menu_ids : 96,25,26,97,98,99,100,101,102,103,104,27,28,105,29  update= false
   -------> after
   id : 12  update= false
   uid : 1  update= false
   name : XR测试2  update= false
   status : 1  update= false
   menu_ids : 96,25,26,97,98,99,100,101,102,103,104,27,28,105  update= true
   
   # delete
   ================> binlog[mysql-bin.002686 : 266562],name[test_suyoda_net,auth_center_sub_role], eventType: DELETE
   id : 13  update= false
   uid : 1  update= false
   name : XR测试3  update= false
   status : 1  update= false
   menu_ids : 96,25,26,97,98,99,100,101,102,103,104,27,28,105,30  update= false
   ================> binlog[mysql-bin.002686 : 266748],name[test_suyoda_net,auth_center_sub_role], eventType: DELETE
   id : 12  update= false
   uid : 1  update= false
   name : XR测试2  update= false
   status : 1  update= false
   menu_ids : 96,25,26,97,98,99,100,101,102,103,104,27,28,105  update= false
   ```
   
   



## 参考

> https://blog.csdn.net/fei_thefirst/article/details/120727544
>
> https://blog.csdn.net/yehongzhi1994/article/details/107880162
>
> https://www.modb.pro/db/126925
>
> https://github.com/alibaba/canal/wiki/QuickStart
>
> https://github.com/alibaba/canal/tree/canal-1.1.6
>
> [Canal高可用架构部署](https://mp.weixin.qq.com/s/QwvmxqxXirjf-J6mqYY44Q)
>
> [全量同步Elasticsearch方案之Canal](https://zhuanlan.zhihu.com/p/360631964)

