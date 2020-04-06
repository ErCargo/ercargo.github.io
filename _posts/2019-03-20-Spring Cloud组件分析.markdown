---
layout: post
title: Spring Cloud 组件分析
date: 2019-03-20 23:10
---

- Spring Cloud
- 



## Spring Cloud

> 快速开发分布式应用的工具集

主要功能

|      功能          | 工具                                           |
| -------------------- | ---------------------------------------------------|
| 分布式/版本化的配置管理 | Spring Cloud Config、 Consul、Zookeeper、Nacos      |
| 服务注册与服务发现      |  Eureka、Consul、Zookeeper、Nacos                  |
| 路由/网关              |  Zuul、Spring Cloud Gateway                              |
| 端到端的服务调用        |  RestTemplate、Feign     |
| 负载均衡  |  Ribbon  |
| 断路器 | Hystrix、Resilienc4J、 Sentinel |
| 全局锁 | SpringCloud Cluster (已迁移到 SpringIntegration)  |
| 选举与集群管理状态  |  SpringCloud Cluster (已迁移到 SpringIntegration)  | 
| 分布式消息 | Kafka、 RabbitMQ Spring Cloud Stream |


## Spring cloud 版本与兼容性

Spring Cloud 版本命名: `https://spring.io/projects/spring-cloud#learn`

版本顺序:

Angel Brixton Camden  Dalston Edgware Finchley Greenwich Hoxton

指定版本的发布流程：

SNAPSHOT -> Mx -> RELEASE -> SRx 


Spring Cloud 生命周期

- 版本发布规划: `https://github.com/spring-cloud/spring-cloud-release/milestones`

- 版本发布记录: `https://github.com/spring-cloud/spring-cloud-release/releases`

- 版本终止声明:  `https://spring.io/projects/spring-cloud#overview`


Spring cloud 和 Spring Boot 的版本兼容性: `https://spring.io/projects/spring-cloud`

![version](/assets/images/version.jpg)

