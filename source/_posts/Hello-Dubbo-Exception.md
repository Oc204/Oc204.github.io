---
title: Hello Dubbo Exception
date: 2019-10-29 10:30:02
tags: dubbo
---

<h2> 这是一个非常坑的BUG,Dubbo消费服务一直报空指针</h2>

<!--more-->

首先说一下这个Bug，下面是控制台的报错信息，分别有两个报错

这个错误是@Reference没有添加任何参数的时候,报错显示service 实例没有初始化

```
2019-10-29 10:04:58.582 ERROR 8520 --- [nio-8082-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is com.alibaba.dubbo.rpc.RpcException: Failed to invoke remote method: hello, provider: dubbo://10.14.253.30:20880/learn.dubbo.common.api.HelloService?application=server-consumer&check=false&dubbo=2.6.2&interface=learn.dubbo.common.api.HelloService&methods=hello&pid=8520&register.ip=10.14.253.30&side=consumer&timestamp=1572314668328, cause: message can not send, because channel is closed . url:dubbo://10.14.253.30:20880/learn.dubbo.common.api.HelloService?application=server-consumer&check=false&codec=dubbo&dubbo=2.6.2&heartbeat=60000&interface=learn.dubbo.common.api.HelloService&methods=hello&pid=8520&register.ip=10.14.253.30&side=consumer&timestamp=1572314668328] with root cause

com.alibaba.dubbo.remoting.RemotingException: message can not send, because channel is closed . url:dubbo://10.14.253.30:20880/learn.dubbo.common.api.HelloService?application=server-consumer&check=false&codec=dubbo&dubbo=2.6.2&heartbeat=60000&interface=learn.dubbo.common.api.HelloService&methods=hello&pid=8520&register.ip=10.14.253.30&side=consumer&timestamp=1572314668328
	at com.alibaba.dubbo.remoting.transport.AbstractClient.send(AbstractClient.java:263) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.remoting.transport.AbstractPeer.send(AbstractPeer.java:53) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.remoting.exchange.support.header.HeaderExchangeChannel.request(HeaderExchangeChannel.java:115) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.remoting.exchange.support.header.HeaderExchangeClient.request(HeaderExchangeClient.java:90) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.dubbo.ReferenceCountExchangeClient.request(ReferenceCountExchangeClient.java:83) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.dubbo.DubboInvoker.doInvoke(DubboInvoker.java:95) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.AbstractInvoker.invoke(AbstractInvoker.java:148) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.listener.ListenerInvokerWrapper.invoke(ListenerInvokerWrapper.java:77) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.monitor.support.MonitorFilter.invoke(MonitorFilter.java:75) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.ProtocolFilterWrapper$1.invoke(ProtocolFilterWrapper.java:72) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.dubbo.filter.FutureFilter.invoke(FutureFilter.java:54) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.ProtocolFilterWrapper$1.invoke(ProtocolFilterWrapper.java:72) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.filter.ConsumerContextFilter.invoke(ConsumerContextFilter.java:48) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.ProtocolFilterWrapper$1.invoke(ProtocolFilterWrapper.java:72) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.proxy.InvokerInvocationHandler.invoke(InvokerInvocationHandler.java:52) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.common.bytecode.proxy0.hello(proxy0.java) ~[dubbo-2.6.2.jar:2.6.2]
	at learn.dubbo.consumer.controller.HelloController.hello(HelloController.java:29) ~[classes/:na]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_144]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_144]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_144]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_144]
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:209) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:136) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:102) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:877) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:783) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:991) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:925) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:974) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:866) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:635) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:851) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:742) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52) ~[tomcat-embed-websocket-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:99) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.filter.HttpPutFormContentFilter.doFilterInternal(HttpPutFormContentFilter.java:109) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.filter.HiddenHttpMethodFilter.doFilterInternal(HiddenHttpMethodFilter.java:93) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:200) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:198) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:493) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:140) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:81) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:87) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:342) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:800) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:800) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1471) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [na:1.8.0_144]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [na:1.8.0_144]
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at java.lang.Thread.run(Thread.java:748) [na:1.8.0_144]
```



这个错误是在Controller的@Reference注解添加参数(check=false)后出现，大概意思就是通道不存在或者已关闭

