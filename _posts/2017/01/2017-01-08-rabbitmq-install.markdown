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


#### rabbitmq中的helloworld(一个生产者一个消费者，一条消息只能发送给一个消费者)  

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


scala   

Send.scala  

```scala

package codetosurvive1.helloworld

import com.rabbitmq.client.ConnectionFactory;

object Send {

  private val QUEUE_NAME="hello";

  def main(argv:Array[String]){

    val factory = new ConnectionFactory();
    factory.setHost("localhost");
    val connection = factory.newConnection();
    val channel = connection.createChannel();
    channel.queueDeclare(QUEUE_NAME,false,false,false,null);

    val message = "hello world!";
    channel.basicPublish("",QUEUE_NAME,null,message.getBytes("UTF-8"));
    println("sent message :"+message);

    channel.close();
    connection.close();
  }
}

```

Receive.scala     

```scala

package codetosurvive1.helloworld;

import com.rabbitmq.client.AMQP.BasicProperties
import com.rabbitmq.client.{ConnectionFactory, DefaultConsumer, Envelope};

/**
  * Created by mac on 2017/1/8.
  */
object Receive {

  private val QUEUE_NAME="hello";

  def main(args:Array[String]){

    val factory = new ConnectionFactory();
    factory.setHost("localhost");

    val connection = factory.newConnection();
    val channel = connection.createChannel();

    channel.queueDeclare(QUEUE_NAME,false,false,false,null);

    println("waiting for messages.");

    val consumer = new DefaultConsumer(channel){
      override def handleDelivery(consumerTag: String,
                                  envelope: Envelope,
                                  properties: BasicProperties,
                                  body: Array[Byte]): Unit = {
        var message = new String(body,"UTF-8");
        println("received message :"+message);
      }
    }

    channel.basicConsume(QUEUE_NAME,true,consumer);
  }

}


```

------

#### rabbitmq中的work queues工作队列（一个生产者多个消费者，一条消息只能发送给一个消费者）       

![](../assets/2017/01/2017-01-08-python-two.png)

HelloWorld例子中我们编写程序来往一个已经命名的队列中发送和接受消息。下面我们将要创建一个工作队列用来分发耗时的任务到不同的工人中。工作队列或者任务队列的主要思想是为了避免立刻去做一些资源密集性的任务并不必等待他完成。相反我们调度任务来后面去完成。一个后台运行的工作进程会弹出任务斌给最终执行任务。当你运行多个工作进程，任务将在他们之间共享。 

这一部分在web应用中尤其有用，因为在一个短的http请求中不可能处理非常复杂的任务。   


准备：   

在helloworld例子中，我们发送了一个包含“hello world”内容的消息。现在我们将发送代表复杂任务的字符串。我们不需要真是的工作任务，比如修改图片尺寸或者需要渲染的pdf文件，我们就仅仅通过Thread.sleep()方法来假设人任务的复杂程度。我们将.的个数作为任务的复杂程度。每一个点代表一秒的工作，比如假设任务描述为Hello...将会消耗三秒钟。  

我们仅需要简单的修改我们之前的Send.java文件来允许任意的消息被发送。这个程序会调度任务到我们的工作队列中，我们命名为NewTask.java      


```java

package io.github.codetosurvive1.workqueues;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.MessageProperties;

public class NewTask {

    private static final String TASK_QUEUE_NAME = "task_queue";

    public static void main(String[] argv) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        channel.queueDeclare(TASK_QUEUE_NAME, true, false, false, null);

        String message = getMessage(argv);

        channel.basicPublish("", TASK_QUEUE_NAME,
                MessageProperties.PERSISTENT_TEXT_PLAIN,
                message.getBytes("UTF-8"));
        System.out.println(" [x] Sent '" + message + "'");

        channel.close();
        connection.close();
    }

    private static String getMessage(String[] strings) {
        if (strings.length < 1)
            return "Hello World!";
        return joinStrings(strings, " ");
    }

    private static String joinStrings(String[] strings, String delimiter) {
        int length = strings.length;
        if (length == 0) return "";
        StringBuilder words = new StringBuilder(strings[0]);
        for (int i = 1; i < length; i++) {
            words.append(delimiter).append(strings[i]);
        }
        return words.toString();
    }
}

```

