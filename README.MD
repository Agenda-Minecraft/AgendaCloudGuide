# MEtcdCloud

这是一套在 Minecraft 中可用的云原生微服务架构

## 为什么制造这一系列生态

闲的

## 为什么不用k8s API

为了保证插件在没有 k8s 的情况下也能使用，故不使用 k8s api

## 需要掌握的预备知识

搭建Kubenetes 搭建 Consul 有一定的 k8s 或 docker swarm 运维经验 制作容器镜像

## 组件

### 基础组件

AgendaCloud（已实现） 一种服务器状态上报插件 https://github.com/Agenda-Minecraft/AgendaCloud

AgendaCloud-Menu（优化渲染） 一种通过Consul来渲染服务器菜单列表的插件 https://github.com/Agenda-Minecraft/AgendaCloud-Menu

ATP（只实现了跨服） 一种使用消息队列的跨服传送插件 https://github.com/Agenda-Minecraft/ATP

ABCRegister （已实现）一种使用Consul向BungeeCord动态添加服务的插件 https://github.com/Agenda-Minecraft/ABCRegister

### 聊天

EtcdChat（已实现）一种使用Etcd的，可以订阅频道的聊天插件 https://github.com/Agenda-Minecraft/EtcdChat

AgendaAntiSpam（聊天频率控制，Redis）一种使用Redis实现聊天频率控制的插件[https://github.com/Agenda-Minecraft/AgendaAntiSpam](https://github.com/Agenda-Minecraft/AgendaAntiSpam)

### 经济

UIS（已实现）一种可以使用多种数据库（包括tidb）的商店插件 https://github.com/Agenda-Minecraft/UltimateInventoryShop

### 娱乐

#### MinuteRush
MinuteRush（实现了一小部分）一种完成任务的小游戏插件https://github.com/Agenda-Minecraft/MinuteRush

UHCReloaded（付费的 UHC 插件）https://www.spigotmc.org/resources/uhcreloaded-%E2%9C%85-designed-for-networks-small-servers.2900/
UHCReloaded-Consul（UHCReloaded Consul 兼容插件）Patch https://github.com/Agenda-Minecraft/UHCReloaded-ACloudPatch

## 如何暴露服务

需要在公网搭建Kubenetes，在公网服务器上运行BungeeCord的副本集，并且对于每个Node暴露NodePort（需要扩展NodePort范围至25565），使用Selector匹配到BungeeCord所在的Pod

## 模板

### 编写副本集（ReplicaSet）

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: mctest
  labels:
    app: mctest
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mctest
  template:
    metadata:
      labels:
        app: mctest
    spec:
      containers:
      - name: mctest
        image: docker.io/BungeeCord镜像:tag
```

## 编写Service暴露端口

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mctest
spec:
  ports:
    - protocol: TCP
      port: 25565
      targetPort: 25565
      nodePort: 32000
  selector:
    app: mctest
  type: NodePort
```

正在编写…
