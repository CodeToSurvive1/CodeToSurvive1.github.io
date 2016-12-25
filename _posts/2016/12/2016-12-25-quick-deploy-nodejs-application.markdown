---
layout: "post"
title: "docker快速发布nodejs应用"
date: "2016-12-25 13:52"
category: "other"
tags: [大数据,虚拟机,docker]

---

#### 安装redis	

1.docker安装redis 		

```

docker pull redis
Using default tag: latest
latest: Pulling from library/redis
75a822cd7888: Pull complete
e40c2fafe648: Pull complete
ce384d4aea4f: Pull complete
5e29dd684b84: Pull complete
29a3c975c335: Pull complete
a405554540f9: Pull complete
4b2454731fda: Pull complete
Digest: sha256:be03cb3b2e0d290c3d6670d1843b6036c1914663574bb624ffb62535344de5b4
Status: Downloaded newer image for redis:latest

mac:~ mac$ docker  images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              67591570dd29        9 days ago          191.8 MB
redis               latest              d59dc9e6d0bf        10 days ago         182.9 MB
registry            latest              c9bd19d022f6        9 weeks ago         33.3 MB

```

2.启动redis 		

```

docker run --name redis-server -d redis redis-server --appendonly yes
e2c2f5eb97b3ad14a58dda238a8e3c19623e36555777576e57dc1da9bcb05d11

```

解释一下：	

--name：是指定容器的名字 		
-d：是后台运行 		
redis-server --appendonly yes：是运行容器中的redis-server，并打开redis数据序列化功能		

验证是否启动  	

```

docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                    NAMES
e2c2f5eb97b3        redis               "docker-entrypoint.sh"   About a minute ago   Up About a minute   6379/tcp                 redis-server



```

#### 安装nodejs 	

1.安装node镜像 	

```bash

docker pull node
Using default tag: latest
latest: Pulling from library/node
75a822cd7888: Already exists
57de64c72267: Pull complete
4306be1e8943: Pull complete
871436ab7225: Pull complete
0110c26a367a: Pull complete
1f04fe713f1b: Pull complete
d8daeae79eeb: Pull complete
Digest: sha256:356097f9b8059a29476134b58249a123211153faf823a5c3d5370e3518219a89
Status: Downloaded newer image for node:latest


```

2.启动node镜像，并安装cnpm	,然后打包产生新的镜像，命名为nodecnpm		

```bash

docker run -it node /bin/bash
root@f04b5e564560:/#

npm install cnpm -g --registry=https://registry.npm.taobao.org

docker commit f04b5e564560 nodecnpm
sha256:82d2fb5e840f82177446ab6fc9df8f5c361662d95718292023f14714b48fb326

exit

```

3.创建本地目录,并编写nodejs应用  	

```bash

mkdir -p ~/software/docker/node/docker_node	

```

```
touch package.json
mac:docker_node mac$ vi package.json

```

在~/software/docker/node/docker_node目录下创建package.json文件，并如下内容  	


```nodejs

{

    "name": "docker_node",
    "version": "0.0.1",
    "dependencies": {
        "express":"4.14.0",
        "redis":"2.6.3"
    },
    "engines": {
        "node":">=0.10.0"
    }
}

```

新建app.js，并添加下面内容  	

```nodejs

var express = require('express');

var redis = require('redis');

var app = express();

var redisHost = process.env['REDIS_PORT_6379_TCP_ADDR'];

var redisPort = process.env['REDIS_PORT_6379_TCP_PORT'];

var redisClient = redis.createClient(redisPort,redisHost);

app.get('/',function(req,res){

    console.log('get request');

    redisClient.get('access_count',function(err,countNum){
        if(err){
            return res.send('get access count error');
        }

        if(!countNum){
            countNum=1;
        }else{
            countNum = parseInt(countNum)+1;
        }

        redisClient.set('access_count',countNum,function(err){
            if(err){
                return res.send('set access count error');
            }
            res.send(countNum.toString())
        });

    });
});


app.listen(8000);



```


4.运行npm容器，并将上面编写的代码映射到容器中，然后安装一下相关依赖  	

```nodejs

docker run --rm -it -v /Users/mac/software/docker/node/docker_node/:/var/node/docker_node -w /var/node/docker_node/ nodecnpm cnpm install


```

-w：表示命令的当前目录	

5.运行容器中的app.js  		 		

```

docker run -v /Users/mac/software/docker/node/docker_node/:/var/node/docker_node -p 8000:8000 -d  --link redis-server:redis -w /var/node/docker_node/ nodecnpm node app.js
3f8e4b4531a8901ab1ead9e53f364a45957c4bf38d9a326d493c65907d5e7470

```

--link：链接redis容器 	


6.刷新页面http://localhost:8000可以看到数字的变换  		

代码地址 		

[https://github.com/CodeToSurvive1/docker_node](https://github.com/CodeToSurvive1/docker_node)