NewTask所做的工作便是根据命令行传递的参数来往task_queue队列中发送消息，并且队列是可以序列化到硬盘的，而worker所做的工作便是接受队列发送来的消息，并根据消息的.的个数来执行时间复杂度不同的处理操作。


分发策略：轮询分发策略（默认）和公平策略  

上面的任务队列或者工作队列的一个优点就是可以并行的工作。一旦工作有积压，我们仅仅通过增加工人就可以很好的进行拓展。   

默认的情况下，rabbitmq将会按顺序分发消息给下一个消费者，平均而言，每个消费者会得到相同数量的消息，这种分发消息的策略成为轮询分发策略。   


消息应答    

执行一个任务可能花费几秒。如果一个消费者开始了一个很长的任务并且只执行了部分任务就死掉了，你可能会疑问到底发生了什么。用现在的代码来说，每次rabbitmq讲消息传输给消费者后，就立即将消息从内存中删除。这样的话，如果你杀掉了一个worker，你将会丢失掉他正在处理的消息。我们也会丢失掉所有的我们分发到这个特殊worker上但是没有处理的消息。   

但是我们不想丢失掉任何的任务。如果一个worker死掉了，我们期望的是这个任务会被分发给另外一个worker。   

为了确保一条消息永远不会被丢失，rabbitmq支持消息应答。当消息被消费者接受，处理之后，消费者会给rabbitmq发送应答，rabbitmq在接受到应答之后才会删除掉这条消息。    

如果一个消费者死掉了，包括channel通道关闭了，链接关闭了或者tcp链接丢失了，这样就没有应答，rabbitmq会理解为这个消息没有被处理完毕，他将会重新将消息仍回到队列中。如果这时候有其他的消费者在线，那么他将会分发消息到其他的消费者中。这样的话，你就能确保没有消息会丢失，及时worker工作者偶然会死亡掉。  

这里没有任何的消息超时，如果消费者死掉了，rabbitmq会重新传输消息。尽管处理一个消息耗费了非常非常长久的时间，那么也是ok的。  

消息应答默认的情况下是开着的。可以通过autoAck=true标志来设置将其关闭。 



消息耐久性   

上面讲解了如何确保一个消费者在死掉以后，任务不会丢失。但是我们的任务在rabbitmq服务停止掉后仍然会丢失。 

当rabbitmq退出或者挂掉之后，他会遗忘掉队列和队列中的消息，除非你告诉他不要忘记。两件事情能够确保消息不会丢失：我们需要确队列和消息都是持久的。 

首先我们需要确保rabbitmq永远不会丢失掉我们的队列，为了达到这个目的，我们需要声明队列为durable：   

```java

boolean durable = true;
channel.queueDeclare("hello", durable, false, false, null);

```

尽管这两条语句是对的，但是当前情况下是不生效的。因为我们之前已经声明hello队列是不持久化的。rabbitmq是不允许你用不同的参数重新定义一个已经存在的队列的，并且他会返回一个错误信息给任何想尝试这么做的程序。但是这里有一个快速生效的方法，就是声明一个不同的名字，比如task_queue    

```java

boolean durable = true;
channel.queueDeclare("task_queue", durable, false, false, null);

```

这个task_queue需要在producer和consumer的代码中都需要修改   

这时候，我们确保task_queue队列在rabbitmq重启后不会丢失。现在我们需要将我们的消息进行持久化，通过设置MessageProperties(他实现了BasicProperties)为PERSISTENT_TEXT_PLAIN。    

```java

import com.rabbitmq.client.MessageProperties;

channel.basicPublish("", "task_queue",
            MessageProperties.PERSISTENT_TEXT_PLAIN,
            message.getBytes());

```

> 注意消息持久化   

将消息标记为持久化并不能完全保证消息不会丢失。尽管告诉rabbitmq将消息保存到磁盘中，但是这里仍然会有一个短时间的错失窗口-当rabbitmq已经接收到消息但是还没有对他进行保存。而且rabbitmq不会为每条消息都做fsync(2)-他可能仅仅保存到缓存中并不是真正的写到磁盘中。持久化保证还不强健，但是对于我们简单的任务队列已经足够了。如果你想要一个更强健的保证，你可以使用 publisher confirms  


公平调度    

![](../assets/2017/01/2017-01-08-prefetch-count.png)

