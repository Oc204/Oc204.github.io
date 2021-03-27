---
title: spring boot learning小记
date: 2019-03-20 14:07:44
tags: spring boot
---

## 简介
  简单记录了一下用spring boot 和 mybatis搭建的一个小demo，下面贴出工程结构及对应代码 

---
<!--more-->
## 一、工程结构
![](/spring-boot-learning/p_struture.png)
基本的mvc结构

## 二、详细代码

<h4>1)entity</h4>
```php
public class User {

	private int id;
	private String username;
	private String pwd;

	//省略toString 和 get set方法

}

```
<h4>2)mapper</h4>
```php
@Repository
public interface UserMapper {

	User Sel(int id) ;   //定义查询接口
}
```
<h4>3)service</h4>
```php
@Service
public class UserService {

	@Autowired
	UserMapper userMapper;
	
	public User Sel(int id) {   //mybatis查询
		return userMapper.Sel(id) ;
	}
}
```
<h4>4)controller</h4>
```php
@RestController
@RequestMapping("/testBoot")  //页面映射，用户访问的URI
public class UserController {

	@Autowired
	private UserService userService ;
	
	@RequestMapping("getUser/{id}")    //处理页面请求，也可以直接返回页面
	public String GetUser(@PathVariable int id) {
		return userService.Sel(id).toString() ;
	}
}
```
<h4>5)启动类</h4>
```php
@MapperScan("com.learn.mapper") //必须指定的mapper包路径，否则接口类无效
@SpringBootApplication
public class App 
{
    public static void main( String[] args )
    {
        SpringApplication.run(App.class, args) ;
    }
}

```

## 三、配置文件
<h4>1)pom.xml</h4>
```php
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>ocj.study</groupId>
	<artifactId>boot_learn</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>boot_learn</name>
	<url>http://maven.apache.org</url>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.1.RELEASE</version>
	</parent>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>

	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<!--<scope>provided</scope>-->
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jdbc</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional> <!-- 这个需要为 true 热部署才有效 -->
			<scope>runtime</scope>
		</dependency>
		<!-- mybatis -->
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.1.1</version>
		</dependency>
		<!-- mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
	</dependencies>
</project>

```
<h4>2)yml文件</h4>
application.yml主要用于指定其他yml配置文件，方便配置文件的分类
```php
spring: 
  profiles:
    active: dev
```
application-dev.yml
```php
server:
  port: 8080
  
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=UTF-8
    driver-class-name: com.mysql.jdbc.Driver
    
mybatis:
  mapper-locations: classpath:mapping/*.xml
  type-aliases-package: com.learn.entity
```

<h4>1)UserMapping.xml</h4>
mybatis映射文件
```php
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.learn.mapper.UserMapper">
 
    <resultMap id="BaseResultMap" type="com.learn.entity.User">
        <result column="id" jdbcType="INTEGER" property="id" />
        <result column="username" jdbcType="VARCHAR" property="username" />
        <result column="pwd" jdbcType="VARCHAR" property="pwd" />
    </resultMap>
 
    <select id="Sel" resultType="com.learn.entity.User">
        select * from userinfo where id = #{id}
    </select>
 
</mapper>

```

运行结果
![](/spring-boot-learning/result.png)
查询匹配的id直接返回显示在页面上


## 四、小结

   初步理解springboot微服务概念，最直接的理解感觉就是整个项目可以打包成jar/war包格式运行
   利用springboot框架，可以继续使用多种插件进行集成开发。
   本项目中直接利用浏览器页面url添加参数，没有另外写html页面，不过思路大同小异。之前有做过ajax级联操作，应该是可以直接放上去用。
   
   项目难度比较低，但是可以以此为基础进一步集成其他功能。