

# Docker概述
##Docker的特点
* 标准化的image，易于移植
* 系统最大程度的解耦和组件化
* 在系统内用同样的标准部署image，易于插拔
* 单个容器，单个进程
* 可以从Dev->Test->Prod所有环境部署相同的容器

### Docker Engine 架构
![WechatIMG157.jpeg](https://i.loli.net/2017/12/24/5a3f5f2621f14.jpeg)



## Docker 三大组件
> * Docker Daemon Process：用于监听与docker相关的请求
> * Docker Client：用户通过REST API 与Docker Engine 交互，发送请求
> * Docker Registry：用于存储和交易Docker image的仓库

## Docker的对象
> * image: Docker构建的产物，是一个打包好的dockerfile.
> * Container: Docker 运行的容器，可以通过CLI或者API来控制。 

## Docker-CE Installation

> * 利用Registory安装，需要添加官方的GPG Key
> * 需要指定Stable库以获取正式更新（每三个月），与之对应的Edge库每个月可获取测试更新。 
> * 在生产环境中，为了保证所有Host的Docker版本一致，安装时需要指定版本号，否则默认安装最新版。










Docker File 










Docker 运行指令