```
com.alibaba.dubbo.remoting.RemotingException: message can not send, because channel is closed . url:dubbo://10.14.253.30:20880/cc.mrbird.common.api.HelloService?anyhost=true&application=server-consumer&check=false&codec=dubbo&dubbo=2.6.2&generic=false&heartbeat=60000&interface=cc.mrbird.common.api.HelloService&methods=hello&pid=4968&register.ip=10.14.253.30&remote.timestamp=1572315119101&side=consumer&timestamp=1572321792912
	at com.alibaba.dubbo.remoting.transport.AbstractClient.send(AbstractClient.java:263) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.remoting.transport.AbstractPeer.send(AbstractPeer.java:53) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.remoting.exchange.support.header.HeaderExchangeChannel.request(HeaderExchangeChannel.java:115) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.remoting.exchange.support.header.HeaderExchangeClient.request(HeaderExchangeClient.java:90) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.dubbo.ReferenceCountExchangeClient.request(ReferenceCountExchangeClient.java:83) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.dubbo.DubboInvoker.doInvoke(DubboInvoker.java:95) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.AbstractInvoker.invoke(AbstractInvoker.java:148) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.monitor.support.MonitorFilter.invoke(MonitorFilter.java:75) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.ProtocolFilterWrapper$1.invoke(ProtocolFilterWrapper.java:72) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.dubbo.filter.FutureFilter.invoke(FutureFilter.java:54) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.ProtocolFilterWrapper$1.invoke(ProtocolFilterWrapper.java:72) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.filter.ConsumerContextFilter.invoke(ConsumerContextFilter.java:48) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.ProtocolFilterWrapper$1.invoke(ProtocolFilterWrapper.java:72) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.listener.ListenerInvokerWrapper.invoke(ListenerInvokerWrapper.java:77) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.protocol.InvokerWrapper.invoke(InvokerWrapper.java:56) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.cluster.support.FailoverClusterInvoker.doInvoke(FailoverClusterInvoker.java:78) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.cluster.support.AbstractClusterInvoker.invoke(AbstractClusterInvoker.java:238) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.cluster.support.wrapper.MockClusterInvoker.invoke(MockClusterInvoker.java:75) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.rpc.proxy.InvokerInvocationHandler.invoke(InvokerInvocationHandler.java:52) ~[dubbo-2.6.2.jar:2.6.2]
	at com.alibaba.dubbo.common.bytecode.proxy0.hello(proxy0.java) ~[dubbo-2.6.2.jar:2.6.2]
	at cc.mrbird.consumer.controller.HelloController.hello(HelloController.java:17) ~[classes/:na]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_144]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_144]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_144]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_144]
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:209) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:136) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:102) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:877) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:783) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:991) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:925) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:974) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:866) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:635) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:851) ~[spring-webmvc-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:742) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52) ~[tomcat-embed-websocket-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:99) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.filter.HttpPutFormContentFilter.doFilterInternal(HttpPutFormContentFilter.java:109) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.filter.HiddenHttpMethodFilter.doFilterInternal(HiddenHttpMethodFilter.java:93) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:200) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107) ~[spring-web-5.0.8.RELEASE.jar:5.0.8.RELEASE]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:198) ~[tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:493) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:140) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:81) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:87) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:342) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:800) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:800) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1471) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [na:1.8.0_144]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [na:1.8.0_144]
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61) [tomcat-embed-core-8.5.32.jar:8.5.32]
	at java.lang.Thread.run(Thread.java:748) [na:1.8.0_144]

```

然后一开始看到zk的控制台有个Exception，然后以为是zk的锅，后面发现并不是

然后就顺着Dubbo Service启动服务加载bean为NULL这个思路去百度。

然后看到几个回答，原因和解决方案是这样:

```
spring 扫描的时候根本无法识别@Reference ，同一方面，dubbo的扫描也无法识别Spring @Controller ,所以两个扫描的顺序要排列好，如果先扫了controller，这时候把控制器都实例化好了，再扫dubbo的服务，就会出现空指针。
```

但是我后面也试了一下，我这里是完全用的注解加yml配置，并没有使用任何的xml配置，和读取任何xml文件启动的操作。

然后，发现了一个答案，**如果电脑启动了wifi热点或者虚拟机，需要关闭它们，然后就能正常运行**

然后我真的把wifi热点关闭之后，服务就调用上了，我不禁思考为什么。

都知道Dubbo服务注册之后，是通过调用远程注册的服务，然后是通过地址调用。然后也正是因为我开启了wifi热点，使得本机的ip发生了改变，因此也出现了所谓的**”服务管道关闭“**，**“无法连接到远程服务”**，然后也就是出现 Service服务初始化为NULL的原因。总而，原因就是**远程地址改变，服务调用无法获取导致空指针异常**。



然后探究一下，在打开wifi热点的时候，究竟我们的ip发生了什么变化？

用wifi热点上网其实就是利用电脑软件做路由器，用无线网卡发射wifi信号，因此它也是产生了一个叫做地址池的东西，所以ip地址会变成多个，比如是192.168.1.2-192.168.1.255之间的ip地址。

因此，Dubbo无法从这么多个ip里面识别哪个是注册ip，或者说已经抢占了某个虚拟ip,所以就无法提供服务。