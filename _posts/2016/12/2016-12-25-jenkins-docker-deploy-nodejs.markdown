---
layout: "post"
title: "docker快速发布nodejs应用"
date: "2016-12-25 13:52"
category: "other"
tags: [大数据,虚拟机,docker]

---


1.安装jenkins  			

```bash

docker pull jenkins
Using default tag: latest
latest: Pulling from library/jenkins

75a822cd7888: Already exists
57de64c72267: Already exists
4306be1e8943: Already exists
1e6944bfb718: Pull complete
3521f2f45ed2: Pull complete
9c2f0d9b5f90: Pull complete
8cedcf6d2527: Pull complete
31f99da7583d: Pull complete
2b1a4e887f7e: Pull complete
ec60c180ba47: Pull complete
b8c5058508d7: Pull complete
5f6956ea8053: Pull complete
11322c12182f: Pull complete
012931252722: Pull complete
1414621e5a2e: Pull complete
bcff974135a1: Pull complete
27a8f00c6f2a: Pull complete
3b2fdd5946f5: Pull complete
e5af99d78c8f: Pull complete
Digest: sha256:e635156257ec0e64f03607a89aac7383f192f0aebc7f70cba1e35461b76c1fad
Status: Downloaded newer image for jenkins:latest
mac:~ mac$

```


2.创建本地目录，映射到容器中来保存jenkins的数据  		

```bash

mkdir -p ~/software/docker/jenkins/jenkins_home

docker run -d --name jenkins -p 49001:8080 -v /Users/mac/software/docker/jenkins/jenkins_home:/var/jenkins_home jenkins
2553a256e0d4817ee4328718aeeaa0370fedd64cfbd8d7205120217f730be316


```

3.稍等一会后打开本地的http://localhost:49001，会看到下面的页面  	

![](../assets/2016/12/2016-12-25_21-22-31.png)

![](../assets/2016/12/2016-12-25_21-32-04.png)

4.进入到docker容器中查看文件中的密码 		

```bash

docker exec -it jenkins /bin/bash

jenkins@2553a256e0d4:/$ cat /var/jenkins_home/secrets/initialAdminPassword
da702c468f9642b3a85ac25220f6165e

```

5.下一步安装插件  	


![](../assets/2016/12/2016-12-25_21-37-09.png)

经过漫长的等待下载后 		

![](../assets/2016/12/2016-12-26_08-37-12.png)

输入用户名及密码后并设置为管理员		

![](../assets/2016/12/2016-12-26_08-38-12.png)

6.进入到系统管理-管理插件-已安装，确保下面两款插件已经安装，如果没有安装，则进行安装并重启jenkins 		

![](../assets/2016/12/2016-12-26_08-47-19.png)

![](../assets/2016/12/2016-12-26_08-48-08.png)

7.进入系统管理-系统设置来对刚刚安装的插件进行相关配置  	


![](../assets/2016/12/2016-12-26_08-50-43.png)

这里设置自己的mac电脑远程ssh 		

![](../assets/2016/12/2016-12-26_19-33-44.png)

![](../assets/2016/12/2016-12-26_19-34-55.png)

test测试成功	


8.点击新建->输入项目名称->选择自由软件风格  	

![](../assets/2016/12/2016-12-26_19-37-39.png)

![](../assets/2016/12/2016-12-26_19-42-26.png)

![](../assets/2016/12/2016-12-26_19-43-19.png)

![](../assets/2016/12/2016-12-26_19-44-33.png)

![](../assets/2016/12/2016-12-26_19-46-07.png)

------

这样，到这一步之后其实就可以保存进行构建测试了，只是需要模拟真实的发布版本的情况，一般我们的手工操作如下：		

a.从svn或者git上下载源代码 		
b.将本地代码打包，并排除不需要的文件，比如.git文件  		
c.将打包好的程序包上传到远程服务器中  	
d.停止远程服务器的服务，删除服务器上的代码，解压新的代码 	
e.通过新的package来安装依赖，然后重启服务  	

针对上面的步骤，我们需要做的指令如下  	

rm -rf /var/jenkins_home/workspace/docker_node.tar.gz
tar -zcvf /var/jenkins_home/workspace/docker_node/docker_node.tar.gz -C /var/jenkins_home/workspace/docker_node . --exclude="*.git"

9.选择构建->execute shell		

![](../assets/2016/12/2016-12-26_19-48-05.png)

![](../assets/2016/12/2016-12-26_19-51-05.png)

将上面编写的删除打包好的压缩包及重新打包的代码粘贴到上面的地方  	

![](../assets/2016/12/2016-12-27_22-25-54.png)

这样在每次构建的时候，都会在github下载新代码之后，重新压缩打包  	

10.继续选择添加构建步骤->send files or execute commands over ssh 	

![](../assets/2016/12/2016-12-27_21-10-59.png)

![](../assets/2016/12/2016-12-27_22-22-09.png)

在execute  command 中粘贴如下代码 	

```bash

rm -rf /Users/mac/software/docker/node/docker_node/README.md

rm -rf /Users/mac/software/docker/node/docker_node/package.json

rm -rf /Users/mac/software/docker/node/docker_node/app.js

tar -zxvf /Users/mac/software/docker/node/docker_node/docker_node.tar.gz -C /Users/mac/software/docker/node/docker_node


docker run --rm -v /Users/mac/software/docker/node/docker_node/:/var/node/docker_node -w /var/node/docker_node/ nodecnpm cnpm install

docker run -d --name 'dockercount' -p 8000:8000 -v /Users/mac/software/docker/node/docker_node:/var/node/docker_node --link redis-server:redis -w /var/node/docker_node cnpm node app.js

rm -rf /Users/mac/software/docker/node/docker_node/docker_node.tar.gz

```

