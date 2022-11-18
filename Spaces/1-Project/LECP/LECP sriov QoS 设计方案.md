---
title: 代码分析
date created: 2022-11-18
date modified: 2022-11-18
---

通过参考 VPN 和 floatingip 都是通过 CRD 监控 Pod 的创建过程从而达到一种修改的目的，所以我们的设计应该参考这种方式，建立 CRD，并且建立一个 [[DaemonSet]] 来完成系统的监控。

# 代码分析

mlx/sriovnet

| 函数名                 | 目录地址                                 | 过程                                                    |
| ---------------------- | ---------------------------------------- | ------------------------------------------------------- |
| GetVfIndexByPciAddress | /sys/bus/pci/devices/vfpci/physfn/virtfn | 获取目录下的所有virtfn；根据 PCI 号，查找 vfindex |
| GetPfPciFromVfPci      | /sys/bus/pci/devices/vfpci/physfn        | 读取 pf 所在的目录；得到 pf 的 uplink            |


## 如何设置 sriov 的带宽？

- 将注解写到 VM，然后将注解传给 pod，当 pod 启动并且，分配到 vf 以后，返回PCI号
- 利用pci号，查找pci号对应的 pf
- 监控 Pod 建立的过程，当发现带有 sriov 的网卡的新建，则调用 netlink 设置 vf 的带宽

## 如何查找 PCI 号？

### 方案一

虚拟机建立后，使用的 PCI 号，可以在目录下找到  
/var/run/k8s.cni.cncf.io/devinfo/cni/admin-srv-e061661af3732d3722604b84a196956ad759f02ae00cda41a639ff6aff85a276_net1  
namespace+net名+containerID+ifname

- 这个文件是 multus-cni 建立的，内容就是 device-info 部分
- container ID 实际上并不是容器本身的 ID，而是容器的sandbox 的 ID，所以查找 PCI 号，要查到对应的容器id，然后使用 crictl 查找 sandbox id

### 方案二

通过读取 Pod 注解，在有 sriov 网卡时，注解中包含了 pci 参数

``` yaml
k8s.v1.cni.cncf.io/network-status:
{[
     "name": admin/srv
     "interface": net1
     "dns":{}
     "device-info": {
       "type": pci
       "version":
       "pci": {
         "pci-address": "0000:58:0a.1"
       }
]}
```
