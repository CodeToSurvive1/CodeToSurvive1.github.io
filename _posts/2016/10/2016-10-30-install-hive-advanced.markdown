---
layout: "post"
title: "hive安装，远程模式"
date: "2016-10-30 09:04"
category: "hive"
tags: [大数据,hive]
---


#### hive远程模式安装步骤		

远程模式，特点是：hive服务和metastore在不同的进程内，可能是不同的机器。
该模式需要将hive.metastore.local设置为false，并将hive.metastore.uris设置为metastore服务器URI，如有多个metastore服务器，URI之间用逗号分隔。metastore服务器URI的格式为thrift://hostort	

下面配置方式为，将mac电脑作为hive的服务端程序，centos作为远程客户端程序来搭建		

1.配置hive服务端程序配置 		

下面还是使用[hive安装，内嵌模式及本地模式](https://codetosurvive1.github.io/posts/install-hive-basic.html)中的配置，

配置修改为mysql数据库，这样可以支持多会话连接,在conf目录下新建hive-site.xml文件并添加如下内容    


```xml

cd /Users/mac/software/newcdh/hive-0.13.1-cdh5.3.6/conf

touch hive-hive-site.xml

vi hive-hive-site.xml


<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>

	<property>
	  <name>javax.jdo.option.ConnectionURL</name>
	  <value>jdbc:mysql://localhost:3306/hive_metastore?createDatabaseIfNotExist=true</value>
	  <description>mysql连接地址</description>
	</property>

	<property>
	  <name>javax.jdo.option.ConnectionDriverName</name>
	  <value>com.mysql.jdbc.Driver</value>
	  <description>mysql驱动类</description>
	</property>

	<property>
	  <name>javax.jdo.option.ConnectionUserName</name>
	  <value>hive</value>
	  <description>刚刚创建的用户名hive</description>
	</property>

	<property>
	  <name>javax.jdo.option.ConnectionPassword</name>
	  <value>123456</value>
	  <description>hive用户对应的mysql密码</description>
	</property>

</configuration>


```

同时需要讲mysql驱动程序拷贝到lib目录下，否则会报驱动程序找不到的错误	
cp ../../cdh/hive-0.13.1-cdh5.3.6/lib/mysql-connector-java-5.1.27-bin.jar ./lib/



2.配置客户端程序 			

由于本人有使用centos虚拟机，因此这里直接使用centos虚拟机作为客户端来访问上面的mac配置的hive服务端程序 	

2.1将hive安装包拷贝到centos虚拟机中 	

在mac中拷贝hive安装程序到centos中，下面的192.168.36.129为centos的ip地址，hadoop为centos虚拟机用户名，拷贝到centos中的/home/hadoop/bigdata/software目录中 		

```xml

scp hive-0.13.1-cdh5.3.6.tar.gz hadoop@192.168.36.129:/home/hadoop/bigdata/software
hive-0.13.1-cdh5.3.6.tar.gz   100%   61MB  61.1MB/s   00:01 

```

2.2进入centos系统中进行相关配置  	



```xml
解压hive安装程序到cdh目录下
tar -zxvf hive-0.13.1-cdh5.3.6.tar.gz -C ../cdh/

查看centos中的java环境变量
echo $JAVA_HOME
/home/hadoop/bigdata/cdh/jdk1.7.0_67

cd /home/hadoop/bigdata/cdh/hive-0.13.1-cdh5.3.6

cp conf/hive-env.sh.template conf/hive-env.sh

cp conf/hive-default.xml.template conf/hive-site.xml

修改hive-env.sh,配置为centos中的hadoop安装目录

HADOOP_HOME=/home/hadoop/bigdata/cdh/hadoop-2.5.0-cdh5.3.6
export HIVE_CONF_DIR=/home/hadoop/bigdata/cdh/hive-0.13.1-cdh5.3.6/conf

修改hive-site.xml，注意这里的ip是mac的ip

<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>


<configuration>
  
  <property>  
    <name>hive.metastore.warehouse.dir</name>  
    <value>/user/hive/warehouse</value>  
  </property>  
     
  <property>  
    <name>hive.metastore.local</name>  
    <value>false</value>  
  </property>  
    
  <property>  
    <name>hive.metastore.uris</name>  
    <value>thrift://172.31.218.249:9083</value>  
  </property>  

</configuration>



```


3.启动mac电脑的hdfs文件系统及hive的服务端程序 		

mac电脑上  	

```xml


启动hdfs服务		

```xml

cd software/cdh/hadoop-2.5.0-cdh5.3.6/

./sbin/start-dfs.sh 

```

启动hive服务端metastore程序 		

```

./bin/hive --service metastore
Starting Hive Metastore Server

```

进入centos，启动hive客户端程序  	

```xml

[hadoop@bigdata1 hive-0.13.1-cdh5.3.6]$ ./bin/hive
16/10/29 19:06:37 WARN conf.HiveConf: DEPRECATED: Configuration property hive.metastore.local no longer has any effect. Make sure to provide a valid value for hive.metastore.uris if you are connecting to a remote metastore.

Logging initialized using configuration in jar:file:/home/hadoop/bigdata/cdh/hive-0.13.1-cdh5.3.6/lib/hive-common-0.13.1-cdh5.3.6.jar!/hive-log4j.properties
hive> show databases;
OK
default
Time taken: 0.59 seconds, Fetched: 1 row(s)
hive> use default;
OK
Time taken: 0.039 seconds
hive> show tables;
OK
sample_07
sample_08
xxx
Time taken: 0.067 seconds, Fetched: 3 row(s)
hive> 


```

在mac上启动hive客户端查看是否与centos看到的结果一致 		

```

mac:hive-0.13.1-cdh5.3.6 mac$ ./bin/hive

Logging initialized using configuration in file:/Users/mac/software/newcdh/hive-0.13.1-cdh5.3.6/conf/hive-log4j.properties
hive (default)> show databases;
OK
database_name
default
Time taken: 0.746 seconds, Fetched: 1 row(s)
hive (default)> use default;
OK
Time taken: 0.015 seconds
hive (default)> show tables;
OK
tab_name
sample_07
sample_08
xxx


```

到此搭建完毕  		


参考连接：

[http://bi.dataguru.cn/thread-32711-1-1.html](http://bi.dataguru.cn/thread-32711-1-1.html)



