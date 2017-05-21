---
layout: "post"
title: "kafka集群搭建"
date: "2017-05-21 09:17"
category: "zookeeper"
tags: [kafka,bigdata]

---

#### kafka集群搭建


1.下载kafka  

[http://kafka.apache.org/downloads](http://kafka.apache.org/downloads)

2.下载slf4j软件包  

[https://www.slf4j.org/download.html](https://www.slf4j.org/download.html)

3.将两个压缩包全部上传到集群的某台机器上，这里是centos1  

4.解压两个软件包，并重新命令kafka  

```

tar -zxvf kafka_2.10-0.8.2.1.tgz

mv kafka_2.10-0.8.2.1 kafka

tar -zxvf slf4j-1.7.25.tar.gz

mv slf4j-1.7.25/slf4j-nop-1.7.25.jar /opt/cdh/kafka/libs/


```  

5.修改kafka中的config目录下的server.properties文件中的zookeeper配置   

```

zookeeper.connect=centos1:2181,centos2:2181,centos3:2181

```
6.复制压缩包kafka到集群中的centos2和centos3中  

```
scp -r kafka/ centos@centos2:/opt/cdh/

scp -r kafka/ centos@centos3:/opt/cdh/

```

7.修改centos2和centos3中的kafka目录下的server.properties中的broker.id分别为1和2，保证集群中的id均不同   

8.分别启动三台机器中的启动zookeeper   

9.分别启动三台机器中的kafka  

```
nohup ./bin/kafka-server-start.sh config/server.properties &
```


#### 验证集群  

1.创建主题  

```

./bin/kafka-topics.sh --zookeeper centos1:2181,centos2:2181,centos3:2181 --topic testTopic --replication-factor 1 --partitions 1 --create

Created topic "testTopic".

```

2.创建生产者

```

./bin/kafka-console-producer.sh --broker-list centos1:9092,centos2:9092,centos3:9092 --topic testTopic

[2017-05-21 10:12:41,802] WARN Property topic is not valid (kafka.utils.VerifiableProperties)
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/opt/cdh/kafka/libs/slf4j-log4j12-1.6.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/opt/cdh/kafka/libs/slf4j-nop-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
```  

3.新建窗口，创建消费者  

```

 ./bin/kafka-console-consumer.sh --zookeeper centos1:2181,centos2:2181,centos3:2181 --topic testTopic --from-beginning

```

4.这时候在生产者窗口中输入helloworld，这时候在消费者窗口会看到消息
