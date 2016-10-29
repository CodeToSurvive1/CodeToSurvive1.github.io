---
layout: "post"
title: "nginx+flume+hadoop实战"
date: "2016-10-29 09:03"
category: ["hadoop","flume"]
tags: [flume,hadoop]

---

#### 实战说明    

本文通过nginx服务器收集访问日志，并通过flume实时抽取日志到hdfs文件系统中   

------

#### nginx安装    

1.安装nginx   

```

brew install nginx

Docroot is: /usr/local/var/www

The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080 so that
nginx can run without sudo.

nginx will load all files in /usr/local/etc/nginx/servers/.

To have launchd start nginx now and restart at login:
  brew services start nginx
Or, if you don't want/need a background service you can just run:
  nginx
==> Summary
🍺  /usr/local/Cellar/nginx/1.10.2_1


```

启动浏览器查看8080端口是否成功，http://localhost:8080   

2.这里使用自己的github个人网站，直接将编译过的_site文件夹拷贝到nginx的html目录下(不懂的可以网上搜索)  

```

cd /usr/local/Cellar/nginx/1.10.2_1/html

mac:html mac$ cp -r /Users/mac/software/github/macaca/CodeToSurvive1.github.io/_site/* ./
mac:html mac$ ls
404.html	about.html	atom.xml	faqs.html	index.html	macaca.html	tags.html
CNAME		archives.html	categories.html	favicon.ico	js		posts
README.md	assets		css		fonts		links.html	robots.txt

```

3.修改nginx配置文件，添加日志输出格式及日志输出文件   

添加日志格式    

```xml

log_format  lf  '$remote_addr^A$msec^A$http_host^A$request_uri';

```

在nginx目录下新建日志文件夹    

```xml

/usr/local/Cellar/nginx/1.10.2_1
pwd

/usr/local/Cellar/nginx/1.10.2_1

mkdir logs
cd logs/
pwd
/usr/local/Cellar/nginx/1.10.2_1/logs

```

添加日志输出文件    


```xml

access_log  /usr/local/Cellar/nginx/1.10.2_1/logs/host.access.log  lf;

```

修改后的nginx.conf配置文件如下所示    


```

worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    log_format  lf  '$remote_addr^A$msec^A$http_host^A$request_uri';
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       8080;
        server_name  localhost;
        access_log  /usr/local/Cellar/nginx/1.10.2_1/logs/host.access.log  lf;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    include servers/*;
}


```

4.将nginx进行平滑重启    

```

nginx -s reload

```

5.访问页面并查看日志文件中的输出数据   

```

mac:logs mac$ tail -f host.access.log
127.0.0.1^A1477709204.499^Alocalhost:8080^A/fonts/glyphicons-halflings-regular.ttf
127.0.0.1^A1477709205.588^Alocalhost:8080^A/favicon.ico
127.0.0.1^A1477709231.377^Alocalhost:8080^A/posts/nginx-flume-hadoop.html
127.0.0.1^A1477709231.392^Alocalhost:8080^A/css/bootstrap.min.css
127.0.0.1^A1477709231.392^Alocalhost:8080^A/css/blog.css
127.0.0.1^A1477709231.392^Alocalhost:8080^A/css/prettify.css
127.0.0.1^A1477709231.392^Alocalhost:8080^A/js/jquery-1.11.0.js
127.0.0.1^A1477709231.392^Alocalhost:8080^A/js/bootstrap.min.js
127.0.0.1^A1477709231.392^Alocalhost:8080^A/js/run_prettify.js
127.0.0.1^A1477709231.491^Alocalhost:8080^A/fonts/glyphicons-halflings-regular.ttf







127.0.0.1^A1477709735.875^Alocalhost:8080^A/
127.0.0.1^A1477709735.889^Alocalhost:8080^A/css/bootstrap.min.css
127.0.0.1^A1477709735.901^Alocalhost:8080^A/css/blog.css
127.0.0.1^A1477709735.901^Alocalhost:8080^A/js/bootstrap.min.js
127.0.0.1^A1477709735.902^Alocalhost:8080^A/js/run_prettify.js
127.0.0.1^A1477709735.903^Alocalhost:8080^A/js/jquery-1.11.0.js
127.0.0.1^A1477709735.903^Alocalhost:8080^A/css/prettify.css
127.0.0.1^A1477709736.785^Alocalhost:8080^A/fonts/glyphicons-halflings-regular.ttf
127.0.0.1^A1477709742.728^Alocalhost:8080^A/posts/nginx-flume-hadoop.html
127.0.0.1^A1477709748.243^Alocalhost:8080^A/posts/macaca-plugin-develop.html
127.0.0.1^A1477709749.219^Alocalhost:8080^A/posts/mac-redis-install.html
127.0.0.1^A1477709750.355^Alocalhost:8080^A/posts/mcaca-master-slave-no-docker.html
127.0.0.1^A1477709750.416^Alocalhost:8080^A/assets/2016-10-28_14-00-51.png

```

