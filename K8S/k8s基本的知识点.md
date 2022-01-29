学习文档

k8s中文翻译文档：http://docs.kubernetes.org.cn/115.html

个人博客学习网站：https://www.huweihuang.com/kubernetes-notes/



https://jimmysong.io/kubernetes-handbook/guide/resource-quota-management.html



Kubernetes 入门&进阶实战，腾讯的案例，写的不错

https://mp.weixin.qq.com/s/mUF0AEncu3T2yDqKyt-0Ow

笔记：包含排查指南

https://feisky.gitbooks.io/kubernetes/content/deploy/kubernetes-configuration-best-practice.html

应用的架构图层

https://www.cnblogs.com/spec-dog/p/13035898.html

pod容器的分类 标准容器，sidecar容器，init 容器，ephemeral 容器 4种类型

https://segmentfault.com/a/1190000022860953

kubernetes 的架构

http://docs.kubernetes.org.cn/251.html

service与ingress的区别

https://blog.51cto.com/xinsz08/2697411



### 好的组件

资源视图隔离：lxcfs https://www.huweihuang.com/kubernetes-notes/resource/lxcfs/lxcfs.html

集群诊断的组件：kube-advisor



常见的操作可以通过以下kubectl命令完成：

- **kubectl get -** 列出资源
- **kubectl describe** - 显示资源的详细信息
- **kubectl logs** - 打印pod中的容器日志
- **kubectl exec** - pod中容器内部执行命令



### Kubernetes 特点

- **可移植**: 支持公有云，私有云，混合云，多重云（multi-cloud）
- **可扩展**: 模块化, 插件化, 可挂载, 可组合
- **自动化**: 自动部署，自动重启，自动复制，自动伸缩/扩展

Kubernetes能提供一个以“**容器为中心的基础架构**”

### kubernetes核心功能

- 服务的发现与负载的均衡； 

- 容器的自动装箱，我们也会把它叫做 scheduling，就是“调度”，把一个容器放到一个集群的某一个机器上，Kubernetes 会帮助我们去做存储的编排，让存储的声明周期与容器的生命周期能有一个连接； 

- Kubernetes 会帮助我们去做自动化的容器的恢复。在一个集群中，经常会出现宿主机的问题或者说是 OS 的问题，导致容器本身的不可用，Kubernetes 会自动地对这些不可用的容器进行恢复； 

- Kubernetes 会帮助我们去做应用的自动发布与应用的回滚，以及与应用相关的配置密文的管理； 

- 对于 job 类型任务，Kubernetes 可以去做批量的执行； 

- 为了让这个集群、这个应用更富有弹性，Kubernetes 也支持水平的伸缩

核心能力：

1、调度

2、自动修复

3、水平伸缩

###　kubernetes的架构

Kubernetes 架构是一个比较典型的二层架构和 server-client 架构。Master 作为中央的管控节点，会去与 Node 进行一个连接。

 所有 UI 的、clients、这些 user 侧的组件，只会和 Master 进行连接，把希望的状态或者想执行的命令下发给 Master，Master 会把这些命令或者状态下发给相应的节点，进行最终的执行

![image-20210614145640220](D:\A-资料文档\E-文档编写笔记文件夹\typora笔记\K8S\k8s核心概念.assets\image-20210614145640220.png)



Kubernetes 的 Master 包含四个主要的组件：API Server、Controller、Scheduler 以及 etcd

- **API Server：**顾名思义是用来处理 API 操作的，Kubernetes 中所有的组件都会和 API Server 进行连接，组件与组件之间一般不进行独立的连接，都依赖于 API Server 进行消息的传送； 
- **Controller：**是控制器，它用来完成对集群状态的一些管理。比如刚刚我们提到的两个例子之中，第一个自动对容器进行修复、第二个自动进行水平扩张，都是由 Kubernetes 中的 Controller 来进行完成的； 
- **Scheduler：**是调度器，“调度器”顾名思义就是完成调度的操作，就是我们刚才介绍的第一个例子中，把一个用户提交的 Container，依据它对 CPU、对 memory 请求大小，找一台合适的节点，进行放置； 
- **etcd：**是一个分布式的一个存储系统，API Server 中所需要的这些原信息都被放置在 etcd 中，etcd 本身是一个高可用系统，通过 etcd 保证整个 Kubernetes 的 Master 组件的高可用性、

![img](D:\A-资料文档\E-文档编写笔记文件夹\typora笔记\K8S\k8s核心概念.assets\PH8F]325@9@}7FP5[[AMMR.JPG)



Kubernetes 的 Node 是真正运行业务负载的，每个业务负载会以 Pod 的形式运行。等一下我会介绍一下 Pod 的概念。一个 Pod 中运行的一个或者多个容器，真正去运行这些 Pod 的组件的是叫做 **kubelet**，也就是 Node 上最为关键的组件，它通过 API Server 接收到所需要 Pod 运行的状态，然后提交到我们下面画的这个 Container Runtime 组件中

![img](D:\A-资料文档\E-文档编写笔记文件夹\typora笔记\K8S\k8s核心概念.assets\I93C4BUN1VDAP9K7_HV4W2.JPG)

#### pod 分类

在 Pod 中，容器也有分类，对这个感兴趣的同学欢迎自行阅读更多资料：

- **标准容器 Application Container**。
- **初始化容器 Init Container**。
- **边车容器 Sidecar Container**。
- **临时容器 Ephemeral Container**





### K8S的概念术语

+ k8s的Master Node组件
  + API Server 	k8s的请求入口
  + Scheduler
+ k8s的Master Node组件
  + dsad

namespace k8s的命名空间



