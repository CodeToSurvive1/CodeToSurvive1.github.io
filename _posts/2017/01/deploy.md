# 发布版本说明


## docker部署

一般情况下电脑重新启动后，docker会自动启动，这部分不需要关心，只有docker关闭后或者有问题之后才会重启docker

```

cd /Users/admin/macaca/reliable/reliable-macaca-docker-compose

make start


```


## reliable-macaca-slave部署  


一般新加入电脑后都充当slave，只需要将slave连接到master即可


如果发布的电脑与slave的代码在同一台电脑上，则执行下面部分

```

cd /Users/admin/macaca/reliable/reliable-macaca-slave

make slave
```


如果发布的电脑与slave的代码在不同一台电脑上，则执行下面部分

```

cd /Users/admin/macaca/reliable/reliable-macaca-slave

./bin/reliable-macaca-slave server -m masterIp:masterPort --verbose
```