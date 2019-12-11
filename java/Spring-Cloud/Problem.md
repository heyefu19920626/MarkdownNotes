# 问题

- [问题](#问题)
  - [Eureka Server](#eureka-server)
    - [自我保护机制](#自我保护机制)


## Eureka Server

### 自我保护机制

系统在三种情况下会出现红色加粗的字体提示：[参考博客园](https://www.cnblogs.com/linjiqin/p/10087462.html)

a.在配置上，自我保护机制关闭  
<font color="red">RENEWALS ARE LESSER THAN THE THRESHOLD. THE SELF PRESERVATION MODE IS TURNED OFF.THIS MAY NOT PROTECT INSTANCE EXPIRY IN CASE OF NETWORK/OTHER PROBLEMS.</font>

b.自我保护机制开启了  
<font color="red">EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE
NOT BEING EXPIRED JUST TO BE SAFE.</font>

c.在配置上，自我保护机制关闭了，但是一分钟内的续约数没有达到85% ， 可能发生了网络分区，会有如下提示  
<font color="red">THE SELF PRESERVATION MODE IS TURNED OFF.THIS MAY NOT PROTECT INSTANCE EXPIRY IN CASE OF NETWORK/OTHER PROBLEMS.</font>

``eureka.server.enable-self-preservation=true``开启自我保护机制

解决方式：[Stackoverflow](https://stackoverflow.com/questions/33921557/understanding-spring-cloud-eureka-server-self-preservation-and-renew-threshold)

1. Deploy two Eureka server and enable registerWithEureka.
2. If you just want to deploy in demo/dev environment, you can set eureka.server.renewalPercentThreshold to 0.49, so when you start up a Eureka server alone, threshold will be 0.