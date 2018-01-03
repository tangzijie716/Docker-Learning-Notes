#Docker SWARM
## 概述 

Docker 集群分为了三个层级： 
> * Container 单个容器 
> * Stack 多个容器组成的堆叠
> * Service 单个容器或者多个容器提供的服务
  

Docker SWARM就是用于调度和管理以上的service或者stack的容器集群.
Dokcer SWARM可以管理任意类型的docker 主机，包括物理机，虚拟机 


## 利用 Docker-Machine管理docker VMs

### Docker Machine安装
Windows 和 Mac OS下都默认预装
Ubuntu下安装： 
```
$ curl -L https://github.com/docker/machine/releases/download/v0.13.0/docker-machine-`uname -s`-`uname -m` >/tmp/docker-machine &&
chmod +x /tmp/docker-machine &&
sudo cp /tmp/docker-machine /usr/local/bin/docker-machine
```
### Docker VM操作
Docker VM需要启用Oracle Virtualbox 虚拟平台
创建Docker VMs： docker-machine create --driver virtualbox [VM Name]
查看Docker VMs： docker-machine ls 
启动关闭docker VM： docker-machine start/stop [VM name]
复制文件到Docker VM：docker-machine scp [filename] [VM name]

#### Docker-machine ssh
在Docker VM中执行命令： docker-machine ssh [VM Name] "[CMD Line]" 
#### Docker-machine env 
关联进入Docker VM脚本： eval $(docker-machine env [vm name]) 
进入后，所有本地指令都在VM的Shell上执行的
查看状态，* 表示已激活关联Shell的VM

	~ docker-machine ls
	NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
	myvm1   *        virtualbox   Running   tcp://192.168.99.100:2376           v17.09.1-ce
	myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v17.09.1-ce

取消VM Shell 关联： eval $(docker-machine env -u)


## Docker Swarm
仅仅管理Swarm manager即可自动将scale的VM分配到所有的swarm node上
初始化swarm： docker swarm init --advertise-addr [IP address]
加入一个swarm集群： docker swarm join --token [Token Code] [IP Address]:2377
查看swarm集群状态： docker node ls
移除docker swarm： 
> docker swarm leave 
> docker swarm leave --force   // Swarm Manager 上使用

##Docker stack 集群部署
利用docker-compose 部署： docker stack deploy -c docker-compose.yml [stack name]

	services:
	web:
	    image: username/repo:tag
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "80:80"
    networks:
      - webnet

    visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]    //指定部署的Node
    networks:
      - webnet   
	networks:
	  webnet:  

查看stack状态： docker stack ps [stackname]
删除集群：docker stack rm [stackname]


## Docker Service
服务都是依靠docker stack 部署的

查看服务状态： docker service ps [服务名称_应用名]
查看启动的服务清单  docker service ls

xxx
