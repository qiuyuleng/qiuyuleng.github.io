---
title: "K8s in Action"
typora-root-url: ../
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

# 第一章：Kubernetes 介绍

Kubernetes 通过对实际硬件做抽象，然后将自身暴露成一个平台，用于部署和运行应用程序。它允许开发者自己配置和部署应用程序，而不需要系统管理员的帮助，让开发者聚焦于保持底层基础设施运转正常的同时，不需要关注实际运行在平台上的应用程序。

K8s系统由一个主节点和若干个工作节点组成。开发者把一个应用列表提交到主节点，k8s会把它们部署到集群的工作节点。组件被具体部署在哪个节点对于开发者和系统管理员来说都不用关心。

![image-20230223231226259](/../img/image-20230223231226259.png)

- 控制面板中的组件运行在单个或多个主节点上
  - API服务器: 其他控制面包组件都与它通信
  - Scheduler: 调度应用，为每个组件分配一个工作节点
  - Controller Manager: 执行集群级别的功能，如复制组件、持续跟踪工作节点、处理节点失败
  - etcd: 分布式数据存储，储存集群配置
- 工作节点：运行容器化应用的机器
  - Kubelet: 与API服务器通信，管理它所在节点的容器
  - Kube-proxy: 负责组件之间的负载均衡网络流量

# 第二章：开始使用K8S和Docker

`kubectl cluster-info` 展示集群信息

`kubectl get` 列出各种kubernetes对象的基本信息, e.g., `kubectl get nodes `

`kubectl describe node xxx` 查看更详细的信息

为kubectl创建别名 `alias k=kubectl` , tab命令补全

pod: 一组紧密相关的容器，运行在同一个工作节点上，同一个linux命名空间内

不能用`kubectl get` 列出单个容器，因为它们不是独立的k8s对象，但是可以列出pod，`kubectl get pods`

调度scheduling：将pod分配给一个节点

`kubectl expose re kubia --type=LoadBalancer --name kubia-http` 创建外部的负载均衡，可以通过负载均衡的公共ip访问pod

![image-20230223231331497](/../img/image-20230223231331497.png)

kubia-http服务：解决不断变化的pod IP地址的问题

ReplicationController：负责pod并让它们保持运行

# 第三章：pod: 运行于Kubernetes中的容器

## 介绍pod

一个pod不会跨越多个工作节点

![image-20230223202908875](/../img/image-20230223202908875.png)

想象一个由多个进程组成的应用程序，由于不能将多个进程聚集在一个单独的容器中（很难确定每个进程分别记录了什么），我们需要用pod将容器绑定在一起，作为一个单元进行管理

Kubernetes 通过配置Docker 来让一个pod 内的所有容器共享相同的Linux 命名空间、网络、IPC（进程间通信）命名空间，可以通过IPC进行通信。也可以共享PID命名空间。

同一pod 中的容器运行的多个进程需要注意不能绑定到相同的端口号（共享network命名空间），否则会导致端口冲突。

- 容器可以通过localhost与同一pod中的其他容器进行通信。

![image-20230223202938053](/../img/image-20230223202938053.png)

