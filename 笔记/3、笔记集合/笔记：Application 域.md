### 1、ServletContext 概述

`ServletContext` 对象在服务器启动时创建，在服务器关闭时销毁。每个 Web 应用仅有一个唯一的 `ServletContext` 对象，所有的 `Servlet` 对象都可以共享该对象。通过 `ServletContext`，我们可以在不同的 `Servlet` 之间共享数据，获取初始化参数，并实现请求分派功能。

由于 `ServletContext` 中的数据在应用中都有效，因此这些数据被称为应用域数据。

---



### 2、ServletContext 操作方法

### 2.1、获取 ServletContext
```java
ServletContext context = getServletContext();
```

---



### 2.1、共享数据

servletContext 允许在应用程序内的各个 Servlet 之间共享数据，可以通过设置属性来实现这一点
```java
/**
* 1. setAttribute(String name, Object object)：设置属性
* 2. getAttribute(String name)：获取属性，一般需要类型转换
* 3. removeAttribute(String name)：移除属性
*/

context.setAttribute("myAttribute", "someValue");

String value = (String) context.getAttribute("myAttribute");

context.removeAttribute("myAttribute");
```

---



#### 2.2、获取初始化参数

`ServletContext` 可以用来获取在 `web.xml` 文件中配置的上下文初始化参数 

==1.设置上下文初始化参数==
```
// web.xml

<context-param>
  <param-name>myParam</param-name>
  <param-value>myValue</param-value>
</context-param>
```

==2.获取初始化参数==
```
String myParamValue = context.getInitParameter("myParam");
```

---


#### 2.3、请求分派
`ServletContext` 可以实现请求分派（Request Dispatching），也就是将请求转发到另一个 Servlet、JSP 页面或静态资源
```java
RequestDispatcher dispatcher = context.getRequestDispatcher("/anotherServlet");
dispatcher.forward(request, response);
```

----