你可能已经注意到，分发消息并没有像我们期望的那样工作，比如，有两个worker工人，当所有的奇怪的信息很重量级，一些即时消息会很轻量级，一个worker将会一直忙碌，而另外一个将会基本不工作。好吧，rabbitmq并不知道这个事情，因此就会一直这样分配任务消息。 

这样的事情会发生的原因在于rabbitmq在一个消息进入到队列中之后仅仅调度消息。他并没有查看每个消费者没有做出应答的消息的数字，他仅仅盲目的每n个消息轮回给每n个消费者。    

为了检测到这个，我们可以使用basicQos方法来设置为1.这会告诉rabbitmq每次不要给消费者超过一个的消息。换句话说，在消费者处理并应答上条消息之前不要为其分发新的任务消息。这样，他就会分配给下一个不在忙碌的worker。   

```java

int prefetchCount = 1;
channel.basicQos(prefetchCount);

```

注意queue size  

如果所有的workers都在忙碌中，你的队列可能会增长。如果你想注意下他，你可能增加更多的workers或者你使用其他的策略。 


------


#### rabbitmq中的publish/subscribe（一条消息可以发送给多个消费者）  


> 上面我们讲解了如何创建一个工作队列或者说是任务队列，一个生产者，一个队列，有多个消费者来消费。这里存在一个假设条件，就是每个任务仅仅被传送给一个消费者。接下来我们将会做一些彻底不同的东西-我们将阐述消息给多个消费者。这个模式成为主题的发布/订阅模式。     

为了阐述这个模式，我们将会构造一个简单的日志系统。他包含两个程序-第一个是提交日志消息，第二个是接受消息并打印他们。  

在我们的日志系统中，每个运行的receiver程序都会接受到消息。这样的话，我们就可以启动一个receiver来接受消息，然后序列化到磁盘；同事我们可以继续运行一个receiver，然后将日志显示到屏幕当中。    


重要的是，发布的日志消息会广播给所有的receivers    


##### Exchanges     

在之前的章节中，我们从一个队列中发送和接受消息。现在，是时候介绍rabbit中的全部的消息模型了。       

让我们快速的复习之前的章节中提到的内容：    

一个producer是一个发送消息的应用程序。 
一个队列是一个存储消息的缓存。 
一个消费者是一个接受消息的应用程序。  

在rabitmq中的消息模型的核心思想是：一个producer生产者绝不直接给队列发送消息。实际上，生产者也确实经常不知道一个消息是否被传输到一个队列。    

相反，一个生产者只能将消息传递给交换区。一个交换区是一个非常简单的东西。一边，他从生产者那里接受消息，另一边他将消息推送到队列。exchange交换区必须准确的知道当接受到一个消息后该如何做。他应该继续追加到某个特定的队列？还是应该追加到很多队列中？或者是应该丢弃？这里的规则是通过exchange 类型定义的。    

![](../assets/2017/01/2017-01-09-exchanges.png)

这里有几个可用的exchange类型：direct，topic，headers和fanout。我们这里会关注最后一个，fanout。让我们创建一个这种类型的exchange，并称作logs。 

```java

channel.exchangeDeclare("logs","fanout");

```

fanout类型的exchange非常简单，你可能仅通过名字就能猜测出，他仅仅广播他所接受到的消息到所有的他知道的队列中。而且这正好是我们的日志所需要的功能。 


列出交换区   
为了列出服务器中的交换区，你可以运行rabbitmqctl：  

```bash

docker exec -it 8c015f9b6beb /bin/bash
root@8c015f9b6beb:/# rabbitmqctl list_exchanges     
Listing exchanges ...
amq.topic   topic
amq.headers headers
amq.direct  direct
amq.rabbitmq.trace  topic
amq.rabbitmq.log    topic
amq.match   headers
    direct
amq.fanout  fanout

```

在上面的列表中，这里有amq.*的交换区和默认的（没命名）的交换区。他们是默认创建的，但是并不是说你可以使用他们。   


无名交换区   

在之前的章节中我们并不知道任何关于交换区的概念，但是我们仍然能够给队列发送消息。这可能是因为我们使用了默认的交换区，也就是我们用“”标示的交换区。   

回忆一下我们是怎样发送消息的： 

```java

channel.basicPublish("", "hello", null, message.getBytes());

```

第一个参数就是exchange的名字。空字符串标示默认的或者无名的exchange：消息被路由到routingKey所指定的队列中。  

