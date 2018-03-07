# 鱼与熊掌
笼统了解了一下Kubernetes的架构设计，发现它的架构非常有意思，在一些方面实现了一定程度上得的“鱼与熊掌得兼”：
1. 系统本身高复杂——对外接口简单清晰
2. 高访问——没有性能瓶颈
3. 高可用的特性——实现上的低成本

总之就是很牛逼了

## Kubernetes中枢部分的“决”“策”分离

Kubernetes的组件可以大致分为俩部分：
1. 负责整个集群的【中枢】，中枢的首长是【api server】
2. 负责单个Node的【地方】，地方的行政长官是【kubelet】

中枢部分又包括了很多组件，但对外，不论是使用者，还是对地方，都只呈现出一个api server

所有用户对集群的指令，和所有kubelet的交互，都由api server完成

**Kubernetes通过“决”与“策”的分离，减轻了api server的负担**

api server负责【决】，所有引起集群状态变更的动作，都经过api server并由其负责落地（存储到etcd中），只有api server由读写etcd的权限

专门的参谋团负责【策】，其中最主要的俩个就是：
1. controller manager
2. scheduler

比如一个新的pod需要运行，api server会让scheduler负责考虑pod该运行在哪个Node上

scheduler考虑清楚后把方案提交给api server，api server对方案做持久化

对应Node上的kubelet去运行pod实例

## Kubernetes Client订阅机制
Kubernetes中组件的交互很频繁，但因为做了“决”“策”分离，负责具体事务的【中枢】职能官既不与etcd通信，也不与【地方】kubelet通信

所以Kubernetes的网络通信模型最后变成了所有组件都要而且只要与api server通信，一定程度上降低了复杂度和全局的通信负担，api server也变相承担了全局“锁”的作用，维持通信上的先后顺序

但是如此一来api server似乎负担会很重，宕机的可能性和成本都变大了

所以Kubernetes使用了带hook的client

> 提供一个api server的client，这个client带一个hook，使用者在api server上注册订阅，有相关的状态变动时，api server通过hook发布到使用者（订阅者）

> 相当于【中枢】不维护飞鸽，不需要知道飞鸽的目的地，而是【地方】和【中枢】的职能官自己在【中枢】处放一只飞鸽

这个设计带来的好处：
1. 双向通信变成单向通信，api server不需要感知到各个组件，也不需要实现各个组件的client
2. 减少握手成本和用于勘察状态的通信
3. 最关键的一点，减轻了api server的通信成本


## Kubernetes高可用
软件系统的高可用，通常是通过冗余和高频的通信和保持状态一致

### 中枢部分
api server支持分布式

其它职能组件原生不支持，但是在api server分布式的场景中，可以在每个api server的Node上
运行一套职能组件的副本，职能组件的多副本间是主备关系

但是只有api server知道哪个副本是主副本，因为副本之间没有通信

api server上的状态变动会通知到所有副本，但它只会采用主副本提供回来的方案，主副本异常后，api server再从新选择一个主副本

职能组件以此来实现了伪分布式的高可用

### kubelet
kubelet通常是以系统的service形式存在的，所以kubelet的高可用一般是有操作系统保证

