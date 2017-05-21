---
layout: "post"
title: "oozie安装"
date: "2017-03-28 19:17"
category: "oozie"
tags: [oozie,bigdata]

---

#### oozie安装    

1.解压oozie  

```
tar -zxvf ../software/oozie-4.0.0-cdh5.3.6.tar.gz -C .

cd oozie-4.0.0-cdh5.3.6/

```

2.关闭hadoop集群，然后配置hadoop配置目录下的core-site.xml文件   

vi etc/hadoop/core-site.xml   

```
<!-- OOZIE -->
<property>
  <name>hadoop.proxyuser.mac.hosts</name>
  <value>localhost,127.0.0.1,centos1,centos2,centos3</value>
</property>
<property>
  <name>hadoop.proxyuser.mac.groups</name>
  <value>*</value>
</property>
```    

然后复制该文件到集群中的其他两个机器中centos2和centos3中

```
scp etc/hadoop/core-site.xml centos2:/opt/cdh/hadoop-2.5.0-cdh5.3.6/etc/hadoop/
core-site.xml                                 100% 1260     1.2KB/s   00:00    

[mac@centos1 hadoop-2.5.0-cdh5.3.6]$ scp etc/hadoop/core-site.xml centos3:/opt/cdh/hadoop-2.5.0-cdh5.3.6/etc/hadoop/
core-site.xml                                 100% 1260     1.2KB/s   00:00    
[mac@centos1 hadoop-2.5.0-cdh5.3.6]$
```   

3.启动hadoop集群    
4.解压oozie目录下的hadooplibs的tar包,在oozie-4.0.0-cdh5.3.6/文件夹中包含了hadooplibs文件夹,其中的hadooplib-2.5.0-cdh5.3.6.oozie-4.0.0-cdh5.3.6是hadoop2.x所使用的包   

```
tar -zxvf oozie-hadooplibs-4.0.0-cdh5.3.6.tar.gz -C .
cd oozie-4.0.0-cdh5.3.6/

[mac@centos1 oozie-4.0.0-cdh5.3.6]$ ll
总用量 4
drwxr-xr-x. 4 mac mac 4096 7月  29 2015 hadooplibs

ll hadooplibs/
总用量 8
drwxr-xr-x. 2 mac mac 4096 7月  29 2015 hadooplib-2.5.0-cdh5.3.6.oozie-4.0.0-cdh5.3.6
drwxr-xr-x. 2 mac mac 4096 7月  29 2015 hadooplib-2.5.0-mr1-cdh5.3.6.oozie-4.0.0-cdh5.3.6

```

5.在oozie目录下创建libext目录   

```
pwd
/opt/cdh/oozie-4.0.0-cdh5.3.6

mkdir libext
/opt/cdh/oozie-4.0.0-cdh5.3.6

```

6.将4部解压的所使用的hadoop的版本中的所有的jars包及下载好的extjs包拷贝到libext目录下      

```
cp oozie-4.0.0-cdh5.3.6/hadooplibs/hadooplib-2.5.0-cdh5.3.6.oozie-4.0.0-cdh5.3.6/* ./libext/

cp /opt/software/ext-2.2.zip ./libext/
```

7.生成war包，用来监控oozie定时程序的web端页面   

```
./bin/oozie-setup.sh  prepare-war
  setting CATALINA_OPTS="$CATALINA_OPTS -Xmx1024m"

INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/activation-1.1.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/apacheds-i18n-2.0.0-M15.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/apacheds-kerberos-codec-2.0.0-M15.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/api-asn1-api-1.0.0-M20.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/api-util-1.0.0-M20.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/avro-1.7.6-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/aws-java-sdk-1.7.4.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-beanutils-1.7.0.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-beanutils-core-1.8.0.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-cli-1.2.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-codec-1.4.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-collections-3.2.1.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-compress-1.4.1.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-configuration-1.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-digester-1.8.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-httpclient-3.1.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-io-2.4.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-lang-2.4.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-logging-1.1.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-math3-3.1.1.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/commons-net-3.1.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/curator-client-2.6.0.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/curator-framework-2.6.0.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/curator-recipes-2.5.0.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/gson-2.2.4.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/guava-11.0.2.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-annotations-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-auth-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-aws-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-client-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-common-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-hdfs-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-mapreduce-client-app-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-mapreduce-client-common-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-mapreduce-client-core-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-mapreduce-client-jobclient-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-mapreduce-client-shuffle-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-yarn-api-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-yarn-client-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-yarn-common-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/hadoop-yarn-server-common-2.5.0-cdh5.3.6.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/httpclient-4.2.5.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/httpcore-4.2.5.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jackson-annotations-2.2.3.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jackson-core-2.2.3.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jackson-core-asl-1.8.8.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jackson-databind-2.2.3.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jackson-jaxrs-1.8.8.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jackson-mapper-asl-1.8.8.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jackson-xc-1.8.8.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jaxb-api-2.2.2.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jersey-client-1.9.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jersey-core-1.9.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jetty-util-6.1.26.cloudera.2.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/jsr305-1.3.9.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/leveldbjni-all-1.8.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/log4j-1.2.16.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/netty-3.6.2.Final.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/paranamer-2.3.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/protobuf-java-2.5.0.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/servlet-api-2.5.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/slf4j-api-1.7.5.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/slf4j-log4j12-1.7.5.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/snappy-java-1.0.4.1.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/stax-api-1.0-2.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/xmlenc-0.52.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/xz-1.0.jar
INFO: Adding extension: /opt/cdh/oozie-4.0.0-cdh5.3.6/libext/zookeeper-3.4.5-cdh5.3.6.jar


New Oozie WAR file with added 'ExtJS library, JARs' at /opt/cdh/oozie-4.0.0-cdh5.3.6/oozie-server/webapps/oozie.war


INFO: Oozie is ready to be started
```