每个pod都可以通过其他pod 的IP 地址来实现相互访问，它们之间没有NAT (网络地址转换） 网关，不管是不是在不同的server上。

这个平面网络是由额外的软件基于真实链路实现的。

### 通过pod合理管理容器

我们应该将应用程序组织到多个pod 中， 而每个pod 只包含紧密相关的组件或进程。

- 多层应用分担到多个pod中，提高基础架构的利用率
- pod是扩缩容的基本单位，不同组件有不同的扩缩容要求

![image-20230223203032183](/../img/image-20230223203032183.png)

## 以YAML或JSON描述文件创建pod

YAML包含：

- Kubernetes API 版本
- YAML描述的资源类型
- metadata
- spec
- status （运行时的只读数据，创建pod时不需要提供）

![image-20230223203302043](/../img/image-20230223203302043.png)

指定容器端口纯粹是展示性的（informational），但是仍然是有意义的，这样每个使用集群的人都可以快速查看每个pod对外暴露的端口。

`kubectl explain pods` 来查看pods支持哪些属性，例如`kubectl explain pod.spec`

`kubectl create -f xxx.yaml `从YAML文件或JSON创建pod（及其他k8s资源）

`kubectl get po xxx -o yaml` 查看该pod的完整描述文件

`kubectl logs xxx` 获取pod日志

- `kubectl logs pod_name -c container_name`
- pod被删除后，日志也会被删除（需要设置集中的日志系统）

通过向pod发送请求来测试pod：

- 将本地网络端口转发到pod中的端口：kubect1 port-forward kubia-manual 8888:8080

- 通过端口转发连接到pod：另开一个终端，curl localhost:8888

## 使用标签组织pod

标签是可以附加到任意k8s资源的键值对。

一个资源可以拥有多个标签，在创建资源时可以将标签附加到资源上， 之后也可以再添加其他标签， 或者修改现有标签的值。

`kubectl get pods --show-labels` 列出所有标签

`kubectl get po -L xxx,xxx` 列出指定的标签

`kubectl label po xxx creation_method=manual` 添加标签

`kubectl label po xxx creation_method=manual --overwrite` 更改标签

## 通过标签选择器列出pod子集

`kubectl get po -l xxx`  标签选择器，多个条件的话用，隔开

## 使用标签选择器来约束pod调度

使用节点标签和节点标签选择器来描述pod对于节点的需求， 使Kubemetes选择一个符合这些需求的节点。

运维团队向集群添加新节点时，通过标签对节点进行分类，e.g., gpu=true, 唯一标签key为kubernetes.io/hostname，值为该节点的实际主机名

将pod调度到特定节点：在spec部分添加一个nodeSelector字段

![image-20230223204213495](/../img/image-20230223204213495.png)

## 注解pod

注解也是键值对，但是不能像标签一样用于对对象进行分组。

用作为pod或其他对象添加说明，使得使用者可以快速了解信息

查找对象的注解：获取完整YAML或者JSON文件（`k get po xxx -o yaml` 或` k describe pod xxx` ）

`kubectl annotate pod kubia-manual mycompany.com/someannotation="foo bar"` 添加注解

## 使用命名空间对资源进行分组

想将对象分割成完全独立且不重叠的组，允许我们多次使用相同的资源名称（跨不同的命名空间）

如果有多个用户或用户组正在使用同一个Kubernetes 集群，并且它们都各自管理自己独特的资源集合，那么它们就应该分别使用各自的命名空间。不用担心删除更改其他用户的资源，无需担心名称冲突

命名空间为资源名称提供了一个作用域

### 发现、创建、管理命名空间

`k get ns` 列出集群中所有的命名空间

`k get po --namespace(or: -n) xxx` 列出xxx命名空间下的pod（默认是列出default下的）

从YAML文件创建命名空间

![image-20230223204512119](/../img/image-20230223204512119.png)

`k create namespace xxx` 创建命名空间

命名空间的名字不允许包含点号

在特定命名空间中创建资源

- YAML文件的metadata字段添加 `namespace:xxxx`
- `k create -f xxx.yaml -n name_space`

`kubectl config` 管理默认的命名空间

- `kubectl config set context $ (kubectl config current context) -- namespace xxx`

命名空间也行不提供网络隔离（看具体配置）等等隔离

## 停止和移除pod

30秒

按名称删除pod

`kubectl delete po xxx1 xxx2 xxx3` 

使用标签选择器删除pod

`k delete po -l xxx=xxx` 

删除整个命名空间来删除pod

`k delete ns xxx `

删除命名空间中的所有pod，但保留命名空间

`k delete po --all`  删除当前命名空间中的所有pod

删除命名空间中的（几乎）所有资源

`k delete all --all` 

# 第四章：副本机制和其他控制器：部署托管的pod

## 保持pod健康：存活探针 (liveness probe)

有三种探针机制：

- HTTP GET探针
- TCP套接字探针
- Exec探针

![image-20230223205111809](/../img/image-20230223205111809.png)

应该给探针设置`initialDelaySeconds`

查看之前容器为什么终止，应该看前一个pod的日志，而不是当前pod的：`k logs mypod --previous`

通过`k describe po xxx`来了解po状态，关注其中的exit code和Events

Exit Code: 128+x, 其中x是终止进程的信号编号。e.g., 137=128+9，9是SIGKILL的信号编号

如何创建有效的存活探针：

- 应该给探针设置initialDelaySeconds，留出应用程序的启动时间
- 存活探针应该检查什么，应用程序内部
- 保持探针轻量
- 无需在探针中实现重试循环

存活探针运行在pod工作节点上的kubelet，部署运行在主服务器上。如果工作节点本身崩溃，存活探针无法执行任何操作，这时候需要ReplicationController。

## ReplicationController

ReplicationController是一种k8s资源，可以确保pod始终保持运行状态

![image-20230223205205852](/../img/image-20230223205205852.png)

### ReplicationController的协调流程

![image-20230223205232215](/../img/image-20230223205232215.png)

ReplicationController的三个部分：

- label selector 标签选择器，用于确定RC作用域中有哪些pod
  - 更改后对现有的pod没有影响，会使现有的pod脱离RC的范围，控制器会停止关注它们
- replica count 副本个数，指定运行的pod数量
- pod template pod模板，用于创建新的pod副本
  - 更改后只影响RC新创建的pod

### 创建RC

![image-20230223205308714](/../img/image-20230223205308714.png)

pod模板中的标签要与RC的selector一致，或者省略selector标签，它会自动根据pod模板中的标签来配置。

### 修改pod模板

![image-20230223205353081](/../img/image-20230223205353081.png)

不影响旧的pod

`k edit rc kubia` 修改pod模板

### 水平缩放pod

陈述式

更改rc.yaml文件中的spec.replicas

### 删除RC

`k delete rc` 删除rc，由rc管理的pod也会被删除

`k delete rc --cascade=false/orphan` 删除rc，保留由rc创建的pod

## 使用ReplicaSet而不是ReplicationController

二者区别：ReplicaSet的标签选择器更强

- 支持标签组，e.g., RC无法将pod与env=production和env-devil同时匹配，ReplicaSet可以
- 支持仅基于标签名的存在来匹配pod，类似于env=*

### 定义ReplicaSet

![image-20230223205627766](/../img/image-20230223205627766.png)

`k create -f kubia-replicaset.yaml`  报错 no matches for kind "ReplicaSet" in version "apps/v1beta2"。

解决方案：查看版本`kubectl api-versions`，改成app/v1

`k get rs`： replicaset的缩写是rs

`k describe rs`

### ReplicaSet强大的标签选择器

![image-20230223210345784](/../img/image-20230223210345784.png)

![image-20230223210400110](/../img/image-20230223210400110.png)

## 使用DaemonSet在每个节点上运行一个pod

希望在集群中的每个节点上运行一个pod（例如日志收集器、资源监控器、kube-proxy进程等系统服务），需要创建DaemonSet对象

DaemonSet的调度不受节点不可调度属性的影响

通过pod模板中的nodeSelector属性指定部署到哪些特定的节点

![image-20230223210447697](/../img/image-20230223210447697.png)

`k get ds` DaemonSet缩写是ds

`k label node node_name disk=ssd` 给节点打标签

更改node的标签后，由ds管理的pod也会被删除

## 运行执行单个任务的pod

Job资源：运行一个pod，这个pod在任务完成后，变成完成状态。例如，临时任务（需要以正确的方式结束）

在发生节点故障时，该节点上由Job 管理的pod将按照ReplicaSet 的pod 的方式，重新安排到其他节点。

如果进程本身异常退出（进程返回错误退出代码时）， 可以将Job 配置为重新启动容器。

![image-20230223210528212](/../img/image-20230223210528212.png)

### 定义Job资源

![image-20230223210633635](/../img/image-20230223210633635.png)

其中的restartPolicy属性指定容器中运行的进程结束时，k8s会做什么。默认是always，但是job资源不能使用默认策略。因此要将restartPolicy设定为OnFailure或者Never

### 在Job中运行多个pod实例

![image-20230223210712738](/../img/image-20230223210712738.png)

也可也通过缩放job来更改parallelism属性：k scale job job_name --replicas 3

### 限制Job pod完成任务的时间

在pod template中设置activeDeadlineSeconds属性来限制pod时间。如果超过这个时间，系统就会终止pod，并将job标记为失败。

## 安排Job定期运行或在将来运行一次

### 创建CronJob

![image-20230223211047473](/../img/image-20230223211047473.png)

cron时间表格式，从左到右分别是 分钟 小时 每月中的第几天 月 星期几。

对于对时间要求较高的任务，可以设置startingDeadlineSeconds来设置截止时间。到了截止时间不执行任务会直接failed。

![image-20230223211110361](/../img/image-20230223211110361.png)

# 服务：让客户端发现pod并与之通信

## 介绍服务

k8s服务：为一组相同功能的pod提供单一不变的接入点的资源。

服务存在时，它的IP地址和端口号不会改变（pod的IP地址可能会改变），客户端通过IP和端口号建立连接，连接会被路由到提供该服务的任意一个pod上。因此，客户端不需要知道每个pod的地址。

![image-20230223211809059](/../img/image-20230223211809059.png)

服务通过标签选择器来指定pod

![image-20230223211823003](/../img/image-20230223211823003.png)

### 创建服务

- `k expose `
- 通过yaml文件

![image-20230223211916588](/../img/image-20230223211916588.png)

在80端口接受请求，然后路由到标签选择器app=kubia的pod的8080端口上

service的缩写是svc

### 在运行的容器中远程执行命令

`k exec pod_name -- curl -s http://10.111.249.153`

其中，双横杠“--”表示k命令项的结束，双横杠之后的内容是pod内部需要执行的命令

![image-20230223212039897](/../img/image-20230223212039897.png)

### 会话亲和性

希望特定客户端产生的所有请求每次都指向同一个pod

- spec.sessionAffinity = ClientIP
- 会话亲和性不能基于cookie

![image-20230223212104034](/../img/image-20230223212104034.png)

### 端口命名

同一个服务可以暴露多个端口（需要给每个端口指定名字），但是标签选择器是针对整个服务的，不能对每个端口做单独的配置

可以命名端口，好处是即使更改端口号也无需更改服务spec

![image-20230223212139086](/../img/image-20230223212139086.png)

### 服务发现

客户端pod如何知道服务的IP和端口？

#### 通过环境变量发现服务

如果创建的服务早于客户端pod的创建，可以客户端pod可以通过环境变量获取服务的IP和端口号

![image-20230223212220282](/../img/image-20230223212220282.png)

`KUBIA_SERVICE_HOST` 服务集群的IP 和` KUBIA_SERVICE_PORT` 服务所在的端口，所有字母大写，横杠被转化成下划线

#### 通过DNS发现服务

pod `kube-dns` 运行DNS服务，集群中的其他pod使用其作为dns（k8s通过修改每个容器的/etc/resolv.conf实现）

pod是否使用内部DNS服务器是根据pod中spec的`dnsPolicy`属性来决定的

#### 通过全限定域名（Fully qualified domain name,FQDN）连接服务

FQDN链接：服务名称.命名空间.svc.cluster.local

其中svc.cluster.local是集群本地服务名称中使用的可配置集群域后缀

如果两个pod在同一个命名空间下，可以省略命名空间和svc.cluster.local后缀

#### 无法ping通服务IP的原因

在pod内ping服务ip无法成功，因为这是一个虚拟IP，只有在和服务端口结合时才有意义

## 连接集群外部的服务

通过k8s service的特性暴露外部服务，不要让服务将连接重定向到集群中的pod，而是重定向到外部IP和端口

可以利用服务负载均衡的特点

### 介绍Endpoints资源

![image-20230223212420872](/../img/image-20230223212420872.png)

Endpoints资源：暴露一个服务的IP地址和端口的列表

`k get endpoints kubia` 

缩写`ep`

尽管定义了Selector标签选择器，但是在重定向传入连接时不会直接使用它。标签选择器用于构建IP和端口列表，然后储存在Endpoint资源中。在客户端连接到服务时，服务代理选择这些IP和端口对中的一个，并将传入连接重定向到该位置监听的服务器

### 手动配置服务的endpoint

创建没有标签选择器的服务

![image-20230223212452640](/../img/image-20230223212452640.png)

为没有标签选择器的服务创建Endpoint资源

![image-20230223212513087](/../img/image-20230223212513087.png)

Endpoints对象需要与服务具有相同的名称

![image-20230223212917920](/../img/image-20230223212917920.png)

其中，11.11.11.11:80和22.22.22.22:80是外部IP:端口的例子

### 为外部服务创建别名

通过FQDN访问外部服务

![image-20230223212946430](/../img/image-20230223212946430.png)

服务创建后，可以通过external-service.default.svc.cluster.local域名（甚至external-service）连接到外部服务，不需要外部服务实际的FQDN

ExternalName服务仅在DNS级别实施，因此连接到服务的客户端将直接连接到外部服务，绕过了服务代理

## 将服务暴露给外部客户端

需要向外部公开某些服务，例如前端web服务器，以便外部客户端可以访问它们

![image-20230223213010374](/../img/image-20230223213010374.png)

三种方式：

- 将服务的类型设置成NodePort
- 将服务的类型设置成LoadBalance
- 创建一个Ingress资源

### 使用NodePort类型的服务

可以通过内部集群IP访问NodePort服务，还可以通过任何节点IP和预留节点端口访问NodePort服务

![image-20230223213104496](/../img/image-20230223213104496.png)

指定集群节点的节点端口不是必需的。如果忽略，k8s会随机选择一个端口

可以通过以下地址访问该服务：

- 10.100.138.138:80
- <1st node's IP>:30123
- <2nd node's IP>:30123

![image-20230223213129351](/../img/image-20230223213129351.png)

到达任何一个节点30123端口的传入连接将被重定向到一个随机选择的pod，该pod是否位于接收到连接的节点上是不确定的

### 创建LoadBalance类型的服务

![image-20230223213204859](/../img/image-20230223213204859.png)

LoadBalancer类型的服务是一个具有额外的基础设施提供的负载均衡器NodePort服务

![image-20230223213223000](/../img/image-20230223213223000.png)

### 了解外部连接的特性

#### 防止不必要的网络跳数

当外部客户端通过节点端口连接到服务时，随机选择的pod不一定在接受连接的同一节点上运行，需要额外的网络跳转才能到达pod

可以将服务配置为仅将外部通信重定向到接收连接的节点上运行的pod来阻止此额外跳数（local外部流量策略）

![image-20230223213255800](/../img/image-20230223213255800.png)

如果没有本地pod存在，连接将挂起，并不会转发到随机的全局pod（缺点一，需要负载均衡器将连接转发给至少具有一个pod的节点）

缺点二：可能导致负载分配不均衡

![image-20230223213315696](/../img/image-20230223213315696.png)

### 客户端IP是不记录的

当通过节点端口接收到客户端的连接时， 由于对数据包执行了源网络地址转换(SNAT), 因此数据包的源IP将发生更改，无法得知实际的客户端IP

如果使用local外部流量策略，那么IP不会更改，因为在接受连接的节点和托管目标pod的节点之间没有额外的跳跃。

## 通过Ingress暴露服务

Ingress只需要一个公网IP就能为许多服务提供访问，在网络栈（HTTP）应用层操作

![image-20230223213343460](/../img/image-20230223213343460.png)

![image-20230223213439582](/../img/image-20230223213439582.png)

No ingress pod on the server

Ingress工作原理：不会直接将请求转发给该服务，而是用它来选择一个pod

![image-20230223213501292](/../img/image-20230223213501292.png)

### 通过相同的ingress暴露多个服务

#### 将不同的服务映射到相同主机的不同路径

path和rules都是数组。可以根据URL中的不同路径，通过同一个IP地址（Ingress控制器的IP地址）访问两个不同的服务

![image-20230223213533864](/../img/image-20230223213533864.png)

#### 将不同的服务映射到不同的主机上

![image-20230223213550180](/../img/image-20230223213550180.png)

DNS需要将foo.example.com和bar.example.com都指向ingress控制器的IP地址

### 配置Ingress处理TLS传输

客户端和控制器之间的通信是加密的，而控制器和后端pod之间的通信则不是

Ingress负责处理与TLS相关的所有内容

## pod就绪后发出信号

### 介绍就绪探针

就绪探针类型：Exec探针、HTTP GET探针、TCP socket探针

![image-20230223213637076](/../img/image-20230223213637076.png)

### 向pod中添加就绪探针

![image-20230223213702129](/../img/image-20230223213702129.png)

## 使用headless服务来发现独立的pod

将service的spec中的clusterIP设置为None，就会使服务成为headless服务。

k8s不会为headless服务分配集群IP，客户端可以通过这个IP连接到支持它的pod

![image-20230223213741894](/../img/image-20230223213741894.png)

## 排除服务故障
[todo]

# 卷：将磁盘挂载到容器
在pod启动时创建卷，并在删除pod时销毁卷。
卷不是独立的k8s对象，不能单独创建或删除。

卷的类型：
- emptyDir卷
- gitRepo卷：创建后不能与github repo保持同步，创建新的pod时，新的gitRepo卷会包含最新的commit。不能用于private github repo，如果想。用在private github repo上，可以使用git sync sidecar。
- hostPath卷：指向host machine上特定的文件或目录。大多数pod不应该访问主机的文件系统的文件，系统级别的pod（DeamonSet管理的）可能需要。持久性存储，当pod被删除时，hostPath volume不会被删除
  - 但是不建议用hostPath卷作为存储数据库数据的目录。因为卷的内容存储在特定节点的文件系统中，当数据库pod被安排在另一个节点上时，会找不到数据。

sidecar容器：为了对主容器进行操作的容器。例如：git sync sidecar

> 自己部署k8s集群参考链接：
>
> sealos:
>
> - https://developer.aliyun.com/article/1113271#slide-6
> - https://github.com/labring/sealos
>
> rancher:
>
> - https://blog.csdn.net/weixin_47752736/article/details/125018358#:~:text=rancher%20%E5%AE%89%E8%A3%85%20%E9%83%A8%E7%BD%B2%20k8s%201%201.%20%E4%B8%8B%E8%BD%BDRancher%202,5.%20%E8%BF%9B%E5%85%A5rancher%E6%8E%A7%E5%88%B6%E5%8F%B0%206%206.%20%E9%80%9A%E8%BF%87%E9%83%A8%E7%BD%B2rancher%E7%9A%84%E5%AE%B9%E5%99%A8%E8%BF%9B%E5%85%A5%207%207.%20%E9%83%A8%E7%BD%B2Prometheus%E7%9B%91%E6%8E%A7%E9%A1%B5%E9%9D%A2
> - https://www.rancher.cn/quick-start/