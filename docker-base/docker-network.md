# Docker网络学习

## Docker网络容器概述

默认的三种网络

> *  bridge

默认的本机网络,依赖`docker0`网桥。驻留在Docker Engine实例的单个主机上

采用bridge驱动自定义网络时支持以下选项

| option                                        | **Equivalent** | **Description**         |
| --------------------------------------------- | -------------- | ----------------------- |
| com.docker.network.bridge.name                | -              | 创建Linux时使用的网桥名 |
| com.docker.network.bridge.enable_ip_masuerade | --ip_masq      | 启用IP伪装              |
| com.docker.network.bridge.enable_icc          | - -icc         | 启用或者禁用跨容器连接  |
| com.docker.network.bridge.host_binding_ipv4   | - -ip          | 绑定容器端口时的默认IP  |
| com.docker.network.driver.mtu                 | - -mtu         | 设置容器网络MTU         |



> * none
> * host
> * overay

overlay网络可以跨越多个Docker Engine的多个主机，但是`overlay`的创建需要以下前提条件

- Key-Value存储。支持Consul、Zookeeper、Etcd
- 与Key-Value连接的主机集群
- 在swarm上每个主机正确配置`Docker daeon`

支持`overlay`的网络的`dockerd`的选项有：

- `--cluster-store`
- `--cluster-store-opt`
- `--cluster-advertise`

`--subnet`创建子网络，`bridge`网络只支持一个子网络，`overlay`支持多个子网。

**在创建网络的时候建议使用—subnet创建，不然可能导致重叠网络，无法启动**

`--gateway`

`--ip-range`

`--aux-address`

`overlay` 驱动也支持`com.docker.network.driver.mtu` 选项。

以下参数可以传递给任何网络驱动的`docker network create` 。

| Argument     | Equivalent | Description          |
| :----------- | :--------- | :------------------- |
| `--internal` | -          | 限制对网络的外部访问 |
| `--ipv6`     | `--ipv6`   | 启用IPv6网络         |



## 连接容器

可以将一个现有容器连接到一个或者多个网络。容器可以连接到不同驱动的网络。同一个网络下的容器可以使用IP或者名称通信。

对于支持多主机的`overlay`网络或者自定义插件，不同主机上的容器只要连接到同一`multi-host-network`多主机网络，也可以使用IP或者名称通信。



不同bridge驱动网络之间无法通信，可以通信的容器可以不用启动也可以通信，但是`docker etwork inspect `仅显示启动的容器

> 默认的bridge驱动网络，容器之间无法使用名称通信，但是可以使用内网IP

当您使用用户自定义网络时，默认情况下提供所有这些功能，无需额外的配置。 **此外，您可以动态attach到多个网络，也可动态从多个网络中离开。**

- 使用DNS进行自动名称解析
- 支持`--link` 选项为链接的容器提供名称别名
- 网络中容器的自动安全隔离环境
- 环境变量注入

`--link`的使用，可以再默认的birdge驱动的网络时，链接到容器进行通信(**PS:强烈建议自定义网络通信**)

- 使用`--link=CONTAINER-NAME:ALIAS` 定义一个网络别名去连接容器的能力

## Docker网络限制

###环境变量注入

环境变量注入时静态的，容器启动之后无法更改，例如`--link`

使用`docker-network`将容器连接到网络时，不能再容器间动态共享环境变量

### 网络范围的别名

`--network-alias`

`--alias`

多个容器可以再同一个网络使用一个别名，DNS轮询

## 命令大全

- docker network ls
- docker network create
- docker network connect
- docker network rm
- docker network disconnect
- docker network inspect





