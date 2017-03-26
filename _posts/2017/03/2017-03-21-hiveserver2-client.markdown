---
layout: "post"
title: "hiveserver2服务端及客户端使用"
date: "2017-03-21 10:52"
category: "hive"
tags: [hive]

---

#### hiveserver2服务端     

hiveserver2是一个服务端接口，能够使远程客户端链接并执行查询操作并返回结果，目前的实现版本是基于thrift rpc调用，hiveserver2是hiveserver的改良版本，更好的支持并发及安全认证，能够更好的支持jdbc及odbc等开放的客户端api   

配置： 

```
hive.server2.thrift.min.worker.threads – 最小工作线程, 默认是 5.
hive.server2.thrift.max.worker.threads – 最大工作线程, 默认是 500.
hive.server2.thrift.port – 监听的tcp端口, 默认是 10000.
hive.server2.thrift.bind.host – 绑定的ip，默认是localhost.
```

启动： 

```

$HIVE_HOME/bin/hiveserver2
或者  
$HIVE_HOME/bin/hive --service hiveserver2

```

#### Beeline    

hiveserver2支持命令行模式beeline，这是一个基于sqlline cli的jdbc客户端。beeline shell可以工作在内嵌模式或者远程模式中。在内嵌模式中，他运行在内嵌的hive中，远程模式下是通过thrift链接独立的hiveserver2进程，生产环境下推荐远程模式，因为他更加安全，并且不需要获取hdfs元数据的控制权限。 在远程模式下，hiveserver2仅仅接受有效的thrift调用，即使是http模式下，消息体包含thrift payloads   

```
bin/beeline 

!connect jdbc:hive2://centos1:10000
scan complete in 33ms
Connecting to jdbc:hive2://centos1:10000
Enter username for jdbc:hive2://centos1:10000: 
Enter password for jdbc:hive2://centos1:10000: 
Connected to: Apache Hive (version 0.13.1)
Driver: Hive JDBC (version 0.13.1)
Transaction isolation: TRANSACTION_REPEATABLE_READ
0: jdbc:hive2://centos1:10000> show databases;
+----------------+
| database_name  |
+----------------+
| default        |
+----------------+
1 row selected (1.412 seconds)
0: jdbc:hive2://centos1:10000> 

```

或者通过-u参数    

```
 ./bin/beeline -u jdbc:hive2://centos1:10000
scan complete in 3ms
Connecting to jdbc:hive2://centos1:10000
Connected to: Apache Hive (version 0.13.1)
Driver: Hive JDBC (version 0.13.1)
Transaction isolation: TRANSACTION_REPEATABLE_READ
Beeline version 0.13.1 by Apache Hive
0: jdbc:hive2://centos1:10000> show databases;
+----------------+
| database_name  |
+----------------+
| default        |
+----------------+
1 row selected (0.17 seconds)
```


Beeline 命令行参数   

```

-u <database url>       jdbc url
-r hive2.1.0之后支持  重新连接上次连接过的url
-n 用户名
-p 密码
-d 驱动类
-e 查询语句
-f 执行文件
...

```

输出格式：   

Beeline中结果展示可以以不同的形式展示，格式通过outputformat选项设置，支持table，vertical，xmlattr，xmlelements，separated-value formats(csv,tsv,csv2,tsv2,dsv)     

```
beeline --outputformat=tsv
```

执行最后结果为：    

```
 ./bin/beeline --outputformat=tsv
Beeline version 0.13.1 by Apache Hive
beeline> !connect jdbc:hive2://centos1:10000
scan complete in 5ms
Connecting to jdbc:hive2://centos1:10000
Enter username for jdbc:hive2://centos1:10000: 
Enter password for jdbc:hive2://centos1:10000: 
Connected to: Apache Hive (version 0.13.1)
Driver: Hive JDBC (version 0.13.1)
Transaction isolation: TRANSACTION_REPEATABLE_READ
0: jdbc:hive2://centos1:10000> show databases;
'database_name'
'default'
1 row selected (0.196 seconds)
0: jdbc:hive2://centos1:10000> 
```

