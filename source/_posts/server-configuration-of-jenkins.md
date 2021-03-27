---
title: 使用阿里云服务器配置Jenkins小记
date: 2018-09-22 16:23:13
tags: jenkins系列
---

![](/server-configuration-of-jenkins/jenkins3.jpg)

<!--more-->

## 一、Jenkins是什么<br>
   Jenkins是一个功能强大的应用程序，允许持续集成和持续交付项目，无论用的是什么平台。  
   这是一个免费的源代码，可以处理任何类型的构建或持续集成。集成Jenkins可以用于一些测试和部署技术。  
   
## 二、安装Jenkins的步骤<br>
  
  在安装Jenkins之前，我们需要配置好Jenkins的运行环境和一些参数变量。  
  所以从开始直至Jenkins安装完毕，这个过程可以分为三大步骤  
  1、安装对应版本的JDK
  2、安装对应版本的Tomcat
  3、安装Jenkins
  
  
  <h4>1、安装JDK</h4>
  首先查看服务器的信息，是64位还是32位系统的 命令： `uname -a` ，可以看到我的linux是64位的
  ![linux-version](/server-configuration-of-jenkins/linux_version.png)    <br>
  然后到官网下载对应版本的JDK，由上面的命令我们查到系统为64位的，所以我们就下载对应的linux64位版本的  
  ![linux64的JDK](/server-configuration-of-jenkins/jdk.png)<br>
  下载完毕，将目录切换到 usr，然后新建一个文件夹，我这里命名为 myJDK，然后进入 myJDK。  
  ` cd /usr  , mkdir myJDK ,cd myJDK `  
  然后用xftp（不懂xftp是什么的自己百度）把刚刚下好的压缩包复制过来，然后解压。  
  `tar -zxvf [filename] `  
  解压完毕，然后进行JDK的配置
  
  输入命令 ` vim /etc/profile `，进入配置文件界面（内置使用的是vim编辑器，不会用的去百度）  
  在文件最前面添加如下内容：
  ```
    export JAVA_HOME=/usr/myJDK/jdk1.8.0_161  
    export JRE_HOME=${JAVA_HOME}/jre  
    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib 
    export  PATH=${JAVA_HOME}/bin:$PATH  
  ```
  添加完成的时候再再输入命令 `source/etc/profile` 保存一下配置文件的修改  
  大致流程和在windows上配置差不多的，然后同样，检查一下安装是否成功  ``` java -version ```，提示如下说明成功  
  ![JDK配置成功输出的版本信息](/server-configuration-of-jenkins/jdk_sucess.png)  
  
  <h4>2、安装Tomcat </h4> 
  
  百度tomcat，来到官网找对应版本 
  这里我安装的是Tomcat8，选择这个版本
  ![选择tomcat版本](/server-configuration-of-jenkins/tomcat.png)<br>
  然后惯例，在 usr目录下新建一个文件夹,我直接命名为tomcat，把压缩包从本地复制到服务器的这个文件夹中，解压  
  新版tomcat是不用配置环境变量的，所以解压完毕就可以直接启动tomcat服务了。  
  运行如下命令  `cd [解压完成的tomcat目录]`,`cd bin`,运行启动文件 ` ./startup.sh`  
  然后会看到窗口中有这些信息输出，说明运行没有问题  
  ![运行startup文件的提示信息](/server-configuration-of-jenkins/tomcat_start.png)  
  可能需要稍等一下，tomcat启动还是需要点时间的。  
  访问一下，公网地址：8080(tomcat默认端口)，出现如下界面表示成功  
  ![运行tomcat成功的界面](/server-configuration-of-jenkins/tomcat_startup.png)
  
  <h4>3、安装Jenkins</h4>
  百度Jenkins，到官网直接下载  
  usr目录下新建 jenkins文件夹，复制文件到该文件夹内，解压，具体操作如上不啰嗦  
  然后进入解压目录，输入命令 `java -jar jenkin.war`。  
  等待jenkins运行的信息显示完毕，浏览器输入 公网地址：8080,看到以下页面表示安装成功。 
  ![jenkins主界面](/server-configuration-of-jenkins/jenkins_start.png)
  安装成功后，Jenkins还需要在页面上进行一系列的配置，篇幅有限，就不说了。 

  
<h2> 三、我遇到的坑</h2> 
<h4>tomcat无法启动</h4>
    首先是输入8080端口无效，网页显示连接失败。然后查看了一下端口占用情况，以为是端口问题，然后修改了端口号为8081，再次进行启动，发现没有用。  
	然后百度了一下，说是需要到阿里云控制台配置防火墙端口，如下
	![阿里云轻量服务器防火墙端口号配置](/server-configuration-of-jenkins/ali_firewall.png)
	然后再次访问就OK了。  
	吐槽一下，这个问题困扰了我起码有3天！！！
