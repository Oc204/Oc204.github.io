---
title: MySQL server has gone away错误的解决办法
date: 2019-12-27 22:59:37
tags:
---






在使用mysql导入大文件的时候会出现**MySQL server has gone away**的错误，原因是mysql中max_allowed_packet配置的默认值太小了。max_allowed_packet值的作用是限制mysql服务端接收到的包的大小。

1、登入mysql

	mysql -u root -p

2、查看max_allowed_packet的值

    show global variables like 'max_allowed_packet'
    +--------------------+---------+
    | Variable_name      | Value   |
    +--------------------+---------+
    | max_allowed_packet | 4194304 |
    +--------------------+---------+
现在可以看到默认情况下max_allowed_packet的值只有4M，接下来将max_allowed_packet的值设置为150M(1024 x 1024 x 150)
    set global max_allowed_packet=157286400;
然后就能够执行大文件了

3、使命令永久生效
**在命令行中设置只能对当前设置有效，重启mysql服务后便会又恢复默认值。**所以，可以通过修改配置文件中的参数，以此来达到永久生效的效果，修改文件为my.cnf中添加参数 max_allowed_packet=150M即可