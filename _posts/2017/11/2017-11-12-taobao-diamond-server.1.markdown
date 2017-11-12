---
layout: "post"
title: "diamond-server环境搭建"
date: "2017-11-12 12:00"
category: "j2ee"
tags: [j2ee,javaweb]

---

#### rocketmq环境搭建


1.下载rocketmq     

```

git clone -b develop https://github.com/apache/rocketmq.git
cd rocketmq
mvn -Prelease-all -DskipTests clean install -U
cd distribution/target/apache-rocketmq
```

2.启动rocketmq 

```

A.启动rocketmq的nameserver

nohup ./bin/mqnamesrv &

B.启动rockermq的broker代理

nohup ./bin/mqbroker -n localhost:9876 &

```

2.下载rocketmq管理控制台  

```
https://github.com/apache/rocketmq-externals.git

cd rocketmq-externals/rocketmq-console

mvn install -DskipTests  

```

3.启动rocketmq-console.jar包  

```
cd /Users/mac/software/flaginfo/software/rocketmq/rocketmq-externals/rocketmq-console/target

NAMESRV_ADDR=localhost:9876 java -jar rocketmq-console-ng-1.0.0.jar   
 
```

4.访问localhost:8080即可查看管理控制rocketmq  

```
如果8080被占用，可以在启动时候修改端口  

NAMESRV_ADDR=localhost:9876 java -jar rocketmq-console-ng-1.0.0.jar --server.port=9090  

```