------

#### flume环境搭建    

1.下载flume   

由于本人使用的cdh版本的hadoop，因此这里配套下载cdh版本的[flume](http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.5.0-cdh5.3.6.tar.gz)  

2.解压缩即可   

```shell

tar -zxvf flume-ng-1.5.0-cdh5.3.6.tar.gz  
cd apache-flume-1.5.0-cdh5.3.6-bin/
pwd
/Users/mac/software/apache-flume-1.5.0-cdh5.3.6-bin

```

3.编写flume文件   

```properties

#定义source，channels，sinks三大组件名称
agent.sources = tailSource
agent.channels = memoryChannel
agent.sinks = hdfsSink logger

#定义source的属性为exec执行程序
agent.sources.tailSource.type =exec
agent.sources.tailSource.channels = memoryChannel
agent.sources.tailSource.command=tail -f /usr/local/Cellar/nginx/1.10.2_1/logs/host.access.log


#定义sinks的属性为hdfs相关配置
agent.sinks.hdfsSink.type = hdfs
agent.sinks.hdfsSink.channel = memoryChannel
agent.sinks.hdfsSink.hdfs.path = hdfs://mac.cn:8020/user/mac/logs/%Y/%m/%d/
agent.sinks.hdfsSink.hdfs.useLocalTimeStamp = true


#定义sinks的属性为hdfs相关配置
agent.sinks.logger.type = logger
agent.sinks.logger.channel = memoryChannel

#定义channels的属性为内存相关配置
agent.channels.memoryChannel.type = memory
agent.channels.memoryChannel.capacity = 100

```

4.如果上面不加agent.sinks.hdfsSink.hdfs.useLocalTimeStamp = true这句话会在启动过程中报错，如下   

```
2016-10-29 12:23:33,775 (SinkRunner-PollingRunner-DefaultSinkProcessor) [ERROR - org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:467)] process failed
java.lang.NullPointerException: Expected timestamp in the Flume event headers, but it was null
	at com.google.common.base.Preconditions.checkNotNull(Preconditions.java:204)
	at org.apache.flume.formatter.output.BucketPath.replaceShorthand(BucketPath.java:224)
	at org.apache.flume.formatter.output.BucketPath.escapeString(BucketPath.java:422)
	at org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:388)
	at org.apache.flume.sink.DefaultSinkProcessor.process(DefaultSinkProcessor.java:68)
	at org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:147)
	at java.lang.Thread.run(Thread.java:745)
2016-10-29 12:23:33,775 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:23:33,777 (SinkRunner-PollingRunner-DefaultSinkProcessor) [ERROR - org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:160)] Unable to deliver event. Exception follows.
org.apache.flume.EventDeliveryException: java.lang.NullPointerException: Expected timestamp in the Flume event headers, but it was null
	at org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:471)
	at org.apache.flume.sink.DefaultSinkProcessor.process(DefaultSinkProcessor.java:68)
	at org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:147)
	at java.lang.Thread.run(Thread.java:745)
Caused by: java.lang.NullPointerException: Expected timestamp in the Flume event headers, but it was null
	at com.google.common.base.Preconditions.checkNotNull(Preconditions.java:204)
	at org.apache.flume.formatter.output.BucketPath.replaceShorthand(BucketPath.java:224)
	at org.apache.flume.formatter.output.BucketPath.escapeString(BucketPath.java:422)
	at org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:388)
	... 3 more
```

5.启动flume   

不要忘记首先启动hadoop相关进程  


./bin/flume-ng agent -n agent -c conf/ -Dflume.root.logger=INFO,console  -f conf/flume-conf.properties    

