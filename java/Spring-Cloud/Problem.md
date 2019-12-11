# 问题

- [问题](#问题)
  - [Eureka Server](#eureka-server)
    - [自我保护机制](#自我保护机制)


## Eureka Server

### 自我保护机制

控制台显示：THE SELF PRESERVATION MODE IS TURNED OFF.THIS MAY NOT PROTECT INSTANCE EXPIRY IN CASE OF NETWORK/OTHER PROBLEMS.

解决方式：[Stackoverflow](https://stackoverflow.com/questions/33921557/understanding-spring-cloud-eureka-server-self-preservation-and-renew-threshold)

1. Deploy two Eureka server and enable registerWithEureka.
2. If you just want to deploy in demo/dev environment, you can set eureka.server.renewalPercentThreshold to 0.49, so when you start up a Eureka server alone, threshold will be 0.