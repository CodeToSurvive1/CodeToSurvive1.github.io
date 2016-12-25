---
layout: "post"
title: "ubuntu安装docker私有服务器"
date: "2016-12-25 13:52"
category: "other"
tags: [大数据,虚拟机,docker]

---


#### 安装registry   


1.安装之前请先安装docker    

[ubuntu下安装docker](https://codetosurvive1.github.io/posts/docker-install-ubuntu.html)  

2.安装并启动registry     

```

docker run -d -p 5000:5000 --restart=always --name registry registry:2


```

3.下载自己的镜像并进行相关修改    

```

sudo docker pull node
Using default tag: latest
latest: Pulling from library/node
75a822cd7888: Pull complete 
57de64c72267: Pull complete 
4306be1e8943: Pull complete 
871436ab7225: Pull complete 
0110c26a367a: Pull complete 
1f04fe713f1b: Pull complete 
d8daeae79eeb: Pull complete 
Digest: sha256:356097f9b8059a29476134b58249a123211153faf823a5c3d5370e3518219a89
Status: Downloaded newer image for node:latest

sudo docker run -it node /bin/bash
root@6a75f7e56cfe:/# 


npm install pm2 -g

npm install cnpm -g --registry=https://registry.npm.taobao.org


root@6a75f7e56cfe:/# exit
exit
```

4.找到刚才的容器   

```

ubuntu@ubuntu:~$ sudo docker ps -al
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
6a75f7e56cfe        node                "/bin/bash"         3 minutes ago       Exited (0) 47 seconds ago                       dreamy_cori

```

5.提交容器    


```

sudo docker commit 6a75f7e56cfe nodepm2
sha256:11031297762fccbb39181f237e5d355c70e1630d064990b027ee5f7cc218a848

检查镜像是否生成  
ubuntu@ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nodepm2             latest              11031297762f        4 seconds ago       713.7 MB
node                latest              d1699fb7d2bf        3 days ago          659.8 MB
registry            2                   c9bd19d022f6        9 weeks ago         33.3 MB
hello-world         latest              c54a2cc56cbb        5 months ago        1.848 kB


```

6.将nodepm2镜像打标签，并提交到registry中   

```

sudo docker tag nodepm2 192.168.4.138:5000/nodepm2
ubuntu@ubuntu:~$ sudo docker images
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
192.168.4.138:5000/nodepm2   latest              b26536a28863        32 seconds ago      713.7 MB
nodepm2                      latest              b26536a28863        32 seconds ago      713.7 MB
node                         latest              d1699fb7d2bf        3 days ago          659.8 MB
registry                     2                   c9bd19d022f6        9 weeks ago         33.3 MB
hello-world                  latest              c54a2cc56cbb        5 months ago        1.848 kB


ubuntu@ubuntu:~$ sudo docker push 192.168.4.138:5000/nodepm2
The push refers to a repository [192.168.4.138:5000/nodepm2]
Get https://192.168.4.138:5000/v1/_ping: http: server gave HTTP response to HTTPS client

```

7.上面push的时候报错，修改配置文件,并重新启动docker      



```

sudo vi /etc/default/docker

添加下面一行    

DOCKER_OPTS="$DOCKER_OPTS --insecure-registry=192.168.4.138:5000"

sudo service docker restart


```

这里使用的Ubuntu系统，但是配置后没起作用，需要再进行下面配置     

```

sudo vi /etc/docker/daemon.json

添加下面内容  


{"registry-mirrors": ["http://6f89f03a.m.daocloud.io"],"insecure-registries":["192.168.4.138:5000"] }


然后重启docker    

sudo service docker stop

sudo service docker start


```


可以在链接[https://github.com/docker/distribution/issues/1874#issuecomment-237194314](https://github.com/docker/distribution/issues/1874#issuecomment-237194314)中找到相关信息     

8.重新执行命令  

```


sudo docker push 192.168.4.138:5000/nodepm2

The push refers to a repository [192.168.4.138:5000/nodepm2]
ac790c708fef: Pushed 
a05ad5eac50b: Pushed 
604c78617f34: Pushed 
fa18e5ffd316: Pushed 
0a5e2b2ddeaa: Pushed 
53c779688d06: Pushed 
60a0858edcd5: Pushed 
b6ca02dfe5e6: Pushed 
latest: digest: sha256:002b64efd563616feb27f9ec41494a2329a3415af04f82536d3172f60cde4815 size: 2007

``` 


9.直接在自己的mac机器上测试下在192.168.4.138:5000搭建的是否成功    

```
docker pull 192.168.4.138:5000/nodepm2
Using default tag: latest
Error response from daemon: Get https://192.168.4.138:5000/v1/_ping: http: server gave HTTP response to HTTPS client

```

看来跟上面的错误是一样的，由于mac的配置跟ubuntu配置不太一样，这里直接截图   

![](../assets/2016/12/2016-12-25_15-39-59.png)

配置完成后重启docker   

```

docker pull 192.168.4.138:5000/nodepm2
Using default tag: latest
latest: Pulling from nodepm2
75a822cd7888: Already exists
57de64c72267: Already exists
4306be1e8943: Already exists
871436ab7225: Already exists
0110c26a367a: Already exists
1f04fe713f1b: Already exists
d8daeae79eeb: Already exists
e74e30bbf941: Pull complete
Digest: sha256:002b64efd563616feb27f9ec41494a2329a3415af04f82536d3172f60cde4815
Status: Downloaded newer image for 192.168.4.138:5000/nodepm2:latest

```



#### 参考文档   

[https://github.com/docker/docker.github.io/blob/master/registry/deploying.md](https://github.com/docker/docker.github.io/blob/master/registry/deploying.md)    

[https://github.com/docker/distribution/issues/1874#issuecomment-237194314](https://github.com/docker/distribution/issues/1874#issuecomment-237194314)    