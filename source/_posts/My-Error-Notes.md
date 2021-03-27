---
title: My Error Notes
date: 2019-10-25 16:59:34
tags:
---



### SpringBoot常见错误

<!--more-->

1.典型错误

maven报错

pom not exist

运行时出现

```
No qualifying bean of type 'com.example.demo.service.MailService' available: expected at least 1 bean which qualifies as autowire candidate.
```

##原因

被调用的class没有加上@Component注解，无法将bean实例化

看一下@Component注解的作用就知道了

```

```



