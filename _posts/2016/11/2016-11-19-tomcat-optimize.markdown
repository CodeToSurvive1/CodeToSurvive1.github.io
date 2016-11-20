---
layout: "post"
title: "tomcat性能调优 "
date: "2016-11-19 22:18"
category: "j2ee"
tags: [tomcat,j2ee]
---


1.修改connector的protocol模式 		

对于不同版本的tomcat，该值也不同，比如		

tomcat6，启动tomcat，测试下来发现默认是Http11Protocol：		

```xml

org.apache.coyote.http11.Http11Protocol - same as HTTP/1.1，阻塞式IO		
org.apache.coyote.http11.Http11NioProtocol - non blocking Java connector，非阻塞IO 		
org.apache.coyote.http11.Http11AprProtocol - the APR connector.		

```

启动tomcat6后查看后台日志，		

```xml

信息: Deploying web application directory ROOT
十一月 19, 2016 10:41:28 下午 org.apache.coyote.http11.Http11Protocol start
信息: Starting Coyote HTTP/1.1 on http-8080
十一月 19, 2016 10:41:28 下午 org.apache.jk.common.ChannelSocket init
信息: JK: ajp13 listening on /0.0.0.0:8009
十一月 19, 2016 10:41:28 下午 org.apache.jk.server.JkMain start
信息: Jk running ID=0 time=0/19  config=null
十一月 19, 2016 10:41:28 下午 org.apache.catalina.startup.Catalina start
信息: Server startup in 716 ms

```

发现默认是Http11Protocol，即为阻塞式io，因此这里是可以进行优化的 	

修改conf/server.xml文件中的配置 		

```xml

<Connector port="8080" protocol="HTTP/1.1" 
               connectionTimeout="20000" 
               redirectPort="8443" />

```

为

```xml

 <Connector port="8080" protocol="org.apache.coyote.http11.Http11NioProtocol" 
               connectionTimeout="20000" 
               redirectPort="8443" />

```

重新启动tomcat，查看日志  		

```xml

信息: Deploying web application directory ROOT
十一月 19, 2016 10:44:31 下午 org.apache.coyote.http11.Http11NioProtocol start
信息: Starting Coyote HTTP/1.1 on http-8080
十一月 19, 2016 10:44:31 下午 org.apache.jk.common.ChannelSocket init
信息: JK: ajp13 listening on /0.0.0.0:8009
十一月 19, 2016 10:44:31 下午 org.apache.jk.server.JkMain start
信息: Jk running ID=0 time=0/28  config=null
十一月 19, 2016 10:44:31 下午 org.apache.catalina.startup.Catalina start
信息: Server startup in 710 ms

```

这时候发现配置ok，使用了非阻塞式io

注意：不同版本的tomcat可能不同  		

tomcat7也是使用了bio的代理 		

```xml

信息: Deployment of web application directory /Users/mac/software/tomcat/apache-tomcat-7.0.73/webapps/ROOT has finished in 42 ms
十一月 19, 2016 10:45:50 下午 org.apache.coyote.AbstractProtocol start
信息: Starting ProtocolHandler ["http-bio-8080"]
十一月 19, 2016 10:45:50 下午 org.apache.coyote.AbstractProtocol start
信息: Starting ProtocolHandler ["ajp-bio-8009"]
十一月 19, 2016 10:45:50 下午 org.apache.catalina.startup.Catalina start
信息: Server startup in 874 ms

```

因此tomcat7也可以跟上面的tomcat6一样进行配置优化


tomcat8就不同了  	

```xml

19-Nov-2016 22:50:03.085 信息 [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler [http-nio-8080]
19-Nov-2016 22:50:03.094 信息 [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler [ajp-nio-8009]
19-Nov-2016 22:50:03.095 信息 [main] org.apache.catalina.startup.Catalina.start Server startup in 861 ms

```

默认就使用了nio的代理  		

查看官方文档发现，tomcat8中只有下面三中，而且默认就是第一种  	

```xml

org.apache.coyote.http11.Http11NioProtocol - non blocking Java NIO connector
org.apache.coyote.http11.Http11Nio2Protocol - non blocking Java NIO2 connector
org.apache.coyote.http11.Http11AprProtocol - the APR/native connector. 

```


2.修改线程池,默认是不用配置的，tomcat中有自己的内部设置，也可以通过server.xml手动指定 	 	   

首先通过配置tomcat的管理员界面，查看默认值  	

tomcat6,直接配置tomcat-users.xml文件 		

```xml
<role rolename="tomcat"/>
<role rolename="role1"/>
<role rolename="manager-gui"/>

<user username="tomcat" password="tomcat" roles="tomcat,role1,manager-gui"/>

```

![登录](../assets/2016/11/2016-11-19_22-57-54.png)

输入tomat/tomcat进入管理页面看到,tomcat的默认线程数量，可以从连接看到相关默认配置，[http://tomcat.apache.org/tomcat-8.0-doc/config/executor.html](http://tomcat.apache.org/tomcat-8.0-doc/config/executor.html)		 		


![线程池](../assets/2016/11/2016-11-19_22-59-12.png)


修改server.xml文件  	

放开注释 

```xml

<Executor name="tomcatThreadPool" namePrefix="catalina-exec-" 
        maxThreads="150" minSpareThreads="4"/>

```

并修改  

```xml

<Connector port="8080" executor="tomcatThreadPool" protocol="org.apache.coyote.http11.Http11NioProtocol" 
               connectionTimeout="20000" 
               redirectPort="8443" />

```

重启tomcat  


但是不知道为何，web页面上 没有显示修改后的线程数量，该问题待以后研究  		

![失败截图](../assets/2016/11/2016-11-20_08-33-43.png)

3.连接器优化  		

enableLookups   是否查询远程访问ip的dns，默认为false		
maxParameterCount	最大参数数量，get或者post的键值对个数，默认最大10000，多余的会被忽略.手动改为小于0则不限制 	
maxPostSize		post请求时候FORM表单的最大字节数，默认2M，配置为小于0，则不限制 	
protocol    	上面说过，默认是BIO		
redirectport  	安全转发端口 		
URIEncoding		解决乱码问题，默认是ISO-8859-1 				
acceptCount		最大队列数量,默认100，超过之后直接拒绝 		
compression 	开启压缩，GZIP，off/on/force来设置,默认是off，一般不用，我们直接在nginx上进行配置 	
connectionUploadTimeout 	指定上传超时时间，disableUploadTimeout设置为false时候有用
disableUploadTimeout 	是否禁用上传超时		
executor 		指定线程池	
maxConnections 		最大连接数 	
maxThreads 		tomcat能够创建的最大线程数，默认200 	
minSpareThreads 	保证最小空余线程数量 		

4.禁用ajp,因为一般都用nginx服务器直接返回静态文件 		

直接将server.xml中的下面配置注释掉即可  	

```xml

 <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />


```

重启后日志中没有ajp相关	

```xml

信息: Deployment of web application directory /Users/mac/software/tomcat/apache-tomcat-7.0.73/webapps/ROOT has finished in 39 ms
十一月 20, 2016 1:16:30 下午 org.apache.coyote.AbstractProtocol start
信息: Starting ProtocolHandler ["http-bio-8080"]
十一月 20, 2016 1:16:30 下午 org.apache.catalina.startup.Catalina start
信息: Server startup in 776 ms

```

![ajp](../assets/2016/11/2016-11-20_13-20-24.png)


5.java相关内存优化  		






