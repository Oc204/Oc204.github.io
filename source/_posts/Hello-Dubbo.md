---
title: Hello Dubbo
date: 2019-10-29 10:29:35
tags: dubbo
---

![](/Hello-Dubbo/architecture.png)

<!--more-->

本文主要从一个hello-world工程来演示Dubbo的使用方法，进而更加好地理解Dubbo的作用与使用Dubbo做服务的理由。

### 引入Dubbo理念

在说Dubbo之前，不得先说一下RPC这个词

```
What`s RPC
RPC【Remote Procedure Call】是指远程过程调用，是一种进程间通信方式，他是一种技术的思想，而不是规范。它允许程序调用另一个地址空间（通常是共享网络的另一台机器上）的过程或函数，而不用程序员显式编码这个远程调用的细节。即程序员无论是调用本地的还是远程的函数，本质上编写的调用代码基本相同。
```

Dubbo跟RPC有什么关系？

Dubbo是一款JAVA RPC框架，也就是说，Dubbo提供了更好的远程调用服务，它是为RPC服务而生。

然后为什么我们要去使用这么多乱七八糟的框架？因为这些框架里面提供了很多很完善的服务，一些常见的错误修补机制，省去了大量的时间和人力，避免我们总是重复造轮子，而且造轮子的难度也很大，当然一般自己造出来的轮子都比较烂。因此，我们有必要去学习使用这些框架。

Dubbo中的关键词

首先，我们先明确几个关键词，暂且先记住他的意义。

**服务提供者（Provider）**：暴露服务的服务提供方，服务提供者在启动时，向注册中心注册自己提供的服务。

**服务消费者（Consumer）**: 调用远程服务的服务消费方，服务消费者在启动时，向注册中心订阅自己所需的服务，服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。

**注册中心（Registry）**：注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者

**监控中心（Monitor）**：服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心

- 调用关系说明

　　　　l  服务容器负责启动，加载，运行服务提供者。

　　　　l  服务提供者在启动时，向注册中心注册自己提供的服务。

　　　　l  服务消费者在启动时，向注册中心订阅自己所需的服务。

　　　　l  注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。

　　　　l  服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。

　　　　l  服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

## 开始一个完整的Dubbo工程

首先本地先安装好zookeeper，zookeeper作为Dubbo的注册中心，是必不可少的一个组件，安装过程根据自己本地环境安装对应版本。

### 0X01 创建基础父类工程

此处创建maven工程，创建方法参考文章[eclipse创建父子工程](https://blog.csdn.net/NancyWu_LuckyGirl/article/details/80235592)

子工程依赖父工程的pom，这里我们创建的父工程名为dubbo-boot，首先看一下pom文件的配置和内容

![](/Hello-Dubbo/p1.png)



```
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>learn.dubbo</groupId>
	<artifactId>dubbo-boot</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>pom</packaging>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.4.RELEASE</version>
		<relativePath />
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
		<project.version>1.0</project.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!-- dubbo -->
		<dependency>
			<groupId>com.alibaba.boot</groupId>
			<artifactId>dubbo-spring-boot-starter</artifactId>
			<version>0.2.0</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/com.alibaba.boot/dubbo-spring-boot-starter -->

		<!-- zookeeper -->
		<dependency>
			<groupId>org.apache.zookeeper</groupId>
			<artifactId>zookeeper</artifactId>
			<version>3.4.8</version>
		</dependency>
		<dependency>
			<groupId>com.101tec</groupId>
			<artifactId>zkclient</artifactId>
			<version>0.10</version>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>${java.version}</source>
					<target>${java.version}</target>
					<encoding>${project.build.sourceEncoding}</encoding>
				</configuration>
			</plugin>
		</plugins>
	</build>

</project>
```

父工程结束

### 0X02 搭建服务提供接口

创建一个名称为common-api的工程，工程只依赖于父工程，所以配置很简单，pom内容如下

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>learn.dubbo</groupId>
    <artifactId>dubbo-boot</artifactId>
    <version>0.0.1-SNAPSHOT</version>
  </parent>
  <artifactId>common-api</artifactId>
</project>
```

这个工程里面，我们只写一个接口，命名为HelloService

```
package learn.dubbo.common.api;

public interface HelloService {

	public String hello(String message);
}

```

### 0X03搭建服务提供者

子工程同样依赖于父工程，下面贴出代码及工程结构

![](/Hello-Dubbo/p2.png)

