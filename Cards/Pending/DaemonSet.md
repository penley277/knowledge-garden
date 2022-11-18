---
aliases: 
date created: 2022-08-04
date modified: 2022-08-07
tags: 
---
%%和[[Sources&Spaces卡片_标准模板]]相比，第一要素是我的想法和思考，第二要素是参考文献来源%%
up:: Kubernetes
x:: 兄弟主题 [[Sources&Spaces卡片_标准模板]] 

> [!INFO] 提示
>  提示内容

## 正文
DaemonSet 是一个 K8s 资源管理的概念。**DaemonSet** 确保全部（或者某些）节点上运行一个 Pod 的副本。 当有节点加入集群时， 也会为他们新增一个 Pod 。 当有节点从集群移除时，这些 Pod 也会被回收。删除 DaemonSet 将会删除它创建的所有 Pod。


---

## 参考文献
[DaemonSet | Kubernetes](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/daemonset/)