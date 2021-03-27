---
title: Hello Docker!(Based on Linux)
date: 2019-10-19 16:45:55
tags:
---

![](/Hello-Docker-Based-on-Linux/head.jpg)

<!--more-->

## Docker是什么？

百度上是这样解释的：Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中,然后发布到任何流行的[Linux](https://baike.baidu.com/item/Linux)机器上,也可以实现虚拟化,容器是完全使用沙箱机制,相互之间不会有任何接口。

以我目前的理解，我觉得用”寄生虫“来形容Docker更为合适。那什么是宿主机？宿主机就是我们一台普通的机器，比如一台阿里云服务器，一台个人电脑，就可以称为一个宿主机。也正是因为宿主机这个概念，Docker依赖于宿主机建立，但Docker的内部的容器却又是完全对外隔离的，也正是一个“沙箱”的概念。而且一台宿主机可以部署很多个Docker，因此可以有很多不同的容器，也就是很多个“寄生虫”，因此，就实现了完全虚拟化。

## 如何使用Docker？

建议在Linux环境下使用Docker,Windows环境不展开描述。此处将从零开始演示如何通过jenkins+git+docker集成一个java的maven项目。

### 1安装Docker

移除旧的版本：
```
$ sudo yum remove docker
```

安装一些必要的系统工具：

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

添加软件源信息：

```
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

更新 yum 缓存：
```
sudo yum makecache fast
```

安装 Docker-ce：

```
sudo yum -y install docker-ce
```

启动 Docker 后台服务

```
sudo systemctl start docker
```

测试运行 hello-world

```
[root@runoob ~]# docker run hello-world
```

### 2.Docker安装JDK

docker安装jdk很简单，直接运行命令:docker pull java:8    //表示安装jdk1.8

安装其他镜像也一样，docker命令安装镜像如果不指定版本号的话，将默认安装最新版



### 3.使用jenkins打包github项目并发布成docker镜像

#### 3.1首先编写一个HelloWorld的springboot项目，项目内包含Dockerfile

什么是Dockerfile？Dockerfile是Docker的一个特殊的脚本工具，通过在这个文本文档内编写docker命令，然后使用Docker读取运行该文本，就可以实现构建镜像的操作。

目前展示我的一个简单的Dockerfile,并做一些简单的解释

```
FROM java:8  //指的是关联jdk1.8的镜像
VOLUME /tmp   //将tmp目录并持久化到Docker数据文件夹，因为Spring Boot使用的内嵌Tomcat容器默认使用/tmp作为工作目录
ADD docker-test-0.0.1-SNAPSHOT.jar docker-test.jar //将项目添加为docker-test.jar,项目名称一定要与pom中的名称一致，或者可以观察打包完成后的包名
RUN sh -c 'touch /docker-test.jar' //拷贝jar到容器当中
ENV JAVA_OPTS=""    //指定jvm参数
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/docker-test.jar"]
//ENTRYPOINT 执行项目 app.jar。为了缩短 Tomcat 启动时间，添加一个系统属性指向 “/dev/urandom” 作为 Entropy Source 
```

#### 3.2使用jenkins集成构建

jenkins主要说一下触发器和编写脚本这里。

首先触发器先看下面的配置

![](/Hello-Docker-Based-on-Linux/p1.jpg)

选择自动触发构建，下面表示轮询的时间，每隔5分钟查询一次是否有新的提交，如果有新的提交则自动构建。

然后是启动过程中的脚本

![](/Hello-Docker-Based-on-Linux/p2.jpg)

```
##判断mytestdemo中是否已经存在jar
if [  -f "/usr/local/mytestdemo/docker-test-0.0.1-SNAPSHOT.jar" ];then
rm -rf /usr/local/mytestdemo/docker-test-0.0.1-SNAPSHOT.jar
else
echo "文件不存在"
fi

if [ -f "/usr/local/mytestdemo/Dockerfile" ];then
rm -rf /usr/local/mytestdemo/Dockerfile
fi

##移动jar到mytestdemo
cp -r -f ${WORKSPACE}/docker-test/target/docker-test-0.0.1-SNAPSHOT.jar /usr/local/mytestdemo
cp -r -f ${WORKSPACE}/docker-test/src/main/resources/Dockerfile /usr/local/mytestdemo
##判断docker中是否存在运行容器和镜像
imagesid=`docker images|grep -i docker-test|awk '{print $3}'`
if [ -n "$imagesid" ];then
##docker stop $imagesid
docker rmi -f docker-test
else
echo "镜像不存在"
fi

##启动镜像
cd /usr/local/mytestdemo
docker build -t docker-test . 
docker run -d -p 8081:8082 docker-test
echo "镜像启动完毕"
```

脚本编写还不熟练，所以写的也比较冗余，因此只能从比较基础的思路编写，熟练脚本的的话可以考虑更加高级一点的写法。**其中，jenkins构建的项目空间不指定的话，它会将当前项目构建到默认的workspace目录下，最好还是自己指定一下构建目录。**

至此，jenkins部分的工作完毕。然后我们只要修改项目的代码，提交之后，代码库发生变更，jenkins便会自动构建，省去了每一次提交都要重复所有步骤的麻烦。构建成功之后，可以到服务器上面查看docker中镜像的运行状态。