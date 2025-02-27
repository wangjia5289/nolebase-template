# 1、概述






# 2、核心功能
### 1、起步依赖

#### 1、基本概念
在 Spring Boot 中，**起步依赖**（Starter Dependencies）是为简化项目配置而设计的核心特性之一。**起步依赖**通过提供一组常用的依赖集合，起步依赖让开发者能够轻松集成各种功能和第三方库，而无需逐一配置具体的依赖和版本。

本质上，Spring Boot 的起步依赖是 Maven 或 Gradle 的 POM 文件（或 Gradle 的 build 文件）中定义的依赖集合。每个起步依赖都包含了实现某种功能或集成特定技术所需的相关库。
> [!NOTE] 小结
> 简单来说，开发什么场景就导入相应的 **起步依赖**，它可以帮助我们：
>  1. **简化配置**：只需引入一个起步依赖，Spring Boot 就会自动引入与该场景相关的所有依赖。
>  2. **自动版本管理**：起步依赖自动管理库的版本，确保它们之间的兼容性。
>  3. **减少冗余代码**：开发者不再需要手动配置各类库及其版本，显著减少重复配置工作。


#### 2、常用起步依赖
1. Developer Tools
	1. Lombok：
		1. 减少样板代码编写：不用进行 getters、setters、构造函数的书写
		
2. Web
	1. Spring Web：
		1. 内置 Web 服务器：内嵌了常用 Web 服务器（Tomcat、Jetty、Undertow），不需要单独配置和运行服务器，可以直接运行 Boot 应用
		2. 引入 Spring MVC 框架：引入和配置了 Spring MVC 框架，简化基础 Spring MVC 的配置
3. SQL
	1. MyBatis Framework
		1. 引入 MyBatis 核心包和 Spring MyBatis 集成包
		2. 自动集成 Spring 的事务管理
		3. 自动配置 SqlSessionTemplate
		4. 默认使用 Hixxx 连接池





#### 3、常见问题

##### 1、为什么不需要写版本号？

每个 SpringBoot 项目都有一个父项目：`spring-boot-starter-parent`
``` xml
<parent>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-parent</artifactId>  
    <version>3.3.4</version>  
    <relativePath/> <!-- lookup parent from repository -->  
</parent>
```

这个父项目又有一个父项目：`spring-boot-dependencies
``` xml
<parent>  
  <groupId>org.springframework.boot</groupId>  
  <artifactId>spring-boot-dependencies</artifactId>  
  <version>3.3.4</version>  
</parent>
```

在这个父项目中，所有常见的jar的依赖版本都被声明好了
``` xml
<properties>  
  <activemq.version>6.1.3</activemq.version>  
  <angus-mail.version>2.0.3</angus-mail.version>  
  <artemis.version>2.33.0</artemis.version>  
  <aspectj.version>1.9.22.1</aspectj.version>  
  <assertj.version>3.25.3</assertj.version>  
  <awaitility.version>4.2.2</awaitility.version>
  ......
</properties>
```


##### 2、如何指定某 jar 包版本？
尽管 Spring Boot 自动管理了大多数依赖的版本，如果你确实需要指定某个库的版本，仍然可以在本项目的 `pom.xml` 中显式指定，根据 Maven 的就近原则，会使用你指定的版本。

``` xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>5.6.15.Final</version>
</dependency>

```

### 2、包扫描规则
Spring Boot 的包扫描规则主要依赖于 `@ComponentScan` 注解，默认会扫描主类所在包及其子包。
我们可以自定义扫描路径，这样 Spring Boot 将只会扫描指定的包及其子包，而不会自动扫描主类所在的包及其子包，例如：
``` java
// 自定义扫描路径：用 scanBasePackages 属性
@SpringBootApplication(scanBasePackages = "com.example") 
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

> [!NOTE] 补充
> 1、`@SpringBootApplication` 标注的类就是主类
> 2、扫描包指的是：对找到的组件进行实例化和管理





# 3、配置文件

- **配置文件**的所有配置项是和某个**类的对象**值进行一一绑定的。
- 绑定了配置文件中每一项值的类： **属性类**。
- 比如：

- `ServerProperties`绑定了所有Tomcat服务器有关的配置
- `MultipartProperties`绑定了所有文件上传相关的配置
- ....参照[官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.server)：或者参照 绑定的 **属性类**。
`application.properties`：

- 集中式管理配置。只需要修改这个文件就行 。
- 配置基本都有默认值
- 能写的所有配置都在： [https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties)


都绑定一个属性？？？？？
### 2、常用配置项

##### 1、服务器端配置：`server`
在 Spring Boot 中，`server` 配置主要用于设置内嵌服务器（如 Tomcat、Jetty、Undertow）的相关参数。
``` properties
# 1. 应用程序的运行端口，默认是8080
server.port=8081 

# 2. 设置上下文路径，默认是空，这样应用的访问路径将变为 http://localhost:8080/myapp
server.servlet.context-path=/myapp 
```


##### 2、Spring MVC 配置：`spring.mvc`
在 Spring Boot 中，`spring.mvc` 配置用于调整 Spring MVC 的相关设置。
``` properties
# 1. 异步请求的超时时间，单位为毫秒，默认无
spring.mvc.async.request-timeout=5000 （5秒）

# 2. 支持通过请求参数进行内容协商，默认不支持
spring.mvc.contentnegotiation.favor-parameter=true 

# 3. 自定义媒体类型（MIME TYPE），用于内容协商
spring.mvc.contentnegotiation.media-types.json=application/json
```



##### 3、Web 场景配置：`spring.web`




##### 4、文件上传配置：`spring.servlet.multipart`


##### 5、数据源：`spring-datasource`


##### 6、MyBatis：`mybatis`

