---
title: Jenkins迁移
date: 2021-01-20 22:37:31
tags:
---

![](/Jenkins迁移/head.jpg)

<!--more-->

## 背景
随着业务系统的不断增长，Jenkins项目也越来越多，导致一个Jenkins的workspace和一些其他配置的磁盘占用变得非常恐怖，期间也尝试过构建时清理工作空间的方案，后面发现一些结构较为复杂的项目在重新构建时会花很长时间，同时也为了减小系统盘的压力，故采用迁移这一方案。

## Jenkins的一些特殊的文件夹

Jenkins有一个叫做Jenkins Home的配置变量，Jenkins Home是指定Jenkins存储所有数据的地方，包括Jenkins配置、账号密码、工作空间之类的。

## 如何迁移

根据Jenkins Home的特点，因此无论迁移到哪一台机器上，只要将原Jenkins Home的目录打包迁移过去就行了，非常简单。这里操作如下：
-  将原数据进行压缩打包，然后放到数据盘中，
- 修改Jenkins Home的指向路径，指向数据盘中给定的新目录路径
- 重启Jenkins
- 登录web端Jenkins，查看全局配置中Jenkins Home的路径是否符合预期
- 删除原Jenkins Home目录下的数据

