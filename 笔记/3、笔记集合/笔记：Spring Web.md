# 一、理论 + [导图：Spring Web](../5、导图集合/导图：SpringWeb.xmind)

### 1、Web

#### 1.1、Java Web

##### 1.1.1、Java Web 概述

JavaWeb 是使用 Java 技术开发 Web 应用的统称，通常采用 MVC 设计模式进行实现。

---


##### 1.1.2、Web 基础知识
1. ==Web 通信流程==
	1. [[笔记：Web 通信流程]]
2. ==URL 与 RESTful URI==
	1. [[笔记：URL 与 RESTful URL]]
3. ==HTTP 请求协议==
	1. [[笔记：HTTP 请求协议]]
4. ==HTTP 响应协议==
	1. [[笔记：HTTP 响应协议]]
5. ==MIME 类型==
	1. [[笔记：MIME 类型]]
6. ==常见网络攻击==
	1. <font color="#00b0f0">XSS 攻击</font>
		1. [[笔记：跨站脚本攻击（XSS）]]
	2. <font color="#00b0f0">CSRF 攻击</font>
		1. [[笔记：跨站请求伪造攻击（CSRF）]]

---


##### 1.1.3、Web 数据存储

###### 1.1.3.1、客户端存储
1. ==Cookie==
	1. [[笔记：Cookie]]
2. ==LocalStorage==
	1. [[笔记：LocalStorage]]
3. ==SessionStorage==
	1. [[笔记：SessionStroage]]
4. ==Redis==
	1. [[笔记：Redis]]

---


###### 1.1.3.2、服务器端存储
1. ==请求域（Request）==
	1. [[笔记：Request 域]]
2. ==会话域（Session）==
	1. [[笔记：Session 域]]
3. ==应用域（Application）==
	1. [[笔记：Application 域]]
4. ==数据库==
	1. [[笔记：MySQL]]
5. ==消息队列==
	1. [[笔记：Kafka]]

---


##### 1.1.4、Java Web 流程图
![[Web 流程图 1.png|500]]

---


##### 1.1.5、Web 核心对象
1. ==请求对象：HttpServletRequest==
	1. [[笔记：HttpServletRequest]]
2. ==响应对象：HttpServletResponse==
	2. [[笔记：HttpServletResponse]]
3. ==控制器：Servlet==
	2. [[笔记：Servlet]]
4. ==过滤器：Filter==
	2. [[笔记：Filter]]
5. ==监听器：Listener==
	2. [[笔记：Listener]]


#### 1.6、Web 记住我功能

session，默认
jwt



---


### 2、MVC 设计模式

MVC（Model-View-Controller）是一种常用的软件设计模式，通过将应用程序分为三个主要部分——模型（Model）、视图（View）和控制器（Controller），实现了代码的分离，从而提高了应用的可维护性、可扩展性和可测试性。
1. ==模型（M）==：负责应用程序的业务逻辑和数据处理，通常包括以下两个层次：
    - <font color="#00b0f0">Service 层</font>：处理具体的业务逻辑和数据管理。
    - <font color="#00b0f0">DAO 层</font>：负责与数据库进行交互，通常使用 JDBC 或 ORM 框架（如 Hibernate、MyBatis）进行数据持久化操作。
2. ==视图（V）==：负责数据的呈现和用户界面的展示，通常无需关注业务逻辑，仅关注如何展现数据。
    - 在传统的 Java Web 应用中，JSP 用于动态生成 HTML 页面。
    - 在现代前端应用中，框架如 React、Vue 或 Angular 通常负责构建用户界面，开发者更多关注前端交互逻辑。
3. ==控制器（C）==：MVC 的核心部分，负责协调模型层和视图层之间的交互。通常包括以下组件：
    - <font color="#00b0f0">控制器（Servlet）</font>：处理 HTTP 请求，调用模型层的业务逻辑，并返回 HTTP 响应。
    - <font color="#00b0f0">过滤器（Filter）</font>：在请求到达控制器之前进行预处理，如身份验证或日志记录。
    - <font color="#00b0f0">监听器（Listener）</font>：监控应用程序或请求生命周期中的事件，如上下文初始化、销毁等。

![[Pasted image 20250212111636.png]]

---


### 3、Spring MVC

#### 3.1、Spring MVC 执行流程
![[Pasted image 20250212212950.png]]

---


#### 3.2、Spring MVC 主要优化 / 新增

Spring MVC 主要优化或新增了以下三种：
1. ==控制器（Controller）==
	1. [[笔记：Controller]]
2. ==拦截器（Interceptor）==
	1. [[笔记：Interceptor]]
3. ==异常处理器==
	1. [[笔记：异常处理器]]
4. ==Web 数据存储优化==
	1. [[笔记：Web 数据存储优化]]



### 4、Spring WebFlux






















---



# 二、实操

### 1、Java Web 开发步骤


#### 1.1、创建 Web 项目
1. 打开 IDEA 工具，安装 JavaToWeb 插件
2. 创建空项目
3. 配置 Maven、JDK
4. 创建一个 Maven 模块
5. 右键，点击 JBLJavaToWeb
6. Ctrl + F5 刷新一下