现在我们可以发布我们制定的交换区中   

```java

channel.basicPublish( "logs", "", null, message.getBytes());


```

临时队列    

你可能记得之前我们用队列hello和task_queue两个队列了，能够指定一个队列对我们来说是很重要的，我们需要为消费者指定相同的队列。当你需要在生产者和消费者之间共享队列的时候，给队列命名就很重要了。  

但是这并不是我们的日志例子的情况。我们想监听到所有的日志消息，你仅仅是一个子集。我们也只对当前的流动的信息感兴趣，对以前的老的没兴趣。为了解决这个问题，我们需要两件事情。   

首先，无论我们什么时候链接rabbit，我们需要一个新鲜的的，空的队列。为了达到这个目的，我们可能用一个随机的名字来创建一个队列，更好的情况下是让server为我们选择一个随机的队列。 

其次，一旦我们不再链接消费者之后，队列应该立刻被删除。 

在java的client中，我们使用无参数的queueDeclare()，我们创建一个不持久化的，单独的，自动删除的队列。


```java

String queueName = channel.queueDeclare().getQueue();


```

这样返回的队列名称是一个随机的名称，可能名字像这样：amq.gen-JzTY20BRgKO-HjmUJj0wLg    

Bindings    

![](../assets/2017/01/2017-01-09-bindings.png)

我们已经创建了一个fanout的交换区和一个队列，现在我们需要告诉交换区发送消息到我们的队列。在交换区和队列之间的关系叫做绑定 

```java

channel.queueBind(queueName, "logs", "");


```

这样之后，logs交换区会将消息发送到我们的队列    

列出bindings  

你可以通过下面的指定列出存在的bingdings  

```bash

rabbitmqctl list_bindings
Listing bindings ...
    exchange    task_queue  queue   task_queue  []
root@8c015f9b6beb:/# 


```

把他们合并在一起之后      

![](../assets/2017/01/2017-01-09-python-three-overall.png)

产生日志消息的生产者程序，跟之前的看上去改动不大。最终要的改动是我们现在想发送消息到我们的日志交换区中而不是没有名字的默认的交换区。我们需要发送消息时候我们需要指定一个routingKey队列名称，但是对于fanout交换区而言，这会被忽略掉。下面是EmitLog.java的内容  

```java

package io.github.codetosurvive1.pubandsub;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.Channel;
/**
 * Created by mac on 2017/1/9.
 */
public class EmitLog {

    private static final String EXCHANGE_NAME = "logs";

    public static void main(String[] argv) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");

        String message = getMessage(argv);

        channel.basicPublish(EXCHANGE_NAME, "", null, message.getBytes("UTF-8"));
        System.out.println(" [x] Sent '" + message + "'");

        channel.close();
        connection.close();
    }

    private static String getMessage(String[] strings){
        if (strings.length < 1)
            return "info: Hello World!";
        return joinStrings(strings, " ");
    }

    private static String joinStrings(String[] strings, String delimiter) {
        int length = strings.length;
        if (length == 0) return "";
        StringBuilder words = new StringBuilder(strings[0]);
        for (int i = 1; i < length; i++) {
            words.append(delimiter).append(strings[i]);
        }
        return words.toString();
    }
}



```

如你所见，当建立连接之后，我们声明了交换区。这一步是必须的，因为发布到不存在的交换区是不允许的。        

如果没有队列被绑定到exchange上，消息就会丢失，但是对于我们而言还算ok，如果没有消费者在监听，我们完全可以丢弃消息。  

ReceiveLogs.java代码：    

```java

package io.github.codetosurvive1.pubandsub;
import com.rabbitmq.client.*;
import java.io.IOException;

/**
 * Created by mac on 2017/1/9.
 */
public class ReceiveLogs {
    private static final String EXCHANGE_NAME = "logs";

    public static void main(String[] argv) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
        String queueName = channel.queueDeclare().getQueue();
        channel.queueBind(queueName, EXCHANGE_NAME, "");

        System.out.println(" [*] Waiting for messages. To exit press CTRL+C");

        Consumer consumer = new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope,
                                       AMQP.BasicProperties properties, byte[] body) throws IOException {
                String message = new String(body, "UTF-8");
                System.out.println(" [x] Received '" + message + "'");
            }
        };
        channel.basicConsume(queueName, true, consumer);
    }
}


```


