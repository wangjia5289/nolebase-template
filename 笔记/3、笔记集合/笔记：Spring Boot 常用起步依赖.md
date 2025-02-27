### 1、前言：起步依赖的命名
在 Spring Boot 生态系统中，起步依赖的命名有一个约定俗成的规则：官方提供的起步依赖和第三方提供的起步依赖的命名有不同的习惯。
1. <font color="#00b0f0">官方起步依赖</font>：spring-boot-starter-xxx
2. <font color="#00b0f0">第三方起步依赖</font>：xxx-spring-boot-starter

---


### 2、Web 模块

| 名称         | 依赖                                                                                                             | 作用                                       |
| ---------- | -------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| Spring Web | [spring-boot-starter-web](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web) | 提供 RESTful API 开发支持（Tomcat + Spring MVC） |

---


### 3、Security 模块

---


### AI 模块

| 名称  | 依赖                                                                                                                  | 作用  |
| --- | ------------------------------------------------------------------------------------------------------------------- | --- |
| 千问  | [spring-ai-alibaba-core](https://mvnrepository.com/artifact/com.alibaba.cloud.ai/spring-ai-alibaba-core/1.0.0-M5.1) |     |

https://mvnrepository.com/artifact/com.alibaba.cloud.ai/spring-ai-alibaba-core/1.0.0-M5.1

https://mvnrepository.com/artifact/com.alibaba/dashscope-sdk-java
根据 IDEA 脚手架中的依赖

| **场景** | **依赖名称**                        | **作用**                  |
| ------ | ------------------------------- | ----------------------- |
| Web 开发 |                                 |                         |
| 数据访问   | `spring-boot-starter-data-jpa`  | JPA + Hibernate 数据库访问   |
| 安全控制   | `spring-boot-starter-security`  | 身份验证和权限管理               |
| 单元测试   | `spring-boot-starter-test`      | 集成测试支持（JUnit + Mockito） |
| 模板引擎   | `spring-boot-starter-thymeleaf` | Thymeleaf 页面渲染          |
| 监控管理   | `spring-boot-starter-actuator`  | 应用健康监控和管理端点             |
| 消息队列   | `spring-boot-starter-amqp`      | RabbitMQ 消息队列集成         |
|        |                                 |                         |
	