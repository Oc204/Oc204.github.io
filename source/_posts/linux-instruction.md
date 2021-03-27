---
title: linux 命令小记
date: 2018-09-22 20:43:41
tags: 学习小记
---

![](/linux-instruction/linux.png)

<!--more-->

## 本文背景
  ><font face="楷体" size = 4>
    前几天买了个阿里的学生机，之前也完全没有接触过服务器，然后就完全是个小白这样。
    但是经过几天的折腾，也渐渐熟悉了以命令行形式的操作。感觉还挺有意思的，也没有想象中这么难
  </font>
---

<h3>linux基本命令</h3> 

    cd [filename]          //进入某一文件夹
    cd ..                  //返回上一层文件
    cd ~                   //返回根目录
    mkdir [filename]       //创建名为 ‘filename‘的文件夹
    tar -zxvf [filename]   //解压后缀为.tar.gz的压缩包
    ls -la                 //查看当前目录下的所有文件
    rz                     //从本地上传文件至当前文件夹
    rm -rf [filename]      //删除 'filename'文件及其所有子文件
    ps -ef | grep [应用名]  //查看某一应用的进程状态
    nohup java -jar jenkins.war --httpPort=9000 //nohup命令表示应用将转入后台运行
    find / -iname [应用关键词,如 mysql] //查找所有带该关键词的相关文件
    find / iname [应用关键词,如 mysql] | xargs -n 1000 rm -rf //把所有查找到的相关结果删除，rm每次接受参数有限，所有1000表示每次传1000个参数给它

<br><h3>linux中.sh文件提示权限不够和无效问题</h3>

	```
step_01: vi filename.sh 进入文件编辑模式
step_02: 进入编辑模式输入命令 set ff 查看.sh文件的格式，如果出现文件格式为dos则需要修格式，输入 set fileformat=unix
         因为linux系统下只认unix格式的.sh。修改完毕，保存返回。以此可再次查看格式是否修改成功
step_03: 在.sh文件的父目录下执行命令 chmod u+x filename.sh 给予.sh文件权限
	```

### 坑比MYSQL

#### 修改密码

进入mysql命令行界面



#### 允许任何root用户远程访问数据库

1、登录mysql mysql -u root -p，输入密码进入mysql命令界面

2、在mysql命令界面执行 

```
mysql>grant all privileges on *.* to root@'%' identified by 'A123456';
mysql> flush privileges;

//表示允许任何远程为root的用户名，密码为A123456的用户登录本数据库，flush命令表示刷新,重启数据库也行
```

由于目前使用的是mariadb，因为mysql出了问题，目前的水平还无法解决，又不想再重装服务器了，所以就。。

然后之前使用的mysql server的命令，都需要改成mariadb，（T--T）





<h3>vim 的基本命令</h3>

   `vim [filename]        //对可编辑文件'filename'进行编辑`

   进入vim编辑模式后，输入i进入编辑模式，左下方会有 ‘INSERT’ 关键字提示;
   编辑完成后，按 'esc' 退出编辑模式，然后输入 ':' 进入保存选项，输入命令: 
   ```
     q!                //表示不保存修改并提交
	   wq                 //表示保存修稿并提交
   ```
   <br>
<h4>在今后的学习中将会继续完善...</h4>

  