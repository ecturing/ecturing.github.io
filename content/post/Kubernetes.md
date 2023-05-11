---
title: Kubernetes
date: 2022-01-13 23:25:34
tags:
---
# Kubernetes

## 基本概念

### 1.Kubernetes

> 它是一个为 **容器化** 应用提供集群部署和管理的开源工具，由 Google 开发。
> **Kubernetes** 这个名字源于希腊语，意为“舵手”或“飞行员”。k8s 这个缩写是因为 k 和 s 之间有八个字符的关系。 Google 在 2014 年开源了 Kubernetes 项目

K3S?

> k3s为轻量级Kubernetes，K3S是经CNCF一致性认证的Kubernetes发行版，专为物联网及边缘计算设计。

本文基于K3S进行编写

Kubernetes架构图解

![Kubernetes架构图解](https://sjwx.easydoc.xyz/46901064/files/kwoccq7d)

### 2.名词解释

<img src="https://kuboard.cn/assets/img/module_01_cluster.8f54b2c5.svg"  />

- `master`

  主节点，控制平台，不需要很高性能，不跑任务，通常一个就行了，也可以开多个主节点来提高集群可用度。

- `worker`

  工作节点，可以是虚拟机或物理计算机，任务都在这里跑，机器性能需要好点；通常都有很多个，可以不断加机器扩大集群；每个工作节点由主节点管理

- `Pod`

  豆荚，K8S 调度、管理的最小单位，一个 Pod 可以包含一个或多个容器，每个 Pod 有自己的虚拟IP。一个工作节点可以有多个 pod，主节点会考量负载自动调度 pod 到哪个节点运行。

### 3.Kubernetes组件

![](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)

- `kube-apiserver`API 服务器，公开了 Kubernetes API
- `etcd` 键值数据库，可以作为保存 Kubernetes 所有集群数据的后台数据库
- `kube-scheduler` 调度 Pod 到哪个节点运行
- `kube-controller` 集群控制器
- `cloud-controller` 与云服务商交互

## 主要特性

- 高可用，不宕机，自动灾难恢复
- 灰度更新，不影响业务正常运转
- 一键回滚到历史版本
- 方便的伸缩扩展（应用伸缩，机器加减）、提供负载均衡
- 有一个完善的生态

## 环境搭建

### K3S安装脚本

```bash
curl -sfL https://get.k3s.io | sh -
# Check for Ready node,
takes maybe 30 seconds
k3s kubectl get node
```

### 安装管理环境Kuboard

#### 使用Docker安装Kuboard

#### 导入K3S配置

#### 修改K3s服务地址

​	使用docker运行Kuboard时，Kuboard不会与K3S API Server运行端口处于同一网段，由于Docker使用虚拟网桥，产生了一个Ip域，K3SAPI Server默认运行在本地6643端口，所以IP域设置为Docker网桥地址:6643。

