---
title: 异常关闭虚拟机导致k8s故障和docker无法启动
date: 2021-03-29 12:06:49
tags: k8s
---

<!-- ![](/异常关闭虚拟机导致k8s故障和docker无法启动/head.jpg) -->

<!-- <center> -->
<img src="/异常关闭虚拟机导致k8s故障和docker无法启动/head.jpg" width="70%" height="50%"/>
    
<!-- </center> -->

<!--more-->

------



背景：

某一天又又又因为机器连续待机过久（开着三台16G的虚拟机在运行服务，中高负载），导致无法无法正常解除待机使用，怎么按键盘屏幕都没有反应，不得已情况下只能强行关机重启。但重启之后便发现k8s无法启动，其中一台甚至连docker都启动不了。



docker无法启动排查：

docker ps

```
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```



systemctl status docker:

![](D:\个人总结\hexo-blog\hexo\source\_posts\异常关闭虚拟机导致k8s故障和docker无法启动\docker-01.png)

发现得不到什么有用的信息，没有报错详情

查看系统级日志信息 journalctl -u docker ,发现输出信息跟上面的信息一样



尝试systemctl restart docker

```
Job for docker.service canceled.
```



再次查看journalctl -u docker 

![](/异常关闭虚拟机导致k8s故障和docker无法启动/docker-03.png)



发现应该是容器的问题，结合之前线上的问题来看，docker如果异常关闭的话总会导致一些奇怪又严重的问题出现，所以以后还是好好关机。。。

首先备份原docker容器数据，centos7默认docker安装路径为 /var/lib/docker：

```
mv /var/lib/docker /var/lib/docker-bak
mv /var/lib/containerd /var/lib/containerd-bak
```

systemctl restart docker

systemctl status docker 

![](D:\个人总结\hexo-blog\hexo\source\_posts\异常关闭虚拟机导致k8s故障和docker无法启动\docker-04.png)

启动ok 了，然后结合之前的日志来看，这些容器基本坏掉了，暂时没有办法救回来了，所以这个时候集群负载均衡容灾的重要性就体现出来了，所以说k8s真是个好东西。



