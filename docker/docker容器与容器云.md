# docker容器与容器云
<!-- TOC -->

- [docker容器与容器云](#docker容器与容器云)
    - [经典云计算](#经典云计算)
    - [容器的生态](#容器的生态)
    - [docker deamon](#docker-deamon)
    - [docker 命令](#docker-命令)
    - [docker 命令结构图](#docker-命令结构图)
    - [docker 交互参数](#docker-交互参数)
    - [Docker内核知识](#docker内核知识)
    - [Docker架构概览](#docker架构概览)
    - [Docker 镜像管理](#docker-镜像管理)
        - [registry](#registry)
        - [repository](#repository)
    - [Docker数据卷](#docker数据卷)
    - [Docker网络管理](#docker网络管理)
        - [docker网络虚拟化架构图](#docker网络虚拟化架构图)
        - [CNM(Container Network Model)](#cnmcontainer-network-model)
        - [CNM组件图](#cnm组件图)
        - [网桥](#网桥)
        - [Docker网络bridge模式](#docker网络bridge模式)
        - [docker state](#docker-state)
        - [docker inspect](#docker-inspect)
        - [docker容器的监控](#docker容器的监控)
    - [微服务高可用](#微服务高可用)
        - [微服务协同工作](#微服务协同工作)
        - [多实例透明化](#多实例透明化)
        - [分布式通知与协调](#分布式通知与协调)
        - [分布式锁与竞选](#分布式锁与竞选)
    - [集群监控](#集群监控)
        - [ETCD VS ZOOKEEPER](#etcd-vs-zookeeper)

<!-- /TOC -->

> 最近看了docker容器与容器云这本书后, 画出的一些重点, 都是书上的一些知识与图

## 经典云计算
IaaS - Infrastructure as a Service
Paas - Platform as a Service
Saas - Software as a Service

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/classic.png)


## 容器的生态

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/ecology.png)

## docker deamon 

    1.docker deamon需要root权限
    2.默认绑定Unix Socket代替原有TCP端口
    3.默认Unix Socket属于root用户

## docker 命令

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/cmd.png)


## docker 命令结构图

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/dockerstruct.png)


## docker 交互参数

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/interaction.png)


## Docker内核知识

- namespace资源隔离

    chroot指令切换根目录挂载点，文件系统隔离
    
    ip、端口、路由 网络隔离
    
    进程间通信隔离
    
    pid与宿主机pid进行隔离
    
    ![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/namespace.png)

- cgroups资源限制
    
    process container 更名 control groups

    ![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/cgroups.png)

    ![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/cgroupsfunction.png)


## Docker架构概览

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/dockerstructview.png)


## Docker 镜像管理

Docker镜像含有启动Docker容器所需的文件系统结构及其内容，Docker镜像是Docker容器的静态视角，Docker容器是Docker镜像的运行状态

- 主要特点：
  
      1.分层
    
      2.写时复制(copy-on-write)
    
      3.内容寻址
    
      4.联合挂载

### registry
用以保存Docker镜像，还包括镜像层次结构与元数据

### repository
由具有某个功能的Docker镜像的所有迭代版本构成的镜像组

## Docker数据卷

- 无volume导致的问题:

      1.容器中的文件在宿主机上存在形式复杂
      2.多个容器之间的数据无法共享
      3.删除容器时，数据容易丢失

- volume是存在于一个或多个容器中的特定文件或文件夹，以独立于联合文件系统的形式在宿主机中存在，为数据的共享和持久化提供便利
    
      1.volume在容器创建的时候就初始化，运行时可以使用其中的文件
      2.不同容器中进行共享和重用
      3.对数据操作立马生效，并且不影响到镜像本身
      4.生存周期独立于容器生存周期，删除容器，volume依旧存在


## Docker网络管理

### docker网络虚拟化架构图

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/net.png)


### CNM(Container Network Model)

- sandbox 沙盒
- endpoint 端点
- network 网络

### CNM组件图

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/cnm.png)

**通过端点与沙盒 container1 与container3连通**

### 网桥

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/netbridge.png)

### Docker网络bridge模式

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/bridgemodel.png)

### docker state

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/dockerstate.png)


### docker inspect
查看镜像与容器底层详细信息

### docker容器的监控
- google的cAdvisor
- Datadog
- Prometheus

## 微服务高可用
### 微服务协同工作

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/micro.png)


### 多实例透明化

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/multiinstances.png)



### 分布式通知与协调

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/distributedadvice.png)


### 分布式锁与竞选

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/distributedlock1.png)
![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/distributedlock2.png)
![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/distributedlock3.png)
![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/distributedlock4.png)

## 集群监控

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/clusterandmonitor.png)


### ETCD VS ZOOKEEPER

![image](https://github.com/alexanderqiu/learning/blob/master/docker/img/etcdzk.png)