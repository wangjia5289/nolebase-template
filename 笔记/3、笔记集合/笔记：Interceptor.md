### 1、Interceptor 概述

Spring MVC 的 `Interceptor` 允许在请求处理的不同阶段执行特定的逻辑，通常用于日志记录、性能监控、权限验证、请求预处理、响应后处理等功能。与 Servlet 过滤器不同，`Interceptor` 直接与 Spring MVC 的请求处理流程相集成，可以在请求到达 Controller 之前和从 Controller 返回之后的不同阶段进行拦截。

---


### 2、编写 Interceptor

编写 `Interceptor` 需要实现 `HandlerInterceptor` 接口，该接口包含三个方法：`preHandle`、`postHandle` 和 `afterCompletion`
```java
/**
* 1. preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)：在请求到达 Controller 之前调用。返回 true 表示继续执行请求，返回 false 则会阻止请求继续传递。
* 2. postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)：在 Controller 执行完毕后调用，但在视图渲染之前。
* 3. fterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)：在整个请求完成后调用，通常用于清理资源。
*/

public class MyInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 在请求到达控制器之前执行
        return true; // 返回 true 继续处理，false 中止请求
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        // 在控制器处理后，但在视图渲染之前执行
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 在整个请求完成后执行
    }
}
```

---


### 3、配置 Interceptor 

#### 3.1、配置 Interceptor 步骤

在 Spring MVC 中，配置 `Interceptor` 通常涉及以下三个步骤：
1. <font color="#00b0f0">注册 Interceptor</font>：将自定义的 `Interceptor` 类注册到 Spring 配置中。
2. <font color="#00b0f0">配置拦截路径</font>：指定哪些请求路径应该被拦截，通常通过 `addPathPatterns` 和 `excludePathPatterns` 方法进行设置。
3. <font color="#00b0f0">调整拦截器顺序</font>：如果有多个拦截器，设置它们的执行顺序，确保它们按照预期的顺序被调用。

---


#### 3.2、注册 Interceptor

在 Spring MVC 中，注册 Interceptor 实际上是将 Interceptor 类纳入 Spring IoC 容器的管理。详细内容请参考：[[笔记：Spring IoC#二、实操：Spring IoC 配置|Spring IoC 的配置]]。

---


#### 3.3、配置拦截路径

==1.XML 方式==
在 `spring-mvc.xml` 文件中，可以通过 `<mvc:interceptors>` 标签配置拦截器：
```xml
<mvc:interceptors>
	<!-- 第一个拦截器 -->
	<mvc:interceptor>
		<bean class="com.example.FirstInterceptor"/>  // 注册拦截器
		<mvc:mapping path="/api/**"/>  // 拦截路径
		<mvc:exclude-mapping path="/api/public/**"/>  // 排除路径
	</mvc:interceptor>

	<!-- 第二个拦截器 -->
	<mvc:interceptor>
		<bean class="com.example.SecondInterceptor"/>  // 注册拦截器
		<mvc:mapping path="/admin/**"/>  // 拦截路径
		<mvc:exclude-mapping path="/admin/login"/>  // 排除路径
	</mvc:interceptor>
</mvc:interceptors>
```

> [!NOTE] 注意事项
> 拦截器的拦截路径书写方式与 `@RequestMapping` 的 `value` 属性的写法相同，详细内容请参考：[笔记：Controller#2.2.3、`@RequestMapping` 的属性|拦截路径写法]。

==2.Java 配置类方式==
如果使用 Java 配置，可以在实现 `WebMvcConfigurer` 接口的类中重写 `addInterceptors` 方法，用于配置拦截器。
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		// 注册第一个拦截器
		registry.addInterceptor(new FirstInterceptor())	// 注册拦截器
				.addPathPatterns("/api/**") // 拦截路径
				.excludePathPatterns("/api/public/**"); // 排除路径

		// 注册第二个拦截器
		registry.addInterceptor(new SecondInterceptor()) // 注册拦截器
				.addPathPatterns("/admin/**") // 拦截路径
				.excludePathPatterns("/admin/login"); // 排除路径
	}
}
```
---


#### 3.4、调整拦截器顺序
1. 如果所有拦截器的 `preHandle` 方法都返回 `true`：`preHandle` 方法会按照注册的顺序，从上到下依次调用。
2. 如果有任意一个拦截器的 `preHandle` 方法返回 `false`：后续的拦截器的 `postHandle` 方法将不会执行，已执行的拦截器的 `afterCompletion` 方法会按逆序执行，从最后一个拦截器开始，依次执行到第一个

---
