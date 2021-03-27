---
title: 一次线上k8s集群故障记录
date: 2021-01-10 21:04:19
tags:
---

![](/一次线上k8s集群故障记录/head.jpg)

<!--more-->

最近k8s master节点频繁异常，表现为k8s darshboard无法访问，应用node、pod运行状态均正常，但是无法通过ingress配置的子域名访问应用，比较奇怪。

### 1 排查是否是ingress异常
查看ingress pod的状态和运行日志，均表现正常，排除ingress问题

### 2 排查其他系统pod和service
执行```kubectl get pods -A ```，主要观察系统pod。目前是一个集群4个节点，查看发现其中master节点的flannel pod 处于termina状态，所以这就是ingress子域名访问不了应用的原因。
主节点的网络组件异常，导致应用无法和外部网络连接

### 3 查看flannel状态
执行```kubectl describe pods``` 命令，信息提示 low resources，组件得不到足够的资源无法启动

### 4 查看master节点的kubelet状态
通过```systemctl status kubelet``` 以及 [journalctl 命令](https://www.cnblogs.com/effortsing/p/10307830.html)查看到k8s运行输出的日志，发现一直在报错，提示 ImageGC 异常，应用释放失败，需要强制执行（--force）才能释放需要被回收的镜像资源。

### 5 查看docker镜像信息
执行docker images\docker ps ，发现存在一些 tag、name均为<none>的镜像，执行docker rmi删除镜像时，提示镜像被已经停止的容器引用，此时也解释了为什么k8s ImageGC异常的问题。
使用命令 docker rmi --force强制删除为<none>的镜像，再查看k8s日志，已经不报错了，再访问darshboard也能正常访问了。这一篇文章解释了<none>镜像出现的原因[Docker镜像列表中的none:none是什么](https://blog.csdn.net/boling_cavalry/article/details/90727359)

### 6 排查low resource的原因
首先查看磁盘占用，目前磁盘占用为80%多，结合最近有些应用在重新发布时会出现**DiskPressure**的情况，在网上看了一下发现当系统磁盘占用超过80%时K8s就会出现**DiskPressure**的警告，
因为现在应用的几乎是成倍增长，虽然之前也已经扩容了一次，但是磁盘资源相对来说还是比较紧缺。所以只要一直发布新应用，磁盘迟早也会被占满，虽然说k8s是有GC机制。

### 7 k8s ImageGC 周期
查看文档发现，k8s ImageGC是五分钟对image执行一次，它会对哪些已经不被使用的资源进行回收。所以也解释了为什么应用刚刚能正常访问一下就又崩了的问题

### 8 最终解决方案
因为机器上安装了一些其他的软件应用，也是比较占用硬盘资源，所以把Jenkins的数据目录迁移到了新的数据盘上，然后一下子就腾出了50%的系统盘空间，所以至少在半年以内是不会再出现
**low resource** 和 **DiskPressure**的问题了。问题虽然解决了，但还是治标不治本。

### 问题分析
1、构建环境和应用运行环境都在一台机器中进行，硬盘空间容易用得快，硬盘一旦使用率过高就容易出现各种奇葩问题（硬盘不贵，能用钱解决的就不要瞎折腾了。。。）
2、应用在发布新镜像时做好版本号标签更新，不然就会出现<none>的镜像，<none>镜像是需要强制删除才能删掉
3、根据服务器实际情况，修改k8s相关配置，比如磁盘最大允许使用多少等

下一节说说 jenkins怎么优化和迁移