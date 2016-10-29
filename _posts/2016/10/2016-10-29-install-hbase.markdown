---
layout: "post"
title: "hbase安装"
date: "2016-10-29 15:31"
category: "hbase"
tags: [大数据,hbase]
---



1.首先去cdh官网下载[hbase](http://archive.cloudera.com/cdh5/cdh/5/hbase-0.98.6-cdh5.3.6.tar.gz)	

2.解压下载后的hbase-0.98.6-cdh5.3.6.tar.gz 		

```xml

tar -zxvf hbase-0.98.6-cdh5.3.6.tar.gz -C newcdh/

```

3.切换到conf目录下修改配置文件信息 	

```xml


tar -zxvf hbase-0.98.6-cdh5.3.6.tar.gz -C newcdh/

cd /Users/mac/software/newcdh/hbase-0.98.6-cdh5.3.6/conf


```

4.修改环境文件hbase-env.sh  	

修改下面几项信息 	

```xml

export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home

默认在tmp目录下，避免因为重启导致问题
export HBASE_PID_DIR=/Users/mac/software/newcdh/hbase-0.98.6-cdh5.3.6/pids

默认为true，也就是说让hbase使用子集的zookeeper，我们这里不使用自己管理，我们交给zookeeper集群管理

export HBASE_MANAGES_ZK=false


```

5.修改regionservers文件，将所有的regionservers节点添加进来		

```xml

mac.cn


```

6.创建本地数据目录  

mkdir -p data/tmp

7.修改配置文件hbase-site.xml文件,配置如下所示 	

```xml

<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
/**
 *
 * Licensed to the Apache Software Foundation (ASF) under one
 * or more contributor license agreements.  See the NOTICE file
 * distributed with this work for additional information
 * regarding copyright ownership.  The ASF licenses this file
 * to you under the Apache License, Version 2.0 (the
 * "License"); you may not use this file except in compliance
 * with the License.  You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
-->
<configuration>

  <property >
    <name>hbase.tmp.dir</name>
    <value>/Users/mac/software/newcdh/hbase-0.98.6-cdh5.3.6/data/tmp</value>
    <description>本地文件系统的临时目录.</description>
  </property>

  <property >
    <name>hbase.rootdir</name>
    <value>hdfs://mac.cn:8020/hbase</value>
    <description>region servers共享目录，hbase持久化的hdfs文件系统路径，需要完整路径.</description>
  </property>

  <property >
    <name>hbase.cluster.distributed</name>
    <value>true</value>
    <description>是否是分布式启动，如果不是分布式，则所有的hbase和zookeeper进程会在同一个jvm进程中启动
    </description>
  </property>

  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>mac.cn</value>
    <description>配置zookeeper的集群访问节点</description>

  </property>

  <property>
    <name>hbase.regionserver.codecs</name>
    <value>gz</value>
  </property>

</configuration>



```

8.首先启动zookeeper节点,如果没有配置zookeeper的可以参考[连接](https://codetosurvive1.github.io/posts/install-zookeeper.html) 	   

```xml

./bin/zkServer.sh start
JMX enabled by default
Using config: /Users/mac/software/newcdh/zookeeper-3.4.5-cdh5.3.6/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED


```
  
9.启动hbase进程       

```xml

首先启动hmaster进程(关闭stop)   

./bin/hbase-daemon.sh start master
starting master, logging to /Users/mac/software/cdh/hbase-0.98.6-cdh5.3.6/logs/hbase-mac-master-mac.local.out

验证是否启动      

mac:hbase-0.98.6-cdh5.3.6 mac$ jps
12656 HMaster
11314 QuorumPeerMain
3906 DataNode
4005 SecondaryNameNode
12439 Main
12686 Jps
3823 NameNode

启动hregionserver进程(关闭stop)   
mac:hbase-0.98.6-cdh5.3.6 mac$ ./bin/hbase-daemon.sh start regionserver
starting regionserver, logging to /Users/mac/software/cdh/hbase-0.98.6-cdh5.3.6/logs/hbase-mac-regionserver-mac.local.out

验证是否启动
mac:hbase-0.98.6-cdh5.3.6 mac$ jps
12656 HMaster
11314 QuorumPeerMain
3906 DataNode
12756 Jps
4005 SecondaryNameNode
12439 Main
12732 HRegionServer
3823 NameNode


````

或者通过下面的方式启动   

```xml

非后台进程启动(关闭stop)
./bin/hbase master start

非后台进程启动(关闭stop)
./bin/hbase regionserver start

```

10.打开hbase shell客户端查看是否启动成功，是否能够访问hbase的相关表数据     

```xml

 ./bin/hbase shell
2016-10-29 16:19:44,597 INFO  [main] Configuration.deprecation: hadoop.native.lib is deprecated. Instead, use io.native.lib.available
HBase Shell; enter 'help<RETURN>' for list of supported commands.
Type "exit<RETURN>" to leave the HBase Shell
Version 0.98.6-cdh5.3.6, rUnknown, Tue Jul 28 15:17:11 PDT 2015

list_namespace
NAMESPACE                                                                       
default                                                                         
hbase                                                                           
2 row(s) in 0.1730 seconds

hbase(main):006:0> list
TABLE                                                                           
student                                                                         
1 row(s) in 0.0280 seconds

=> ["student"]

```