8.将oozie跑各种定时程序所使用的jar包拷贝到hdfs上，比如hive，pig，sqoop  

```
./bin/oozie-setup.sh sharelib create  -fs hdfs://centos1:8020 -locallib oozie-sharelib-4.0.0-cdh5.3.6-yarn.tar.gz

 setting CATALINA_OPTS="$CATALINA_OPTS -Xmx1024m"
log4j:WARN No appenders could be found for logger (org.apache.hadoop.util.Shell).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/opt/cdh/oozie-4.0.0-cdh5.3.6/libtools/slf4j-simple-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/opt/cdh/oozie-4.0.0-cdh5.3.6/libtools/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/opt/cdh/oozie-4.0.0-cdh5.3.6/libext/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.SimpleLoggerFactory]
the destination path for sharelib is: /user/mac/share/lib/lib_20170328202237
```

9.创建oozie的数据库，初始化表信息    


```

./bin/oozie-setup.sh db create -run
  setting CATALINA_OPTS="$CATALINA_OPTS -Xmx1024m"

Validate DB Connection
DONE
Check DB schema does not exist
DONE
Check OOZIE_SYS table does not exist
DONE
Create SQL schema
DONE
Create OOZIE_SYS table
DONE

Oozie DB has been created for Oozie version '4.0.0-cdh5.3.6'


The SQL commands have been written to: /tmp/ooziedb-827033505718254178.sql

```

10.启动oozie进程    

```
./bin/oozied.sh start

Setting OOZIE_HOME:          /opt/cdh/oozie-4.0.0-cdh5.3.6
Setting OOZIE_CONFIG:        /opt/cdh/oozie-4.0.0-cdh5.3.6/conf
Sourcing:                    /opt/cdh/oozie-4.0.0-cdh5.3.6/conf/oozie-env.sh
  setting CATALINA_OPTS="$CATALINA_OPTS -Xmx1024m"
Setting OOZIE_CONFIG_FILE:   oozie-site.xml
Setting OOZIE_DATA:          /opt/cdh/oozie-4.0.0-cdh5.3.6/data
Setting OOZIE_LOG:           /opt/cdh/oozie-4.0.0-cdh5.3.6/logs
Setting OOZIE_LOG4J_FILE:    oozie-log4j.properties
Setting OOZIE_LOG4J_RELOAD:  10
Setting OOZIE_HTTP_HOSTNAME: centos1.com
Setting OOZIE_HTTP_PORT:     11000
Setting OOZIE_ADMIN_PORT:     11001
Setting OOZIE_HTTPS_PORT:     11443
Setting OOZIE_BASE_URL:      http://centos1.com:11000/oozie
Setting CATALINA_BASE:       /opt/cdh/oozie-4.0.0-cdh5.3.6/oozie-server
Setting OOZIE_HTTPS_KEYSTORE_FILE:     /home/mac/.keystore
Setting OOZIE_HTTPS_KEYSTORE_PASS:     password
Setting OOZIE_INSTANCE_ID:       centos1.com
Setting CATALINA_OUT:        /opt/cdh/oozie-4.0.0-cdh5.3.6/logs/catalina.out
Setting CATALINA_PID:        /opt/cdh/oozie-4.0.0-cdh5.3.6/oozie-server/temp/oozie.pid

Using   CATALINA_OPTS:        -Xmx1024m -Dderby.stream.error.file=/opt/cdh/oozie-4.0.0-cdh5.3.6/logs/derby.log
Adding to CATALINA_OPTS:     -Doozie.home.dir=/opt/cdh/oozie-4.0.0-cdh5.3.6 -Doozie.config.dir=/opt/cdh/oozie-4.0.0-cdh5.3.6/conf -Doozie.log.dir=/opt/cdh/oozie-4.0.0-cdh5.3.6/logs -Doozie.data.dir=/opt/cdh/oozie-4.0.0-cdh5.3.6/data -Doozie.instance.id=centos1.com -Doozie.config.file=oozie-site.xml -Doozie.log4j.file=oozie-log4j.properties -Doozie.log4j.reload=10 -Doozie.http.hostname=centos1.com -Doozie.admin.port=11001 -Doozie.http.port=11000 -Doozie.https.port=11443 -Doozie.base.url=http://centos1.com:11000/oozie -Doozie.https.keystore.file=/home/mac/.keystore -Doozie.https.keystore.pass=password -Djava.library.path=

Using CATALINA_BASE:   /opt/cdh/oozie-4.0.0-cdh5.3.6/oozie-server
Using CATALINA_HOME:   /opt/cdh/oozie-4.0.0-cdh5.3.6/oozie-server
Using CATALINA_TMPDIR: /opt/cdh/oozie-4.0.0-cdh5.3.6/oozie-server/temp
Using JRE_HOME:        /opt/modules/jdk1.7.0_67
Using CLASSPATH:       /opt/cdh/oozie-4.0.0-cdh5.3.6/oozie-server/bin/bootstrap.jar
Using CATALINA_PID:    /opt/cdh/oozie-4.0.0-cdh5.3.6/oozie-server/temp/oozie.pid
```

