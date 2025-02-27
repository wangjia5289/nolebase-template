### 1、创建 Web 项目
1. 打开 IDEA 工具，安装 JavaToWeb 插件
2. 创建空项目
3. 配置 Maven、JDK
4. 创建一个 Maven 模块
5. 右键，点击 JBLJavaToWeb
6. Ctrl + F5 刷新一下

---


### 2、创建 Spring IoC 项目

引入 [Spring Context 依赖](https://mvnrepository.com/artifact/org.springframework/spring-context)
```xml
<dependencies>
    <!-- 1. Spring Context -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>xxxxx</version>
    </dependency>
</dependencies>
```

---


### 3、创建 Spring AOP 项目

引入  [Spring Context 依赖](https://mvnrepository.com/artifact/org.springframework/spring-context)、[Spring AOP 依赖](https://mvnrepository.com/artifact/org.springframework/spring-aop)、[Spring Aspects 依赖](https://mvnrepository.com/artifact/org.springframework/spring-aspects)、[Aspectj Weaver 依赖](https://mvnrepository.com/artifact/org.aspectj/aspectjweaver)
```xml
<dependencies>

    <!-- 1. Spring Context 依赖 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>xxxxxx</version>
    </dependency>
    
    <!-- 2. Spring AOP 核心依赖 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>xxxxxx</version>
    </dependency>

    <!-- 3. Spring AOP 的注解支持 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>xxxxxx</version>
    </dependency>

    <!-- 4. AspectJ 语言支持 -->
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>xxxxxx</version>
    </dependency>
</dependencies>
```

---


### 4、创建 Spring Boot 项目

#### 4.1、创建 Spring Boot 项目

##### 4.1.1、使用 Spirng 提供的脚手架
使用 Spring 提供的脚手架 [Spring Initializr](https://start.spring.io/) 创建项目，下载并解压 ZIP 包，然后在 IntelliJ IDEA 中选择 **Open** 打开该项目。

---


##### 4.1.2、使用 IDEA 提供的脚手架

---


##### 4.1.3、手动添加 Spring Boot 依赖 (继承 Parent)

==1.继承 `spring-boot-starter-parent`==
```xml
# 继承 spring-boot-starter-parent
<parent>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-parent</artifactId>  
    <version>3.4.1</version>  
</parent>
```

> [!NOTE] 补充：spring-boot-starter-parent
> [spring-boot-starter-parent](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent) 是 Spring Boot 的官方父级项目，提供以下核心功能：
> 1. <font color="#00b0f0">依赖版本统一管理</font>：预定义常用依赖的兼容版本，避免版本冲突。
> 2. <font color="#00b0f0">默认构建配置</font>：内置 Maven 插件（如 `spring-boot-maven-plugin`）的优化配置。
> 3. <font color="#00b0f0">资源过滤</font>：自动处理 `application.properties` 和 `application.yml` 的占位符替换。


==2.引入所需的起步依赖==
```xml
# 引入所需的起步依赖
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-web</artifactId>  
    <version>3.4.0</version>  
</dependency>  
```

> [!NOTE] 注意事项
> 1. 继承父项目后，起步依赖无需指定版本号，而是由父级统一管理
> 2. 常用起步依赖有：[[笔记：Spring Boot 常用起步依赖|常用起步依赖]]

---


##### 4.1.4、手动添加 Spring Boot 依赖 (不继承 Parent)

==1.引入 `spring-boot-depencies`==

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <type>pom</type>
  <scope>import</scope>
</dependency>
```

> [!NOTE] 补充：spring-boot-depencies
> [spring-boot-depencies](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-dependencies) 是一个 Spring Boot 提供的父级 BOM（Bill of Materials）文件，主要用于统一管理 Spring Boot 项目的依赖版本。


==2.引入所需的起步依赖==
```xml
# 引入所需的起步依赖
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-web</artifactId>  
</dependency>  
```

> [!NOTE] 注意事项
> 1. 起步依赖无需指定版本号，而是由 `spring-boot-depencies` 一管理
> 2. 常用起步依赖有：[[笔记：Spring Boot 常用起步依赖|常用起步依赖]]

---


#### 4.2、创建主类并添加注解

在 `src/main/java/com/example` 下创建一个主类 `Application`，并添加 `@SpringBootApplication` 注解，它是 Spring Boot 应用的入口点
```java
/**
* @SpringBootApplication 是组合注解，包含
*    1. @SpringBootConfigutation：标识为配置类
*    2. @EnableAutoConfiguration：启用自动配置
*    3. @ComponentScan：扫描当前包及子包的组件
*/

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

> [!NOTE] 注意事项
> 1. 主类的写法非常重要，需要牢牢记住

---


#### 4.3、对项目进行必要配置
```yaml
server:  
  servlet:  
	prot: 8080   # 项目端口号
    encoding:    # 项目响应字符
      enabled: true  
      charset: UTF-8  
      force: true
```

---





