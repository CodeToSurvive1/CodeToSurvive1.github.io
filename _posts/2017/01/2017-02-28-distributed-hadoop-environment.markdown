---
layout: "post"
title: "hadoop分布式环境搭建一(集群准备)"
date: "2017-02-28 08:52"
category: "hadoop"
tags: [hadoop,分布式]

---

#### 安装准备		

首先按照教程安装vmware虚拟机	

[链接](https://codetosurvive1.github.io/posts/centos.html)		

#### 分布式环境搭建  	

1.复制安装好的centos虚拟机两份，并分别命名为centos2，centos3，这里搭建有三台主机的集群  	
![复制虚拟机](../assets/2017/02/2017-02-28_08-19-48.png)	
安装后启动任意一台虚拟机如下所以，这里的用户名为mac是应为自己的mac电脑的用户名就是mac，这里为了方便自己学习方便ssh链接三台虚拟机而不用带用户名即可而使用mac用户名 	

![登录界面](../assets/2017/02/2017-02-28_08-26-32.png)

2.添加自己用户的sudo权限  	
进入root用户

```

su

```
修改/etc/sudoers文件	

```

vi /etc/sudoers

```

添加如下一行内容 	 	


```
mac     ALL=(ALL)       ALL
```

修改内容如下  	


```
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
mac     ALL=(ALL)       ALL
```

当然，同样需要分别修改另外两台

3.开启centos虚拟机的ssh服务  	

首先查询电脑是否安装了ssh服务，默认情况下centos都有安装  	

```

rpm -qa | grep ssh

openssh-5.3p1-84.1.el6.x86_64
openssh-server-5.3p1-84.1.el6.x86_64
libssh2-1.4.2-1.el6.x86_64
ksshaskpass-0.5.1-4.1.el6.x86_64
openssh-askpass-5.3p1-84.1.el6.x86_64
openssh-clients-5.3p1-84.1.el6.x86_64

```

 		
验证服务是否开启  	

```
sudo service sshd status
openssh-daemon is stopped
```

开启服务

```

sudo service sshd start
Starting sshd:                                             [  OK  ]

```

设置sshd开机自启动  

```

sudo chkconfig --list | grep sshd
sshd           	0:关闭	1:关闭	2:关闭	3:关闭	4:关闭	5:关闭	6:关闭

```

检查下是否开机自启动，发现没有设置 

```
sudo chkconfig sshd on

```

再次检查		

```
sudo chkconfig --list | grep sshd
sshd           	0:关闭	1:关闭	2:启用	3:启用	4:启用	5:启用	6:关闭

```

也可以重启电脑测试是否启动  

同样设置其他两台电脑  	



4.直接在mac电脑上进行远程连接即可了  

ssh centos1 centos2 centos 的ip地址即可  

```
ssh1 192.168.4.131
```

```
ssh1 192.168.4.132
```

```
ssh1 192.168.4.133
```

5.分别修改三台虚拟机的主机名称为centos1，centos2，centos3	

```

sudo vi /etc/sysconfig/network

[root@localhost mac]# cat /etc/sysconfig/network
NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME=centos1

```

修改完成后没有立即生效，这里需要重启，如果不想重启可以使用下面命令  	

```
sudo hostname centos1
```

可以通过下面命令进行验证是否修改成功 

```
[root@localhost mac]# hostname
centos1

```

同样操作另外两台电脑 

6.由于拷贝完成后的虚拟机会多出来一个网卡，因此这里需要将centos2和centos3中多余的网卡删掉  

![](../assets/2017/02/2017-02-28_12-42-01.png)

首先查看文件/etc/udev/rules.d/70-persistent-net.rules 		

```

# PCI device 0x8086:0x100f (e1000)
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="00:0c:29:ba:52:6d", ATTR{type}=="1", KERNEL=="eth*", NAME="eth0"

# PCI device 0x8086:0x100f (e1000)
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="00:50:56:35:92:1e", ATTR{type}=="1", KERNEL=="eth*", NAME="eth1"

# PCI device 0x8086:0x100f (e1000)
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="00:50:56:36:21:ee", ATTR{type}=="1", KERNEL=="eth*", NAME="eth2"

```

查看自己的ip地址中的mac地址是哪个  	

```

ifconfig
eth2      Link encap:Ethernet  HWaddr 00:50:56:36:21:EE  
          inet addr:192.168.4.143  Bcast:192.168.4.255  Mask:255.255.255.0
          inet6 addr: fe80::250:56ff:fe36:21ee/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:148 errors:0 dropped:0 overruns:0 frame:0
          TX packets:111 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:16913 (16.5 KiB)  TX bytes:15711 (15.3 KiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:8 errors:0 dropped:0 overruns:0 frame:0
          TX packets:8 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:480 (480.0 b)  TX bytes:480 (480.0 b)


```			

发现是最后一个，因此需要将前面的两个删掉，同事修改最后一个的eth2位eth0，修改后的配置如下  

```

SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="00:50:56:36:21:ee", ATTR{type}=="1", KERNEL=="eth*", NAME="eth0"

```

最后修改/etc/sysconfig/network-scripts/目录下ifcfg-带有eh0的文件中的mac地址为上面的mac地址00:50:56:36:21:EE  

```
sudo vi /etc/sysconfig/network-scripts/ifcfg-Auto_eth0
```

修改后的文件如下 	

```
TYPE=Ethernet
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
IPV6INIT=no
NAME="Auto eth0"
UUID=bf045fa6-3ff3-4b53-9f9c-534985123cb1
ONBOOT=yes
HWADDR=00:50:56:36:21:EE
PEERDNS=yes
PEERROUTES=yes
LAST_CONNECT=1488272496

```

重启电脑就会发现网卡剩余一个了  	

同样修改另外一台电脑并重启   	 


6.修改三台虚拟机的ip地址  	

由于安装完虚拟机后的ip是随机分配ip，可能在重启电脑后，ip地址会换掉，这样集群就肯定会出问题，所以这里直接绑定固定的ip地址就可以了 		
这里的三台虚拟机的ip地址分配为如下  	
centos1 192.168.4.131
centos2 192.168.4.132
centos3 192.168.4.133

这样就进入虚拟机进行设置  

![](../assets/2017/02/2017-02-28_09-00-35.png)

![](../assets/2017/02/2017-02-28_09-01-40.png)

![](../assets/2017/02/2017-02-28_09-02-33.png)

修改完成后需要重启网卡  

```

sudo service network restart
[sudo] password for mac: 
Shutting down loopback interface:                          [  OK  ]
Bringing up loopback interface:                            [  OK  ]

```
将ip映射关系加入到centos1 centos2 和centos3中三台电脑的host文件中  

```
sudo vi /etc/hosts

```

加入

```

192.168.4.131   centos1.com     centos1
192.168.4.132   centos2.com     centos2
192.168.4.133   centos3.com     centos3

```

当然可以加入到自己的mac电脑上这样就可以直接ssh主机了 

按照上面步骤执行完成后，虚拟机是不能上网的，因为我们改成了固定ip地址，为了能同时上网，我们这里需要再添加一个网卡  

首先关闭虚拟机，然后添加网卡 

![](../assets/2017/02/2017-02-28_13-16-03.png)

![](../assets/2017/02/2017-02-28_13-17-25.png)

添加完成后启动系统测试下是否联网  

```

sudo service network restart
[sudo] password for mac: 
Shutting down loopback interface:                          [  OK  ]
Bringing up loopback interface:                            [  OK  ]
Bringing up interface Auto_eth0:  Active connection state: activated
Active connection path: /org/freedesktop/NetworkManager/ActiveConnection/4
                                                           [  OK  ]


```

```
[mac@centos1 ~]$ ping www.baidu.com
PING www.a.shifen.com (115.239.211.112) 56(84) bytes of data.
64 bytes from 115.239.211.112: icmp_seq=1 ttl=128 time=8.84 ms
64 bytes from 115.239.211.112: icmp_seq=2 ttl=128 time=8.66 ms
```

到这里，三台虚拟机的环境准备已经ok，下一步就是安装分布式系统了  	