11.进入tomcat监控页面，端口为11000http://centos1:11000/oozie/或者通过命令查看  

![](../assets/2017/03/2017-03-28_20-35-48.png)

看到上面页面证明环境搭建成功  

#### 验证环境的可用性   

1.解压例子程序   

```
tar -zxvf oozie-examples.tar.gz
```

2.将解压好的example文件上传到hdfs中    

```
../hadoop-2.5.0-cdh5.3.6/bin/hdfs dfs -put examples/ examples
```

3.修改配置内容    

```

vi examples/apps/map-reduce/job.properties

```

内容如下:

```
nameNode=hdfs://centos1:8020
jobTracker=centos2:8032
queueName=default
examplesRoot=examples

oozie.wf.application.path=${nameNode}/user/${user.name}/${examplesRoot}/apps/map-reduce/workflow.xml
outputDir=map-reduce
```

3.运行mapreduce的job   

```
./bin/oozie job -oozie http://centos1:11000/oozie -config examples/apps/map-reduce/job.properties -run
job: 0000000-170328202540397-oozie-mac-W
```

4.报错如下所示  

```
File /user/mac/share/lib does not exist
```

修改配置文件oozie-site.xml中的属性oozie.service.HadoopAccessorService.hadoop.configurations   

```
<property>
  <name>oozie.service.HadoopAccessorService.hadoop.configurations</name>
  <value>*=hadoop-conf</value>
  <description>
      Comma separated AUTHORITY=HADOOP_CONF_DIR, where AUTHORITY is the HOST:PORT of
      the Hadoop service (JobTracker, HDFS). The wildcard '*' configuration is
      used when there is no exact match for an authority. The HADOOP_CONF_DIR contains
      the relevant Hadoop *-site.xml files. If the path is relative is looked within
      the Oozie configuration directory; though the path can be absolute (i.e. to point
      to Hadoop client conf/ directories in the local filesystem.
  </description>
</property>

```

修改为  

```
<property>
    <name>oozie.service.HadoopAccessorService.hadoop.configurations</name>
    <value>*=/opt/cdh/hadoop-2.5.0-cdh5.3.6/etc/hadoop</value>
    <description>
        Comma separated AUTHORITY=HADOOP_CONF_DIR, where AUTHORITY is the HOST:PORT of
        the Hadoop service (JobTracker, HDFS). The wildcard '*' configuration is
        used when there is no exact match for an authority. The HADOOP_CONF_DIR contains
        the relevant Hadoop *-site.xml files. If the path is relative is looked within
        the Oozie configuration directory; though the path can be absolute (i.e. to point
        to Hadoop client conf/ directories in the local filesystem.
    </description>
</property>
```
5.重启oozie   

```
./bin/oozied.sh stop
./bin/oozied.sh start

```

重启后重新提交job  
```
./bin/oozie job -oozie http://centos1:11000/oozie -config examples/apps/map-reduce/job.properties -run
job: 0000000-170328221752755-oozie-mac-W
```

验证结果是否正确  

```

```