---


#### 1.2、创建 Java Web 配置文件
```xml
<!-- web.xml -->

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
    version="6.0"> 

</web-app>
```

---


#### 1.3、开启注解支持（若需要）

在 web.xml 中添加 metadata-complete="false"
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
    version="4.0"
    metadata-complete="false"> <!-- true，代表只支持配置文件；false，代表两种配置都支持 -->

</web-app>
```

---


#### 1.4、编写和配置 Servlet
->->->[[笔记：Servlet#3、编写 Servlet|编写和配置 Servlet]]

---


#### 1.5、编写和配置 Filter
->->->[[笔记：Filter#3、编写 Filter|编写和配置 Filter]]

---


#### 1.6、编写和配置 Listener
->->->[[笔记：Listener|编写和配置 Listener]]

---


#### 1.7、部署 Web 项目，进行测试

---


### 2、Spring MVC 开发步骤

#### 2.1、创建 Web 项目
7. 打开 IDEA 工具，安装 JavaToWeb 插件
8. 创建空项目
9. 配置 Maven、JDK
10. 创建一个 Maven 模块
11. 右键，点击 JBLJavaToWeb
12. Ctrl + F5 刷新一下

---


#### 2.2、引入 Spring 核心依赖

引入 Spring 核心依赖：[Spring Context 依赖](https://mvnrepository.com/artifact/org.springframework/spring-context)，此依赖支持 IoC 和 AOP
```xml
<dependencies>
    <!-- Spring Context -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>xxxxxx</version>
    </dependency>
</dependencies>
```

---


#### 2.3、引入 Spring MVC 相关依赖

引入 Spring MVC 相关以来：[Servlet API 依赖](https://mvnrepository.com/artifact/jakarta.servlet/jakarta.servlet-api)、[Spring MVC 依赖](https://mvnrepository.com/artifact/org.springframework/spring-webmvc)、[Jackson Databind 依赖](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind)
```xml
<dependencies>    
    <!-- Servlet API 依赖 -->
    <dependency>
        <groupId>jakarta.servlet</groupId>
        <artifactId>jakarta.servlet-api</artifactId>
        <version>xxxxxx</version>
        <scope>provided</scope>
    </dependency>
    
    <!-- Spring MVC 核心依赖 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>xxxxxx</version>
    </dependency>

    <!-- Jackson Databind 依赖，Jackson 是 Spring 默认的 JSON 处理库，用于将 JSON 数据转换为 Java 对象，以及反向转换。 -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>xxxxxx</version>
    </dependency>
</dependencies>
```

----


#### 2.4、创建 Java Web 配置文件

```xml
<!-- web.xml -->

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
    version="6.0"> 

</web-app>
```

---


#### 2.5、创建 Spring MVC 配置文件

==1.XML 文件==
```xml
<!-- spring-mvc.xml -->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	 xmlns:mvc="http://www.springframework.org/schema/mvc"
	 xsi:schemaLocation="
		 http://www.springframework.org/schema/beans
		 http://www.springframework.org/schema/beans/spring-beans.xsd
		 http://www.springframework.org/schema/mvc
		 http://www.springframework.org/schema/mvc/spring-mvc.xsd">

</beans>
```

==2.Java 配置类==
```java
// SpringMvcConfig.java

  @Configuration
  public class MvcConfig implements SpringMvcConfig {
  
  }
```

---


#### 2.6、开启 Spring MVC 注解支持（若需要）

==1.XML 文件==
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	 xmlns:mvc="http://www.springframework.org/schema/mvc"
	 xsi:schemaLocation="
		 http://www.springframework.org/schema/beans
		 http://www.springframework.org/schema/beans/spring-beans.xsd
		 http://www.springframework.org/schema/mvc
		 http://www.springframework.org/schema/mvc/spring-mvc.xsd">

  <mvc:annotation-driven/> <!-- 启用 Spring MVC 注解支持 -->

  <!-- 其他配置 -->
</beans>
```


==2.Java 配置类==
```java
  @Configuration
  @EnableWebMvc // 启用Spring MVC 注解支持
  public class MvcConfig implements SpringMvcConfig {
  
  }
```

---


#### 2.7、配置前端控制器

在 `web.xml` 中配置前端控制器 `DsipatcherServlet` 类
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">

    <!-- Spring MVC 提供的前端控制器:DsipatcherServlet -->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup> <!-- 确保 Servlet 启动时加载 -->
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/*</url-pattern> <!-- 所有请求交给 DispatcherServlet 处理 -->
    </servlet-mapping>

</web-app>
```

> [!NOTE] 注意事项
> 注意是在 `web.xml` 中配置的前端控制器，而不是在 `spring-mvc.xml` 中

---


#### 2.8、编写和配置 控制器
->->->[[笔记：Controller#3、编写 Controller|编写 Controller]]

---


#### 2.9、编写和配置 拦截器
