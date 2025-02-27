# 一、理论 + [导图：Spring Boot](../5、导图集合/导图：SpringBoot.xmind)

### 1、Boot 简化 IoC
->->->[[笔记：Spring IoC#2、Boot 简化开发|Boot 简化 IoC]]

---




---


# 二、实操

### 888、基础配置
```
server:  
  servlet:  
    encoding:  
      enabled: true  
      charset: UTF-8  
      force: true
```

### 999、运行 Spring Boot 项目

由于 Spring Boot 内置了 Tomcat 服务器，因此无需单独部署 Web 服务器即可直接运行项目。常见的运行方式如下：
1. **打包前**：若项目尚未打包，可以通过开发工具启动，例如在 IDEA 中运行 `main` 方法。
2. **打包后**：若项目已打包为 JAR 文件，可以通过命令 `java -jar 文件路径` 来启动项目。

---