```xml

2016-10-29 12:27:44,524 (conf-file-poller-0) [INFO - org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:133)] Reloading configuration file:conf/flume-conf.xml
2016-10-29 12:27:44,532 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:logger
2016-10-29 12:27:44,532 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:hdfsSink
2016-10-29 12:27:44,532 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:hdfsSink
2016-10-29 12:27:44,532 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:hdfsSink
2016-10-29 12:27:44,532 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:931)] Added sinks: hdfsSink logger Agent: agent
2016-10-29 12:27:44,532 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:hdfsSink
2016-10-29 12:27:44,532 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:logger
2016-10-29 12:27:44,549 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration.validateConfiguration(FlumeConfiguration.java:141)] Post-validation flume configuration contains configuration for agents: [agent]
2016-10-29 12:27:44,549 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:145)] Creating channels
2016-10-29 12:27:44,557 (conf-file-poller-0) [INFO - org.apache.flume.channel.DefaultChannelFactory.create(DefaultChannelFactory.java:42)] Creating instance of channel memoryChannel type memory
2016-10-29 12:27:44,564 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:200)] Created channel memoryChannel
2016-10-29 12:27:44,566 (conf-file-poller-0) [INFO - org.apache.flume.source.DefaultSourceFactory.create(DefaultSourceFactory.java:41)] Creating instance of source tailSource, type exec
2016-10-29 12:27:44,580 (conf-file-poller-0) [INFO - org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:42)] Creating instance of sink: logger, type: logger
2016-10-29 12:27:44,582 (conf-file-poller-0) [INFO - org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:42)] Creating instance of sink: hdfsSink, type: hdfs
2016-10-29 12:27:44,869 (conf-file-poller-0) [WARN - org.apache.hadoop.util.NativeCodeLoader.<clinit>(NativeCodeLoader.java:62)] Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2016-10-29 12:27:45,079 (conf-file-poller-0) [INFO - org.apache.flume.sink.hdfs.HDFSEventSink.authenticate(HDFSEventSink.java:559)] Hadoop Security enabled: false
2016-10-29 12:27:45,082 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.getConfiguration(AbstractConfigurationProvider.java:114)] Channel memoryChannel connected to [tailSource, logger, hdfsSink]
2016-10-29 12:27:45,093 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:138)] Starting new configuration:{ sourceRunners:{tailSource=EventDrivenSourceRunner: { source:org.apache.flume.source.ExecSource{name:tailSource,state:IDLE} }} sinkRunners:{logger=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@3cc13c74 counterGroup:{ name:null counters:{} } }, hdfsSink=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@7e0d961c counterGroup:{ name:null counters:{} } }} channels:{memoryChannel=org.apache.flume.channel.MemoryChannel{name: memoryChannel}} }
2016-10-29 12:27:45,098 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:145)] Starting Channel memoryChannel
2016-10-29 12:27:45,164 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: CHANNEL, name: memoryChannel: Successfully registered new MBean.
2016-10-29 12:27:45,165 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: CHANNEL, name: memoryChannel started
2016-10-29 12:27:45,169 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:173)] Starting Sink logger
2016-10-29 12:27:45,170 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:173)] Starting Sink hdfsSink
2016-10-29 12:27:45,170 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:184)] Starting Source tailSource
2016-10-29 12:27:45,171 (lifecycleSupervisor-1-2) [INFO - org.apache.flume.source.ExecSource.start(ExecSource.java:169)] Exec source starting with command:tail -f /usr/local/Cellar/nginx/1.10.2_1/logs/host.access.log
2016-10-29 12:27:45,172 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SINK, name: hdfsSink: Successfully registered new MBean.
2016-10-29 12:27:45,172 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SINK, name: hdfsSink started
2016-10-29 12:27:45,175 (lifecycleSupervisor-1-2) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SOURCE, name: tailSource: Successfully registered new MBean.
2016-10-29 12:27:45,175 (lifecycleSupervisor-1-2) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SOURCE, name: tailSource started
2016-10-29 12:27:49,179 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:27:49,180 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:27:49,180 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:27:49,180 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:27:49,180 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:27:49,181 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:27:49,181 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:27:49,181 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:27:49,181 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:27:49,189 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.HDFSSequenceFile.configure(HDFSSequenceFile.java:63)] writeFormat = Writable, UseRawLocalFileSystem = false
2016-10-29 12:27:49,231 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:261)] Creating hdfs://mac.cn:8020/user/mac/logs/2016/10/29//FlumeData.1477715269187.tmp


2016-10-29 12:28:15,180 (conf-file-poller-0) [INFO - org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:133)] Reloading configuration file:conf/flume-conf.xml
2016-10-29 12:28:15,181 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:logger
2016-10-29 12:28:15,224 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:hdfsSink
2016-10-29 12:28:15,224 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:hdfsSink
2016-10-29 12:28:15,224 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:hdfsSink
2016-10-29 12:28:15,225 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:931)] Added sinks: hdfsSink logger Agent: agent
2016-10-29 12:28:15,225 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:hdfsSink
2016-10-29 12:28:15,228 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1017)] Processing:logger
2016-10-29 12:28:15,235 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration.validateConfiguration(FlumeConfiguration.java:141)] Post-validation flume configuration contains configuration for agents: [agent]
2016-10-29 12:28:15,235 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:145)] Creating channels
2016-10-29 12:28:15,236 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:200)] Created channel memoryChannel
2016-10-29 12:28:15,236 (conf-file-poller-0) [INFO - org.apache.flume.source.DefaultSourceFactory.create(DefaultSourceFactory.java:41)] Creating instance of source tailSource, type exec
2016-10-29 12:28:15,237 (conf-file-poller-0) [INFO - org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:42)] Creating instance of sink: logger, type: logger
2016-10-29 12:28:15,237 (conf-file-poller-0) [INFO - org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:42)] Creating instance of sink: hdfsSink, type: hdfs
2016-10-29 12:28:15,237 (conf-file-poller-0) [INFO - org.apache.flume.sink.hdfs.HDFSEventSink.authenticate(HDFSEventSink.java:559)] Hadoop Security enabled: false
2016-10-29 12:28:15,237 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.getConfiguration(AbstractConfigurationProvider.java:114)] Channel memoryChannel connected to [tailSource, logger, hdfsSink]
2016-10-29 12:28:15,238 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.stopAllComponents(Application.java:101)] Shutting down configuration: { sourceRunners:{tailSource=EventDrivenSourceRunner: { source:org.apache.flume.source.ExecSource{name:tailSource,state:START} }} sinkRunners:{logger=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@3cc13c74 counterGroup:{ name:null counters:{runner.backoffs.consecutive=5, runner.backoffs=6} } }, hdfsSink=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@7e0d961c counterGroup:{ name:null counters:{runner.backoffs.consecutive=4, runner.backoffs=5} } }} channels:{memoryChannel=org.apache.flume.channel.MemoryChannel{name: memoryChannel}} }
2016-10-29 12:28:15,238 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.stopAllComponents(Application.java:105)] Stopping Source tailSource
2016-10-29 12:28:15,238 (conf-file-poller-0) [INFO - org.apache.flume.lifecycle.LifecycleSupervisor.unsupervise(LifecycleSupervisor.java:171)] Stopping component: EventDrivenSourceRunner: { source:org.apache.flume.source.ExecSource{name:tailSource,state:START} }
2016-10-29 12:28:15,241 (conf-file-poller-0) [INFO - org.apache.flume.source.ExecSource.stop(ExecSource.java:192)] Stopping exec source with command:tail -f /usr/local/Cellar/nginx/1.10.2_1/logs/host.access.log
2016-10-29 12:28:15,242 (pool-5-thread-1) [INFO - org.apache.flume.source.ExecSource$ExecRunnable.run(ExecSource.java:376)] Command [tail -f /usr/local/Cellar/nginx/1.10.2_1/logs/host.access.log] exited with 143
2016-10-29 12:28:15,243 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:150)] Component type: SOURCE, name: tailSource stopped
2016-10-29 12:28:15,243 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:156)] Shutdown Metric for type: SOURCE, name: tailSource. source.start.time == 1477715265175
2016-10-29 12:28:15,243 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:162)] Shutdown Metric for type: SOURCE, name: tailSource. source.stop.time == 1477715295243
2016-10-29 12:28:15,244 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SOURCE, name: tailSource. src.append-batch.accepted == 0
2016-10-29 12:28:15,244 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SOURCE, name: tailSource. src.append-batch.received == 0
2016-10-29 12:28:15,244 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SOURCE, name: tailSource. src.append.accepted == 0
2016-10-29 12:28:15,244 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SOURCE, name: tailSource. src.append.received == 0
2016-10-29 12:28:15,244 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SOURCE, name: tailSource. src.events.accepted == 10
2016-10-29 12:28:15,245 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SOURCE, name: tailSource. src.events.received == 10
2016-10-29 12:28:15,245 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SOURCE, name: tailSource. src.open-connection.count == 0
2016-10-29 12:28:15,245 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.stopAllComponents(Application.java:115)] Stopping Sink logger
2016-10-29 12:28:15,245 (conf-file-poller-0) [INFO - org.apache.flume.lifecycle.LifecycleSupervisor.unsupervise(LifecycleSupervisor.java:171)] Stopping component: SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@3cc13c74 counterGroup:{ name:null counters:{runner.backoffs.consecutive=5, runner.backoffs=6} } }
2016-10-29 12:28:15,246 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.stopAllComponents(Application.java:115)] Stopping Sink hdfsSink
2016-10-29 12:28:15,246 (conf-file-poller-0) [INFO - org.apache.flume.lifecycle.LifecycleSupervisor.unsupervise(LifecycleSupervisor.java:171)] Stopping component: SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@7e0d961c counterGroup:{ name:null counters:{runner.backoffs.consecutive=4, runner.backoffs=5} } }
2016-10-29 12:28:15,246 (conf-file-poller-0) [INFO - org.apache.flume.sink.hdfs.HDFSEventSink.stop(HDFSEventSink.java:500)] Closing hdfs://mac.cn:8020/user/mac/logs/2016/10/29//FlumeData
2016-10-29 12:28:15,247 (conf-file-poller-0) [INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:390)] Closing hdfs://mac.cn:8020/user/mac/logs/2016/10/29//FlumeData.1477715269187.tmp
2016-10-29 12:28:15,279 (hdfs-hdfsSink-call-runner-4) [INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:656)] Renaming hdfs://mac.cn:8020/user/mac/logs/2016/10/29/FlumeData.1477715269187.tmp to hdfs://mac.cn:8020/user/mac/logs/2016/10/29/FlumeData.1477715269187
2016-10-29 12:28:15,288 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:150)] Component type: SINK, name: hdfsSink stopped
2016-10-29 12:28:15,288 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:156)] Shutdown Metric for type: SINK, name: hdfsSink. sink.start.time == 1477715265172
2016-10-29 12:28:15,288 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:162)] Shutdown Metric for type: SINK, name: hdfsSink. sink.stop.time == 1477715295288
2016-10-29 12:28:15,288 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SINK, name: hdfsSink. sink.batch.complete == 0
2016-10-29 12:28:15,288 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SINK, name: hdfsSink. sink.batch.empty == 5
2016-10-29 12:28:15,288 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SINK, name: hdfsSink. sink.batch.underflow == 1
2016-10-29 12:28:15,288 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SINK, name: hdfsSink. sink.connection.closed.count == 1
2016-10-29 12:28:15,289 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SINK, name: hdfsSink. sink.connection.creation.count == 1
2016-10-29 12:28:15,289 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SINK, name: hdfsSink. sink.connection.failed.count == 0
2016-10-29 12:28:15,289 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SINK, name: hdfsSink. sink.event.drain.attempt == 1
2016-10-29 12:28:15,289 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: SINK, name: hdfsSink. sink.event.drain.sucess == 1
2016-10-29 12:28:15,289 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.stopAllComponents(Application.java:125)] Stopping Channel memoryChannel
2016-10-29 12:28:15,290 (conf-file-poller-0) [INFO - org.apache.flume.lifecycle.LifecycleSupervisor.unsupervise(LifecycleSupervisor.java:171)] Stopping component: org.apache.flume.channel.MemoryChannel{name: memoryChannel}
2016-10-29 12:28:15,290 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:150)] Component type: CHANNEL, name: memoryChannel stopped
2016-10-29 12:28:15,290 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:156)] Shutdown Metric for type: CHANNEL, name: memoryChannel. channel.start.time == 1477715265165
2016-10-29 12:28:15,290 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:162)] Shutdown Metric for type: CHANNEL, name: memoryChannel. channel.stop.time == 1477715295290
2016-10-29 12:28:15,290 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: CHANNEL, name: memoryChannel. channel.capacity == 100
2016-10-29 12:28:15,290 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: CHANNEL, name: memoryChannel. channel.current.size == 0
2016-10-29 12:28:15,290 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: CHANNEL, name: memoryChannel. channel.event.put.attempt == 10
2016-10-29 12:28:15,291 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: CHANNEL, name: memoryChannel. channel.event.put.success == 10
2016-10-29 12:28:15,291 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: CHANNEL, name: memoryChannel. channel.event.take.attempt == 22
2016-10-29 12:28:15,291 (conf-file-poller-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.stop(MonitoredCounterGroup.java:178)] Shutdown Metric for type: CHANNEL, name: memoryChannel. channel.event.take.success == 10
2016-10-29 12:28:15,291 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:138)] Starting new configuration:{ sourceRunners:{tailSource=EventDrivenSourceRunner: { source:org.apache.flume.source.ExecSource{name:tailSource,state:IDLE} }} sinkRunners:{logger=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@2b01ef06 counterGroup:{ name:null counters:{} } }, hdfsSink=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@413efc8b counterGroup:{ name:null counters:{} } }} channels:{memoryChannel=org.apache.flume.channel.MemoryChannel{name: memoryChannel}} }
2016-10-29 12:28:15,300 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:145)] Starting Channel memoryChannel
2016-10-29 12:28:15,300 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:160)] Waiting for channel: memoryChannel to start. Sleeping for 500 ms
2016-10-29 12:28:15,300 (lifecycleSupervisor-1-6) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: CHANNEL, name: memoryChannel started
2016-10-29 12:28:15,804 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:173)] Starting Sink logger
2016-10-29 12:28:15,804 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:173)] Starting Sink hdfsSink
2016-10-29 12:28:15,804 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:184)] Starting Source tailSource
2016-10-29 12:28:15,805 (lifecycleSupervisor-1-9) [INFO - org.apache.flume.source.ExecSource.start(ExecSource.java:169)] Exec source starting with command:tail -f /usr/local/Cellar/nginx/1.10.2_1/logs/host.access.log
2016-10-29 12:28:15,805 (lifecycleSupervisor-1-7) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SINK, name: hdfsSink: Successfully registered new MBean.
2016-10-29 12:28:15,806 (lifecycleSupervisor-1-7) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SINK, name: hdfsSink started
2016-10-29 12:28:15,806 (lifecycleSupervisor-1-9) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SOURCE, name: tailSource: Successfully registered new MBean.
2016-10-29 12:28:15,806 (lifecycleSupervisor-1-9) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SOURCE, name: tailSource started
2016-10-29 12:28:19,813 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:28:19,813 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:28:19,814 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:28:19,814 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:28:19,814 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:28:19,815 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:28:19,814 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.HDFSSequenceFile.configure(HDFSSequenceFile.java:63)] writeFormat = Writable, UseRawLocalFileSystem = false
2016-10-29 12:28:19,815 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:28:19,816 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:28:19,816 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 31 32 37 2E 30 2E 30 2E 31 5E 41 31 34 37 37 37 127.0.0.1^A14777 }
2016-10-29 12:28:19,861 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:261)] Creating hdfs://mac.cn:8020/user/mac/logs/2016/10/29//FlumeData.1477715299815.tmp
2016-10-29 12:28:49,991 (hdfs-hdfsSink-roll-timer-0) [INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:390)] Closing hdfs://mac.cn:8020/user/mac/logs/2016/10/29//FlumeData.1477715299815.tmp
2016-10-29 12:28:50,008 (hdfs-hdfsSink-call-runner-4) [INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:656)] Renaming hdfs://mac.cn:8020/user/mac/logs/2016/10/29/FlumeData.1477715299815.tmp to hdfs://mac.cn:8020/user/mac/logs/2016/10/29/FlumeData.1477715299815
2016-10-29 12:28:50,014 (hdfs-hdfsSink-roll-timer-0) [INFO - org.apache.flume.sink.hdfs.HDFSEventSink$1.run(HDFSEventSink.java:402)] Writer callback called.

```

打开8080端口进行访问页面，后台日志会刷新数据，hdfs文件系统中新增文件如下：   

![后台日志](../assets/2016-10-29_12-39-42.png)  

![hdfs文件系统](../assets/2016-10-29_12-40-06.png)  

如果执行过程中报hdfs相关错误，估计是缺少hdfs相关的jar包，拷贝下面包到flume目录下的lib包目录中即可  

6.将hadoop的相关包放进lib目录下   

```shell

cd /Users/mac/software/apache-flume-1.5.0-cdh5.3.6-bin/lib  

cp ~/software/cdh/hadoop-2.5.0-cdh5.3.6/share/hadoop/common/hadoop-common-2.5.0-cdh5.3.6.jar ./

cp ~/software/cdh/hadoop-2.5.0-cdh5.3.6/share/hadoop/common/lib/commons-configuration-1.6.jar ./

cp ~/software/cdh/hadoop-2.5.0-cdh5.3.6/share/hadoop/common/lib/hadoop-auth-2.5.0-cdh5.3.6.jar ./

cp ~/software/cdh/hadoop-2.5.0-cdh5.3.6/share/hadoop/hdfs/hadoop-hdfs-2.5.0-cdh5.3.6.jar ./

```
