---
layout: "post"
title: "ubuntu安装docker"
date: "2016-12-25 12:52"
category: "other"
tags: [大数据,虚拟机,docker]

---


#### ubuntu安装docker

1.更新系统源  	

```xml

sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates

```

2.添加GPG key 		

```

sudo apt-key adv \
               --keyserver hkp://ha.pool.sks-keyservers.net:80 \
               --recv-keys 58118E89F3A912897C070ADBF76221572C52609D

```

执行结果为 	

```

Executing: /tmp/tmp.Jek7P0R86h/gpg.1.sh --keyserver
hkp://ha.pool.sks-keyservers.net:80
--recv-keys
58118E89F3A912897C070ADBF76221572C52609D
gpg: requesting key 2C52609D from hkp server ha.pool.sks-keyservers.net
gpg: key 2C52609D: public key "Docker Release Tool (releasedocker) <docker@docker.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)

```

3.根据系统版本找到下面对应的链接,这里使用的是Ubuntu 16.04版本，也就是最后一个     		 	


```

Ubuntu version	Repository
Precise 12.04 (LTS)	deb https://apt.dockerproject.org/repo ubuntu-precise main
Trusty 14.04 (LTS)	deb https://apt.dockerproject.org/repo ubuntu-trusty main
Wily 15.10	deb https://apt.dockerproject.org/repo ubuntu-wily main
Xenial 16.04 (LTS)	deb https://apt.dockerproject.org/repo ubuntu-xenial main

```

4.将下面命令的repo替换为3中的链接 		

```
echo "<REPO>" | sudo tee /etc/apt/sources.list.d/docker.list
```

修改为  	

```xml
echo "deb https://apt.dockerproject.org/repo ubuntu-xenial main" | sudo tee /etc/apt/sources.list.d/docker.list


```

5.更新源并进行安装  		

```

sudo apt-get update


```


如果这一步骤很慢，则可以修改第4步骤中的文件内容为阿里镜像的地址  	

```

deb http://mirrors.aliyun.com/docker-engine/apt/repo/  ubuntu-xenial main

```

6.执行下面命令，查看可用版本  	

```xml

apt-cache policy docker-engine

```

7.安装docker  	

```xml

sudo apt-get install docker-engine

```

8.启动docker服务  	

```

sudo service docker start

```

9.查看运行状态  	

```

sudo service docker status
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: e
   Active: active (running) since Sat 2016-12-24 19:02:21 PST; 42s ago
     Docs: https://docs.docker.com
 Main PID: 10630 (dockerd)
    Tasks: 17
   Memory: 34.5M
      CPU: 274ms
   CGroup: /system.slice/docker.service
           ├─10630 /usr/bin/dockerd -H fd://
           └─10645 docker-containerd -l unix:///var/run/docker/libcontainerd/doc

Dec 24 19:02:21 ubuntu dockerd[10630]: time="2016-12-24T19:02:21.578076079-08:00
Dec 24 19:02:21 ubuntu dockerd[10630]: time="2016-12-24T19:02:21.578862967-08:00
Dec 24 19:02:21 ubuntu dockerd[10630]: time="2016-12-24T19:02:21.632339397-08:00
Dec 24 19:02:21 ubuntu dockerd[10630]: time="2016-12-24T19:02:21.722353068-08:00
Dec 24 19:02:21 ubuntu dockerd[10630]: time="2016-12-24T19:02:21.865416140-08:00
Dec 24 19:02:21 ubuntu dockerd[10630]: time="2016-12-24T19:02:21.865672519-08:00
Dec 24 19:02:21 ubuntu dockerd[10630]: time="2016-12-24T19:02:21.865818794-08:00
Dec 24 19:02:21 ubuntu systemd[1]: Started Docker Application Container Engine.
Dec 24 19:02:21 ubuntu dockerd[10630]: time="2016-12-24T19:02:21.988106862-08:00
Dec 24 19:02:56 ubuntu systemd[1]: Started Docker Application Container Engine.


```

10.运行hello-world例子 	

```

sudo docker run hello-world

```

这一步的操作首先会去docker官方下载hello-world镜像，然后运行  	

如果这一步也下载缓慢，则执行下面命令，使用daocloud加速器,然后重启docker		

```xml

curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://6f89f03a.m.daocloud.io

sudo systemctl restart docker.service

```

11.验证是否成功 		

```

ubuntu@ubuntu:/etc/apt$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              c54a2cc56cbb        5 months ago        1.848 kB


ubuntu@ubuntu:/etc/apt$ sudo docker ps -al
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
c1e66fe1d26a        hello-world         "/hello"            2 minutes ago       Exited (0) 2 minutes ago                       pensive_shirley



```


#### 参考文档  		


[https://docs.docker.com/engine/installation/linux/ubuntulinux/](https://docs.docker.com/engine/installation/linux/ubuntulinux/)