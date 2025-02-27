### 1、Listener 概述

`Listener` 是一种特殊的类，用于监听和处理特定事件的发生。`Servlet` 规范定义了几种类型的Listener，它们分别用于监听不同类型的事件，如会话创建和销毁、请求初始化和销毁、上下文初始化和销毁等，其目的是在特定的事件发生时（如应用启动、会话创建、请求生命周期等）监听这些事件并执行相应的逻辑。

---



### 2、编写 Listener

监听器的类型多样，可以根据功能选择接口实现并重写方法，进行编写 Listener 类：
1. <font color="#00b0f0">ServletContextListener</font>: 监听 `ServletContext` 的初始化和销毁事件。
2. <font color="#00b0f0">ServletContextAttributeListener</font>: 监听 `ServletContext` 属性的添加、移除和替换事件。
3. <font color="#00b0f0">HttpSessionListener</font>: 监听 `HttpSession` 的创建和销毁事件。
4. <font color="#00b0f0">HttpSessionAttributeListener</font>: 监听 `HttpSession` 中属性的添加、移除和替换事件。
5. <font color="#00b0f0">HttpSessionIdListener</font>: 监听 `HttpSession ID` 变化事件。
6. <font color="#00b0f0">ServletRequestListener:</font> 监听 `ServletRequest` 的初始化和销毁事件。
7. <font color="#00b0f0">ServletRequestAttributeListener</font>: 监听 `ServletRequest` 属性的添加、移除和替换事件。

以下是一个 `ServletContextListener` 示例：
```java
// 使用 @WebListener 注解将该类注册为监听器 
@WebListener 
public class MyServletContextListener implements ServletContextListener { 
	// 当 ServletContext 初始化时调用 
	@Override 
	public void contextInitialized(ServletContextEvent sce) { 
		System.out.println("ServletContext 初始化..."); 
		// 可以在这里进行一些初始化操作，例如加载配置文件、初始化数据库连接池等 
	} 
		
	// 当 ServletContext 销毁时调用 
	@Override 
	public void contextDestroyed(ServletContextEvent sce) {
		System.out.println("ServletContext 销毁..."); 
		// 可以在这里进行一些资源释放操作，例如关闭数据库连接池、释放缓存等 
	} 
}
```
---



### 3、配置 Listener

#### 3.1、xml 方式
```xml
// web.xml

<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                             http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

<!-- ===================== 注册 Listener ========================= -->
<!--
  1. Listener 的全类名
-->
	<listener>
	    <listener-class>com.example.MyServletContextListener</listener-class>
	</listener>
	
	<listener>
	    <listener-class>com.example.MyServletContextAttributeListener</listener-class>
	</listener>
	
</web-app>
```

---



#### 3.2、注解方式
```java
@WebListener  // 注册监听器
public class MyServletContextListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        // 初始化操作
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        // 清理操作
    }
}
```

> [!NOTE] 注意事项
> 1. 如果使用注解配置，需要开启支持注解，即在 web.xml 中要加入 metadata-complete="false"

---


