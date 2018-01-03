# Docker Image 
## 概念
* 可用于创建容器的只读镜像
* 基于分层文件系统构建
* 每一层通常在上一层文件系统上增加或替换部分内容
* 镜像通常包含：一个轻量级的操作系统发行版，相关依赖，单个服务或应用
* 可以通过现有容器一层层创建，也可以用Dockerfile一次性构建多层

![1511951483674.jpg](https://i.loli.net/2017/12/24/5a3f6067e1532.jpg)


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





执行指令：
>* 启动一个compose 容器组： Docker-compose up -d
>* 查看进程：docker-compose ps
>* 查看Log：docker-compose log
>* 停止所有的容器组：docker-compose kill
>* 删除容器组：docker-compose rm -f
>* 容器扩展：docker-compose scale 




## Dockerfile 
### 基础构建
一个Docker image 的生成，依赖于一个Dockerfile
Dockerfile 实际上就是从原始image 一层层的添加组件构建起来的。 
生成Dockerfile , Dockerfile 需要与所有的Context文件放在同一目录下


	# Dockerfile -[Name]   // Dockerfile的名字
	FROM [Basic Image]   //制定原始image
	COPY [xxxx] /[yyyy]   //简单的从当前文件系统拷贝文件到Docker文件系统
	EXPOSE 8082 8083    //制定Container 侦听的网络接口
	ENTRYPOINT ["java", "-jar", "/review-0.0.1-SNAPSHOT.jar", "server", "config.yml”]  //制定Docker的启动脚本
	CMD []   // Docker 默认参数设置，该例里面留空


通过Docker file构建镜像 ： docker build -t [Image Name] [Source Directory] 

### 一些原则
* 尽量使用专用的构建镜像如openjdk， 而避免从基础镜像开始如ubuntu
* 应尽量使用 multiplestage build 
* 尽可能把指令写成一行以减少layer的build， 如 RUN apt-get -y update && apt-get install -y python
* 如果多个container有很多共同的layer， 建议构建自己的基础镜像以提高读取效率
* 在构建镜像的过程中使用清晰的tag便于管理
* 不要在container内部保存数据，在开发环境使用bind mount，在生产环境使用volume 
* 尽量使用swarm提供冗余和扩展性，使用docker stack deploy替代docker pull 部署提高效率
* 构建自己的容器 CICD Pipeline 提高发布效率
* 
* 
* 
* 
* 



