---
layout: post
title: Spring Cloud 组件分析
date: 2019-03-20 23:10
---

## Spring Cloud

> 快速开发分布式应用的工具集

### Spring Cloud 主要功能

| 功能          |                功能详情                                | 组件  |
| ------------ | ---------------------------------------------------|
| Distributed/versioned configuration   | 分布式/版本化配置管理 | Spring Cloud Config; Consul; Zookeeper; Nacos |
| Service registration and discovery    | 服务注册与服务发现 | Eureka; Consul; Zookeeper; Nacos |
| Routing   | 路由 | Zuul; Spring Cloud Gateway  |
| Service-to-Service calls   | 端到端的调用 | RestTemplate; Feign |
| Load balancing  | 负载均衡 | Ribbon |
| Circuit Breakers   | 断路器 | Hystrix; Resilience4J; Sentinel  |
| Global locks   | 全局锁 | Spring Cloud Clutser(Spring Integration)  |
| Leadership election and cluster state  | 选举与集群状态管理 | Spring Cloud Clutser(Spring Integration)  |
| Distributed messaging   | 分布式消息 | Spring Cloud Stream + Kafka/RabbitMQ/RocketMQ  |


### Spring Cloud 常用组件

![zj](/assets/images/spring-cloud-zj.jpg)


### Spring cloud 版本与兼容性

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


### 服务注册与服务发现

- 什么是服务发现？

> 让服务消费者总能找服务提供者的一种机制

- 服务发现组件(注册中心) consul
