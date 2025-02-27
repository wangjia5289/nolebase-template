### 1、客户端存储

#### 1.1、Cookie 优化

##### 1.1.1、创建 Cookie

`ResponseCookie` 是 Spring MVC 提供的一个工具类，旨在简化响应中 Cookie 的创建和管理。它可以在控制器方法中使用，用于创建 Cookie 并将其添加到 HTTP 响应中。
```java
@RestController
public class CookieController {

    @GetMapping("/set-cookie")
    public ResponseEntity<String> setCookie() {
        // 使用 ResponseCookie 创建一个 Cookie
        ResponseCookie cookie = ResponseCookie.from("user", "john_doe")
                                              .httpOnly(true)   // 设置 HttpOnly 属性，防止 JavaScript 访问
                                              .secure(true)     // 设置为 secure，确保 Cookie 只通过 HTTPS 传输
                                              .path("/")        // 设置 Cookie 的路径
                                              .maxAge(3600)     // 设置 Cookie 的过期时间（单位秒）
                                              .build();

        // 设置响应头，发送 Cookie
        return ResponseEntity.ok()
                             .header(HttpHeaders.SET_COOKIE, cookie.toString())
                             .body("Cookie is set!");
    }
}
```

---


##### 1.1.2、读取 Cookie

使用 `@CookieValue` 注解可直接将 Cookie 的值绑定到控制器方法的参数，无需手动解析 `HttpServletRequest`。
```java
/**
* 1. @CookieValue
*     1.1 value：Spring 根据该名称查找对应的 Cookie 值并赋值给注解所绑定的属性。通常建议 Cookie 名和属性名一致以提高代码可读性。
*     1.2 required：一个布尔值，默认值为 true，表示请求中必须包含指定名称的 Cookie。如果 Cookie 不存在，Spring 会抛出 MissingCookieException 异常。如果希望该 Cookie 可选，可以将该值设置为 false。
*     1.3 defaultValue：指定一个默认值，当请求中没有对应的 Cookie 时，会使用该默认值。通过设置默认值，可以避免因缺少 Cookie 导致的异常。
*/
@RestController
@GetMapping("/demo")
public String handleRequest(User user, @CookieValue("JSESSIONID") String sessionId) {
	  System.out.println(user);
      System.out.println(sessionId);
      return "success";
}
```

---


#### 1.2、LocalStorage/SessionStorage 优化

`LocalStorage / SessionStorage` 仍由前端 JavaScript 管理，但 Spring MVC 支持通过 `@ResponseBody` 或 REST 控制器与前端交互数据，简化了 JSON 数据的传输。

---


### 2、服务器端存储

#### 2.1、请求域（Request）优化

Spring MVC 提供了 `Model` 对象，通过将数据添加到 `Model` 对象，Spring 自动将数据暴露给视图（如 Thymeleaf/JSP），无需手动调用 `request.setAttribute()`
```java
@RequestMapping("/testModel")
public String testModel(Model model){ // 传入 Model 对象
    
    model.addAttribute("testRequestScope", "123456789"); // 存储数据
    
    return "view";
}
```

---


#### 2.2、会话域（Session）优化

在 Spring MVC 中，`@SessionAttributes` 注解用于声明需要存储在会话中的属性键。通过 `Model` 接口的 `addAttribute` 方法，可以将数据存储到会话域中；而 `@SessionAttribute` 注解则用于直接从会话中获取这些属性的值。

==1.存储属性==
```java
@Controller
@SessionAttributes(value = {"x", "y"}) // 在这里要声明需要跨请求的会话属性的键
public class SessionScopeTestController {

    @RequestMapping("/testSessionScope2")
    public String testSessionAttributes(Model model) {
        
        // 向会话域中存储数据
        model.addAttribute("x", "我是埃克斯");
        model.addAttribute("y", "我是歪");

        return "view";
    }
}
```

==2.读取属性==
```
@GetMapping("/profile")
public String profile(@SessionAttribute("user") User user) {
    // 使用 user 对象
    return "profile";
}
```
```java
@Controller
@SessionAttributes("user")
public class UserController {
    @PostMapping("/login")
    public String login(Model model) {
        model.addAttribute("user", new User());
        return "redirect:/dashboard";
    }
}
```

---


#### 2.3、应用域（Application）优化

在 Spring MVC 中，通过 `@Autowired` 注解可以将 `ServletContext` 自动注入到 Spring Bean 中，之后可以通过 `getInitParameter` 方法访问 Web 应用的全局数据。
```java
@Component
public class MyComponent {

    @Autowired
    private ServletContext servletContext;

    public void someMethod() {
        // 使用 servletContext 访问应用级别的数据
        String appName = servletContext.getInitParameter("appName");
        System.out.println("App Name: " + appName);
    }
}
```

---


