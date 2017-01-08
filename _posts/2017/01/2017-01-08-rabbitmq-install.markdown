---
layout: "post"
title: "rabbitmq环境搭建安装及官方例子学习使用"
date: "2017-01-07 08:52"
category: "other"
tags: [rabbitmq,队列,rabbitmq环境搭建，scala,node]

---


### rabbitmq软件安装与启动      	

1.rabbitmq安装    

这里使用docker的方式，方便    

```

docker pull rabbitmq
Using default tag: latest
latest: Pulling from library/rabbitmq
Digest: sha256:53c67c41323977870ad077c8a0e5ef52b2cc460206e6a965ced96ea130de1e12
Status: Image is up to date for rabbitmq:latest

```

也可以参考官方文档来下载安装,[地址](http://www.rabbitmq.com/download.html)    

2.rabbitmq启动  

```

docker run -d --name rabbitmq -p 5672:5672 rabbitmq
413f469182190d05235f75337077125497eefe57abec3f37936897014c881f5a

docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                   PORTS                                                   NAMES
413f46918219        rabbitmq            "docker-entrypoint.sh"   6 seconds ago       Up 5 seconds             4369/tcp, 5671/tcp, 25672/tcp, 0.0.0.0:5672->5672/tcp   rabbitmq

```

------

### 开启web端口访问管理控制页面   

上面的docker启动rabbitmq的时候并没有释放rabbitmq的管理控制台的端口，下面进行重新操作  

为了方便，这里我们直接将原先启动的容器stop掉，然后删掉，重新运行释放端口,rabbitmq的管理控制台端口为15672     

```shell

docker stop 2d5e5be34be7
2d5e5be34be7

docker rm 2d5e5be34be7
2d5e5be34be7

docker run -d --name rabbitmq -p 5672:5672  -p 15672:15672 rabbitmq
8c015f9b6beb4e14e7726a7367c0e9571ae4987dca8bfb64343fd8f3eeaade03

```

启动容器后，执行docker命令，找到container的id       

```

docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                        PORTS                                                                             NAMES
8c015f9b6beb        rabbitmq            "docker-entrypoint.sh"   10 hours ago        Up About a minute             4369/tcp, 0.0.0.0:5672->5672/tcp, 5671/tcp, 25672/tcp, 0.0.0.0:15672->15672/tcp   rabbitmq


```

找到id为8c015f9b6beb 执行下面命令进入docker的命令控制台下 

```shell

docker exec -it 8c015f9b6beb /bin/bash 
root@8c015f9b6beb:/# 

```

然后在docker的控制台下执行下面命令来启动rabbitmq的管理控制台的插件  

```

rabbitmq-plugins enable rabbitmq_management
The following plugins have been enabled:
  mochiweb
  webmachine
  rabbitmq_web_dispatch
  amqp_client
  rabbitmq_management_agent
  rabbitmq_management

Applying plugin configuration to rabbit@8c015f9b6beb... started 6 plugins.

```

刷新页面[http://localhost:15672/#/](http://localhost:15672/#/)会看到rabbitmq的登录页面,通过guest/guest进行登录  

![](../assets/2017/01/2017-01-08_12-52-23.png)  

![](../assets/2017/01/2017-01-08_12-55-21.png)

参考链接:[http://www.rabbitmq.com/management.html](http://www.rabbitmq.com/management.html)   


------

### 编写客户端例子   


#### rabbitmq中的helloworld  

##### 简介

> rabbitmq是一个消息代理。主要的目的很简单：接受和转发消息。你可以把他当做一个邮局：当你往邮箱放入你的信件的时候，你非常确定邮递员最终会将信件放到接收者手中。这个比喻把rabbitmq比作一个邮箱，邮局和邮递员。
  
> 在rabbitmq和邮局之间最主要的区别在于，他不是处理纸质的东西，而是接收，存储和转发二进制的blob数据消息。   
  
rabbitmq或者一般的消息中存在一些专业术语    

1 生产者意味着消息发送者。一个发送消息的程序叫做生产者，我们叫做P，图示如下：   
  
![](../assets/2017/01/2017-01-07-producer.png)  
  
2 队列是一个邮箱的名字。存在于rabbitmq中。尽管消息在rabbitmq和你的应用程序之间进行流通，他们只能存储在队列内部。一个队列不受任何限制，你可以存放任意多的消息，本质上来说他就是一个无穷的缓冲区。许多生产者可以往一个队列中发送消息，许多消费者也可以尝试从一个队列中接受数据。一个队列可以按照下图所示，上面是队列的名字。   
  
![](../assets/2017/01/2017-01-07-queue.png) 
  
3 消费者跟接受的意思类似，一个消费者就是一个等待接受消息的程序。我们可以如下图所画，简称为C    
  
![](../assets/2017/01/2017-01-07-consumer.png)  

注意，生产者，消费者和代理不需要部署在同一台机器上。当然在大多数的应用中他们也确实不在同一台机器上。  

##### 使用java客户端 

> 这部分我们将用java语言编写两个应用程序：一个发送单个消息的生产者和一个接受消息并打印他们的消费者。我们会忽视java的api的细节，专注于仅仅将例子跑通开始。一个hello world的消息例子。在下图中，P使我们的生产者，C是我们的消费者，中间的箱子是队列-一个rabbitmq为消费者存放消息的缓存。       

![](../assets/2017/01/2017-01-07-python-one.png)  


配置    

这里使用rabbitmq的官方客户端    

在pom.xml文件中添加下面依赖   

```

<dependency>
  <groupId>com.rabbitmq</groupId>
  <artifactId>amqp-client</artifactId>
  <version>4.0.1</version>
</dependency>

```

最新的版本可以通过[连接](http://www.rabbitmq.com/java-client.html)查看     


Sending 

![](../assets/2017/01/2017-01-07-sending.png)

我们称我们的消息发送者为send，我们的消息接收者为Recv。发送者会连接rabbitmq，发送消息，然后停止。  

在Send.java中我们需要导入下面包    

```java

import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.Channel;

```

设置类并命名队列  

```java


public class Send {
  private final static String QUEUE_NAME = "hello";

  public static void main(String[] argv)
      throws java.io.IOException {
  }
}


```

然后我们可以创建一个连接服务端的链接    

```java

ConnectionFactory factory = new ConnectionFactory();
factory.setHost("localhost");
Connection connection = factory.newConnection();
Channel channel = connection.createChannel();

```

链接是对socket链接的抽象，并且负责版本协议的协商和认证。这里我们链接了本地服务器的代理，也就是localhost。如果我们想链接一个不同机器的代理，我们仅需要修改这里的名称或者ip地址即可。  

接下来，我们创建一个channel通道，我们的大部分操作的api都在这里。   

为了发送，我们必须声明一个我们要发送信息的目标队列，然后我们往队列中发送消息。   

```java

channel.queueDeclare(QUEUE_NAME,false,false,false,null);
String message = "Hello world!";
channel.basicPublish("",QUEUE_NAME,null,message.getBytes("UTF-8"));
System.out.println(" [x] Sent '"+message+"'");

```

声明一个队列是等幂的，如果队列不存在会创建一个，消息内容是一个字节数组，所以你可以想随心所欲的编码你的内容。    

最后我们关闭channel通道和链接。   

```java

channel.close();
connection.close();

```


下面是Send.java的所有内容  

```java

package io.github.codetosurvive1.helloworld;

/**
 * Created by mac on 2017/1/7.
 */

import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.Channel;

public class Send {

    private final static String QUEUE_NAME = "hello";


    public static void main(String args[]) throws Exception{

        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        String message = "Hello world!";
        channel.basicPublish("",QUEUE_NAME,null,message.getBytes("UTF-8"));
        System.out.println(" [x] Sent '"+message+"'");

        channel.close();
        connection.close();

    }

}



```

> 发送消息不生效   

如果你第一次使用rabbitmq并且没有看到你发送的消息，然后你可能会疑惑什么地方做错了。很有可能是代理没有提供足够的剩余空间启动的，默认是1g，因此拒绝了所有的消息。检查代理的日志文件来确认是否是这一点，如果可能可以减少限制。配置文件可以参考[链接](http://www.rabbitmq.com/configure.html#config-items)中的disk_free_limit  

运行Server.java例子成功后，查看webui页面中是否有发送的消息   

发现新建队列hello成功   

![](../assets/2017/01/2017-01-08_12-57-20.png)

点击hello队列进入查看详细信息   

![](../assets/2017/01/2017-01-08_12-59-37.png)

![](../assets/2017/01/2017-01-08_13-00-25.png)

Receiving   

上面是我们的消息发送者。不像发送者仅仅发送一条消息一样，我们的接收者是从rabbitmq中接受到推送过来的消息，所以我们需要不停的监听消息并进行输出。   

![](../assets/2017/01/2017-01-08-receiving.png)

Recv.java几乎跟Send一样    

```java

import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Consumer;
import com.rabbitmq.client.DefaultConsumer;

```

额外的DefaultConsumer是一个实现Consumer消费者接口的类，我们用它来缓存从server推送过来的消息。   

跟sender一样新建类，创建一个连接和一个通道，声明一个我们用来消费的队列，注意，这应该跟我们把消息推送给的相应的队列对应。   

```

public class Recv {

    private static final String QUEUE_NAME="hello";

    public static void main(String args[]) throws Exception{
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();


        channel.queueDeclare(QUEUE_NAME,false,false,false,null);

        System.out.println("waiting for messages.To exit press CTRL+C");
    }

}

```

注意我们这里也声明了队列，因为我们可能在sender之前启动了receiver，在我么尝试消费消息之前应该确保队列已经存在。    

我们然后需要告诉服务器给我们从队列中传输消息。然后他会异步的给我们推送消息，我们以一个对象的形式提供一个回调函数，来给我们缓存消息知道我们使用它们。这就是DefaultConsumer父类做的。   


```java

Consumer consumer = new DefaultConsumer(channel){
    @Override
    public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
        String message = new String(body,"UTF-8");

        System.out.println("received:'"+message+"'");
    }
};

channel.basicConsume(QUEUE_NAME,consumer);

```

下面是所有的Recv.java代码   

```java

package io.github.codetosurvive1.helloworld;

import com.rabbitmq.client.*;

import java.io.IOException;

/**
 * Created by mac on 2017/1/8.
 */
public class Recv {

    private static final String QUEUE_NAME="hello";

    public static void main(String args[]) throws Exception{
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();


        channel.queueDeclare(QUEUE_NAME,false,false,false,null);

        System.out.println("waiting for messages.To exit press CTRL+C");


        Consumer consumer = new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                String message = new String(body,"UTF-8");

                System.out.println("received:'"+message+"'");
            }
        };

        channel.basicConsume(QUEUE_NAME,consumer);

    }

}


```

运行Recv.java后    

```console

waiting for messages.To exit press CTRL+C
received:'Hello world!'

```

通过页面查看，已经没有上次的消息了   

![](../assets/2017/01/2017-01-08_13-24-44.png)

再运行一次server.java发现控制台中又多了一条打印信息 

```console

waiting for messages.To exit press CTRL+C
received:'Hello world!'
received:'Hello world!'

```


##### 使用amqp.node客户端 

配置  

首选安装客户端 

```nodejs

npm install amqplib

```

send.js中需要首先引入库文件  

```nodejs

var amqp = require('amqplib/callback_api');

```

然后连接rabbitmq服务器   

```nodejs

amqp.connect('amqp://localhost', function(err, conn) {});

```

接下来创建一个通道，

```nodejs

amqp.connect('amqp://localhost', function(err, conn) {
    conn.createChannel(function(err, ch) {});
});

```

为了发送消息，我们必须声明我们要发送的目的队列，然后向队列中发送消息    

```nodejs

amqp.connect('amqp://localhost', function(err, conn) {
    conn.createChannel(function(err, ch) {
        var q = "hello";
        ch.assertQueue(q, { durable: false });

        ch.sendToQueue(q, new Buffer('Hello world!'));
        console.log(" Sent 'hello world!");
    });
});

```

最后关闭连接并退出   

```nodejs

setTimeout(function() {
        conn.close();
        process.exit(0);
    }, 500);

```

Sender.js的全部代码如下： 

```nodejs

#!/usr/bin/env node

var amqp = require('amqplib/callback_api');

amqp.connect('amqp://localhost', function(err, conn) {
    conn.createChannel(function(err, ch) {
        var q = "hello";
        ch.assertQueue(q, { durable: false });

        ch.sendToQueue(q, new Buffer('Hello world!'));
        console.log(" Sent 'hello world!");

    });

    setTimeout(function() {
        conn.close();
        process.exit(0);
    }, 500);
});

```


Receiving   

```nodejs

#!/usr/bin/env node

var amqp = require('amqplib/callback_api');

amqp.connect('amqp://localhost', function(err, conn) {
    conn.createChannel(function(err, ch) {
        var q = "hello";
        ch.assertQueue(q, { durable: false });
        console.log("waiting for messages");

        ch.consume(q, function(msg) {
            console.log(" received message %s", msg.content.toString());
        }, { noAck: true });
    })
})

```

```

./send.js

./receive.js

```



