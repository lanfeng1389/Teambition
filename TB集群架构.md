# TB集群架构

### 1、TES 虚拟机部署架构

**集群架构**

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/pWwXnBoYvIQ31wLa/b517dfd2b5904ac7a9451d21235db7a83437.png)

1.  请求主链路。浏览器 -> SLB -> nginx -> traefik网关 -> k8s service -> pod。
    
2.  各部分解读。底座层是整个架构的基础，其中不仅包含了应用运行环境k8s，也包含了私有镜像仓库docker-registry，底座层负责控制和协调操作系统的各种资源，业务负责应用在集群中的高可用保障。应用层大部分是基于helm安装的k8s 资源（pod，job，configmap, deployment），非边缘服务replica数量均是>=3，且均是无状态服务，容器间通过service name调用，。如要数据落地，将容器内部的路径挂载到pvc中，pvc mount到本地磁盘中。应用层中还有少量的二开服务，目前二开服务通过pm2进程进行管理，nginx直接路由到宿主机端口上。master1的应用层还有TES前端和后端程序。流量分发层负责流量分发和负载，目前有两种分发方式：通过nginx直接分发到节点端口上；通过nginx分发到k8s的treafik网关中，再通过treafik网关对路由进行改写和转发，最终转发到应用的service上。
    
3.  **高可用**。第一层，slb做负载；第二层，nginx到gateway 做负载，优先到本机，另外两台backup ； 第三层，k8s service做负载；第四层，所有中间件均使用副本集模式，同时存储3份数据。