首先是pom

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>learn.dubbo</groupId>
		<artifactId>dubbo-boot</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>server-provider</artifactId>
	<dependencies>
		<!--依赖的接口提供方，将会通过jar包方式将api接口自动打包到本工程中-->
		<dependency>
			<groupId>learn.dubbo</groupId>
			<artifactId>common-api</artifactId>
			<version>${project.version}</version>
		</dependency>
	</dependencies>
</project>
```

编写启动类

```
package learn.dubbo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import com.alibaba.dubbo.config.spring.context.annotation.EnableDubbo;

@EnableDubbo //此注解表示开启Dubbo功能
@SpringBootApplication
public class ProviderApplication {

	public static void main(String[] args) {
		SpringApplication.run(ProviderApplication.class, args);
		System.out.println("Provider complete");
	}
}

```

application.yml配置类

```
server:
  port: 8080
dubbo:
  application:
    name: server-provider # 服务名称，保持唯一
  registry:
    address: zookeeper://127.0.0.1:2181  #zk地址，向其注册服务
    #暴露服务方式，分别为dubbo端口和协议，协议写法是固定的
  protocol:
    port: 20880
    name: dubbo
    #启动监控，协议启动后注册中心发现监控中心地址，否则直连监控中心。
  monitor:
    protocol: registry
```

接口实现HelloServiceImpl

```
package learn.dubbo.server.provider;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import com.alibaba.dubbo.config.annotation.Service;

import learn.dubbo.common.api.HelloService;

@Service(interfaceClass = HelloService.class)//指要发布的服务的接口，也就是暴露服务接口
@Component
public class HelloServiceImpl implements HelloService {

	@Override	
	public String hello(String message) {
		return "hello ," + message;
	}
}
```

### 0X03编写消费者

再搭建一个子类工程，同样依赖于父类

![](/Hello-Dubbo/p3.png)

pom内容如下

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>learn.dubbo</groupId>
		<artifactId>dubbo-boot</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>server-consumer</artifactId>
	<dependencies>
		<!--引入api依赖-->
		<dependency>
			<groupId>learn.dubbo</groupId>
			<artifactId>common-api</artifactId>
			<version>${project.version}</version>
		</dependency>
	</dependencies>
</project>
```

启动类

```
package learn.dubbo.consumer;

import org.springframework.boot.autoconfigure.SpringBootApplication;

import com.alibaba.dubbo.config.spring.context.annotation.EnableDubbo;

import org.springframework.boot.SpringApplication;

@EnableDubbo //开启dubbo服务
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```

application.yml配置

```
server:
  port: 8082
dubbo:
  application:
    # 服务名称，保持唯一
    name: server-consumer
    # zookeeper地址，用于从中获取注册的服务
  registry:
    address: zookeeper://127.0.0.1:2181
  protocol:
    # dubbo协议，固定写法
    name: dubbo
  monitor:
    protocol: registry
```

实现一个Controller，用来演示服务的消费

```
package learn.dubbo.consumer.controller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

import com.alibaba.dubbo.config.annotation.Reference;

import learn.dubbo.common.api.HelloService;

@RestController
public class HelloController {

	private final static Logger logger = LoggerFactory.getLogger(HelloController.class);
	
	@Reference(check=false) //关闭服务检查，因为服务不存在时会报错，可能会出现NULL
	private HelloService helloServie;
	
	@GetMapping("/hello")
	public String hello(@RequestParam String message) {
				
		logger.info("get message : "+message + " helloService " + helloServie); 		
		if(helloServie!=null) {
			return this.helloServie.hello(message);
		}		
		return "helloService Exception , helloService NULL ";
	}
}
```

### 0X04配置Dubbo服务监控

参考文章[Dubbo服务监控搭建](<https://mrbird.cc/dubbo-mointor.html>)

### 0X05启动服务，查看运行结果与总结

浏览器输入localhost:8082?message=123，出现页面返回如下

![](/Hello-Dubbo/p4.png)

然后我们看监控中心,好了，服务已经注册上了，而且服务也已经被消费者消费

![](/Hello-Dubbo/p5.png)

然后点击进去，可以查看到服务提供者下面目前有一个消费者节点

![](/Hello-Dubbo/p6.png)

然后再看Dubbo admin监控中心

![](/Hello-Dubbo/p7.png)

服务和消费都已经被注册。

对于初学者来说，可能还是云里雾里，这时候就需要多查资料，多解决问题，或者慢慢提升项目的难度，通过不断的上手来巩固概念及提升实际操作的能力。

本文完毕