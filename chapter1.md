

# Docker概述
### Docker的特点
* 标准化的image，易于移植
* 系统最大程度的解耦和组件化
* 在系统内用同样的标准部署image，易于插拔
* 单个容器，单个进程
* 可以从Dev->Test->Prod所有环境部署相同的容器

### Docker Engine 架构
![enter description here][1]

## Docker 三大组件
> * Docker Daemon Process：用于监听与docker相关的请求
> * Docker Client：用户通过REST API 与Docker Engine 交互，发送请求
> * Docker Registry：用于存储和交易Docker image的仓库

## Docker的对象
> * image: Docker构建的产物，是一个打包好的dockerfile.
> * Container: Docker 运行的容器，可以通过CLI或者API来控制。 

## Docker-CE Installation
Installation in Ubuntu:  [下载地址][2]

> * 利用Registory安装，需要添加官方的GPG Key
> * 需要指定Stable库以获取正式更新（每三个月），与之对应的Edge库每个月可获取测试更新。 
> * 在生产环境中，为了保证所有Host的Docker版本一致，安装时需要指定版本号，否则默认安装最新版。


# Docker Image 
## 概念
* 可用于创建容器的只读镜像
* 基于分层文件系统构建
* 每一层通常在上一层文件系统上增加或替换部分内容
* 镜像通常包含：一个轻量级的操作系统发行版，相关依赖，单个服务或应用
* 可以通过现有容器一层层创建，也可以用Dockerfile一次性构建多层

![enter description here][3]


## Docker镜像基本操作
查看本地镜像： docker images
创建运行容器并执行指令 ： docker run [image name] [CMD xxxx] 
>* 从镜像创建容器
>* 执行指令
>* 保存执行结果
>* 关闭容器
>* 以上操作相当于在源image的基础上加上了一层操作，可以作为源容器生成更新的image

创建守护态容器并进行操作： docker run -itd [image name] /bin/bash
进入一个守护态容器： docker exec -it [Container ID] /bin/bash
查看Docker image的元数据 ： docker inspect [image name] 
查看image的构建层历史： docker history [image name]:latest
删除本地镜像：docker rmi [image Name]
查看容器状态： docker ps -a 
启动一个容器 docker start [Container ID]  // 以其docker run的指令再次启动一个容器 
停止容器：docker stop [Container ID]
删除容器： docker rm [Container ID] // 嵌套 `docker ps -a -q`可以删除所有容器
从容器生成镜像 ：docker commit [Container ID] [Image Name]

Docker基础指令快速查询：
http://www.cnblogs.com/xiadongqing/p/6144053.html


## Dockerfile 
一个Docker image 的生成，依赖于一个Dockerfile
Dockerfile 实际上就是从原始image 一层层的添加组件构建起来的。 
生成Dockerfile , Dockerfile 需要与所有的Context文件放在同一目录下
```
# Dockerfile -[Name]   // Dockerfile的名字
FROM [Basic Image]   //制定原始image
COPY [xxxx] /[yyyy]   //简单的从当前文件系统拷贝文件到Docker文件系统
EXPOSE 8082 8083    //制定Container 侦听的网络接口

ENTRYPOINT ["java", "-jar", "/review-0.0.1-SNAPSHOT.jar", "server", "config.yml”]  //制定Docker的启动脚本
CMD []   // Docker 默认参数设置，该例里面留空
```

通过Docker file构建镜像 ： docker build -t [Image Name] [Source Directory] 


## Docker Repostroy
### 私有库
安装本地库： docker run -d -p 5000:5000 --restart=always --name registry registry:2
标记本地镜像：docker tag [Old Image Name] [IP Address / localhost]:[Port]/[New Image Name] 
推送到本地库： docker push [IP Address / localhost]:[Port]/[New Image Name] 
查看本地库中的镜像： http://localhost:5000/v2/_catalog

### Docker Hub 操作
登录docker hub：  docker login
重命名本地镜像为dockerhub目标库格式：docker tag [Old Image Name] [username]/[repostroy]:[New Image Name] 
推送本地镜像到dockerhub： docker push [username]/[repostroy]:[Image Name]
从dockerhub 拉去images： docker pull [username]/[repostroy]:[Image Name]

## Docker-compose多容器管理
利用docker-compose.yml运行多容器，例如：
```
version: "3"
shop-app:   
  image: registry:5000/shop-app     //指定需要启动的image
  ports:
    - "8080:8080”   //指定访问的端口映射
  links:
    - review    //指定链接
    - catalogue

review:
  image: registry:5000/review

catalogue:
  image: registry:5000/catalogue

services:
  web:
    image: username/repo:tag
    deploy:
      replicas: 5         //指定scale node 个数
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
networks:
  webnet:


```
执行指令：
>* 启动一个compose 容器组： Docker-compose up -d
>* 查看进程：docker-compose ps
>* 查看Log：docker-compose log
>* 停止所有的容器组：docker-compose kill
>* 删除容器组：docker-compose rm -f
>* 容器扩展：docker-compose scale 




# Docker SWARM
## 概述 
Docker SWARM用于Docker的集群管理



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
```
➜  ~ docker-machine ls
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
myvm1   *        virtualbox   Running   tcp://192.168.99.100:2376           v17.09.1-ce
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v17.09.1-ce
```
取消VM Shell 关联： eval $(docker-machine env -u)

### Docker Swarm
仅仅管理Swarm manager即可自动将scale的VM分配到所有的swarm node上
初始化swarm： docker swarm init --advertise-addr [IP address]
加入一个swarm集群： docker swarm join --token [Token Code] [IP Address]:2377
查看swarm集群状态： docker node ls
移除docker swarm： 
> docker swarm leave 
> docker swarm leave --force   // Swarm Manager 上使用

Docker stack 集群部署
利用docker-compose 部署： docker stack deploy -c docker-compose.yml [stack name]
删除集群：docker stack rm [stackname]





Docker File 







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
networks:
  webnet:  
 


Docker 运行指令


  [1]: ./images/1511951117239.jpg
  [2]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
  [3]: ./images/1511951483674.jpg





