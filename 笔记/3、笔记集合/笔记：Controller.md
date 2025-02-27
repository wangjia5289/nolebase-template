### 1、Controller 概述

控制器（Controller）是对 Java Web 中 Servlet 的一种优化。Spring MVC 提供了一系列请求映射注解，我们不再需要手动实现 Servlet 类，而是通过标注相应的请求映射注解，并将其纳入 Spring 容器管理，从而自动处理请求。

---

Controller 是掉 Service 层的

1. **单一职责**：每个 Controller 处理一类相关的请求，比如针对某个资源（例如用户、订单）的所有操作。例如，一个针对用户资源的 Controller 可能包含以下方法：
   - `createUser()`：处理用户创建请求。

   - `getUser()`：处理获取用户信息请求。

   - `updateUser()`：处理更新用户信息请求。

   - `deleteUser()`：处理删除用户请求。
2. **避免业务逻辑**：保持方法简洁，易于理解，尽量避免复杂的逻辑，将复杂的业务逻辑放在 Service 层。



### 2、请求映射注解

#### 2.1、请求映射注解概述

请求映射注解是 Spring MVC 提供的一组注解，用于将 HTTP 请求映射到控制器的方法。开发者可以通过这些注解定义特定的 URL 路径和请求方法，以处理不同类型的请求。**这些注解可以应用于类或方法上**。常见的请求映射注解包括：
3. <font color="#00b0f0">@RequestMapping</font>：通用的请求映射注解，其他注解都是它的衍生注解。
4. <font color="#00b0f0">@GetMapping</font>：用于处理 GET 请求，等同于 `@RequestMapping(method = RequestMethod.GET)`
5. <font color="#00b0f0">@PostMapping</font>：用于处理 POST 请求，等同于 `@RequestMapping(method = RequestMethod.POST)`
6. <font color="#00b0f0">@PutMapping</font>：用于处理 PUT 请求，等同于 `@RequestMapping(method = RequestMethod.PUT)`
7. <font color="#00b0f0">@DeleteMapping</font>：用于处理 DELETE 请求，等同于 `@RequestMapping(method = RequestMethod.DELETE)`
8. <font color="#00b0f0">@PatchMapping</font>：用于处理 PATCH 请求，等同于 `@RequestMapping(method = RequestMethod.PATCH)`

---


#### 2.2、`@RequestMapping` 讲解

##### 2.2.1、`@RequestMapping` 概述

请求映射注解中，唯一值得讲的只有 `@RequestMapping`，其他的都是它的衍生注解。`@RequestMapping` 是 Spring MVC 中用于处理 HTTP 请求的核心注解之一。它可以用于类或方法，允许你定义请求的 URL、HTTP 方法、请求参数等。

---


##### 2.2.2、`@RequestMapping` 使用位置

==1.用于类上==
当用于类上时，`@RequestMapping` 可以为该类中的所有处理方法定义一个公共的基础路径。
```java
@RequestMapping("/api")
public class MyController {
   
   public ResponseEntity<List<User>> getUsers() {
   ......
	}
}
```

==2.用于方法上==
当用于方法上时，可以指定具体的请求路径和其他细节。
```java
@RequestMapping("/api")
public class MyController {
   
   @RequestMapping("/users") // 匹配/api/users
public ResponseEntity<List<User>> getUsers() {
	......
	}
}
```

---


##### 2.2.3、`@RequestMapping` 的属性

==1.value==：指定请求的 URL 路径
```java
@RequestMapping("/testValue") // 如果只需要配置这一个属性
@RequestMapping(value = "/testValue")
@RequestMapping(value = {"/testValue1", "/testValue2"})
```

> [!NOTE] 注意事项
> `@RequestMapping` 不支持使用正则表达式，它仅支持固定路径和通配符模式：
> 1. **固定路径：例如："/testValue"**
> 2. **通配符模式**：
> 	1. `?`：匹配任意单个字符，例如：`@RequestMapping("/x?z/testValueAnt")`
> 	2. `*`：匹配路径中的零个或多个字符，例如：`@GetMapping("/files/*")`
> 	3. `**`：匹配多级路径，例如：`@GetMapping("/api/**")`
> 	4. `{xx}`：用于动态路由，例如：
> 		1. `@GetMapping("/users/{id}")`：设置动态路由
> 		2. `public ResponseEntity<User> getUser(@PathVariable Long id) { }`：获取动态路由

==2.method==：指定 HTTP 方法，如 `GET`、`POST`、`PUT`、`DELETE` 、`PATCH`等，如果请求类型不匹配，会出现 405 错误
```java
  @RequestMapping(value = "/users", method = RequestMethod.POST)
```

==3.params==：指定请求参数的条件，只有满足条件的请求才会被该方法处理，如果请求参数条件不匹配，会出现 400 错误
```java
@RequestMapping(value = "/users", params = "username") 

@RequestMapping(value="/users", params={"username", "password=100","!Host"})  // 包含 username 参数,不包含 Host 参数,且password=100
```

==4.headers==：指定请求头的条件，只有满足条件的请求才会被该方法处理，如果请求头条件不匹配，会出现 400 或 415 错误
```java
@RequestMapping(value = "/users", headers = "X-Requested-With=XMLHttpRequest")

@RequestMapping(value="/login", headers={"Referer", "!Host","X-Requested-With=XMLHttpRequest"})   // 必须包含 Referer 头，不包含 Host 头，且X-Requested-With=XMLHttpRequest
```

> [!NOTE] 注意事项
> params 和 headers 支持简单的参数匹配和正则表达式

==5.consumes==：指定请求体的内容类型，仅当请求的 Content-Type 符合时才会调用该方法，如果请全体的内容类型不匹配，会出现 415 错误
```java
@RequestMapping(value = "/users", consumes = "application/json")

@RequestMapping(value = "/users", consumes = {"application/json", "application/xml"})
```

---


##### 2.2.4、`@RequestMapping` 使用示例
```java
@RestController
@RequestMapping("/api")
public class UserController {

    @GetMapping("/users")
    public ResponseEntity<List<User>> getAllUsers() {
        // 返回所有用户
    }

    @PostMapping(value = "/users", consumes = "application/json", produces = "application/json")
    public ResponseEntity<User> createUser(@RequestBody User user) {
        // 创建新用户
    }

    @PutMapping(value = "/users/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User user) {
        // 更新用户信息
    }

    @DeleteMapping(value = "/users/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        // 删除用户
    }
}
```

---


### 3、编写 Controller
```java
@RestController
@RequestMapping("/api")
public class UserController {

    @GetMapping("/users")
    public ResponseEntity<List<User>> getAllUsers() {
        // 返回所有用户
    }

    @PostMapping(value = "/users", consumes = "application/json", produces = "application/json")
    public ResponseEntity<User> createUser(@RequestBody User user) {
        // 创建新用户
    }

    @PutMapping(value = "/users/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User user) {
        // 更新用户信息
    }

    @DeleteMapping(value = "/users/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        // 删除用户
    }
}
```

> [!NOTE] 补充：在 Controller 中，一般进行什么操作
> 1. 获取请求信息
> 2. 核心业务处理
> 3. 调用 M 层方法
> 4. 书写响应信息

---


### 4、配置 Controller

配置 Controller，主需要注册 Controller。在 Spring MVC 中，配置 Controller 实际上是将 Controller 类纳入 Spring IoC 容器的管理。详细内容请参考：[[笔记：Spring IoC#二、实操：Spring IoC 配置|Spring IoC 的配置]]。

---

![[Pasted image 20250217181146.png]]
### 5、补充：获取请求信息

#### 5.1、获取请求行信息

##### 5.1.1、查询参数
->->->[[笔记：Controller#5.4、获取请求参数|获取请求参数]]

---


##### 5.1.2、路径参数
```java
@GetMapping("/user/{id}")
public String getUser(@PathVariable("id") String userId) {
    // userId 来自路径 /user/123
    return "user";
}
```

---

##### 5.1.3、其他信息
对于其他信息，Spring MVC 并没有提供简化方法，这时可以使用原生的 Java Web 方法：
```java
@GetMapping("/demo")
public String handleRequest(HttpServletRequest request) {
    String method = request.getMethod();       // GET/POST 等
    String uri = request.getRequestURI();      // /demo
    String protocol = request.getProtocol();   // HTTP/1.1
    return "view";
}
```

---


#### 5.2、获取请求头信息

##### 5.2.1、Cookie
->->->[[笔记：Web 数据存储优化#1.1、Cookie 优化]]

---


##### 5.2.2、其他信息

通过 `@RequestHeader` 注解直接绑定请求头到参数：
==1.获取单个请求头==
```java
@GetMapping("/header")
public String handleHeader(
    @RequestHeader("User-Agent") String userAgent,  // 获取 User-Agent 头
    @RequestHeader(value = "Accept-Language", required = false) String lang) {
    // 处理 userAgent 和 lang
    return "view";
}
```

==2.获取所有请求头==
```java
@GetMapping("/allHeaders")
public String getAllHeaders(@RequestHeader Map<String, String> headers) {
    // headers 包含所有请求头键值对
    return "headers";
}
```

---


#### 5.3、获取请求体信息
请求体通常用于 POST/PUT 请求，包含 JSON、表单数据或文本：

##### 5.3.1、JSON 请求
接收 JSON 请求体并绑定到对象
```java
@PostMapping("/user")
public ResponseEntity<String> createUser(@RequestBody User user) {
    // user 对象自动从请求体 JSON 反序列化
    return ResponseEntity.ok("User created: " + user.getName());
}
```

---


##### 5.3.2、原始文本或二进制数据
```java
@PostMapping("/raw")
public String handleRawBody(@RequestBody String rawBody) {
    // rawBody 是请求体的原始字符串
    return "processed";
}
```

---


##### 5.3.3、表单数据（非 JSON）
```java
@PostMapping("/form")
public String handleForm(@RequestParam("username") String username,
                         @RequestParam("password") String password) {
    // 处理表单字段
    return "login";
}
```

---


#### 5.4、获取请求参数

##### 5.5.1、通过 `@RequestParam` 获取单个参数
```java
@GetMapping("/user")
public String getUser(
    @RequestParam("id") String userId,         // 获取 ?id=123 中的参数
    @RequestParam(value = "role", defaultValue = "guest") String role) {
    // 使用 userId 和 role
    return "profile";
}
```

> [!NOTE] 如果是这种
> public String firstChat(@RequestParam String message)


---


##### 5.5.2、 通过 `Map` 或 `MultiValueMap` 获取所有参数
```java
@GetMapping("/params")
public String getAllParams(@RequestParam Map<String, String> params) {
    // params 包含所有键值对（同名参数取第一个值）
    return "params";
}

@GetMapping("/multiParams")
public String getMultiParams(@RequestParam MultiValueMap<String, String> params) {
    // MultiValueMap 支持同名参数（如 ?id=1&id=2）
    return "multiParams";
}
```

---

##### 5.5.3、自动绑定到 POJO 对象
定义一个与参数名匹配的 Java 对象：
```java
public class UserQuery {
    private String keyword;
    private int page;
    // Getter/Setter
}

@GetMapping("/query")
public String query(UserQuery userQuery) {
    // 自动将 ?keyword=xxx&page=2 绑定到 userQuery 对象
    return "result";
}
```
1. Spring 会自动将字符串参数转换为 `int`、`boolean` 等基本类型。
---


### 6、补充：编写响应信息











**1.5、请求参数**

请求参数可以存在于请求行或请求体中：

1. **请求行**：

   - **查询字符串**： 通常通过 URL 查询字符串传递，如 `GET /api/items?id=1&name=张三`。这种方式适用于简单参数的传递。

   - **参数映射**：

     - 使用 `@RequestParam` 注解将请求参数映射到方法参数。例如：

       ```
       @GetMapping("/items")
       public ResponseEntity<List<Item>> getItems(@RequestParam String id, @RequestParam String name) {
           // 处理请求
       }
       ```

     - 使用 `@ModelAttribute` 注解将请求参数映射到一个 POJO 类的属性上。Spring 会自动进行类型转换，例如：

       ```
       @PostMapping("/items")
       public ResponseEntity<Item> createItem(@ModelAttribute Item item) {
           // 处理请求
       }
       ```

   - **注意事项**：

     - `@RequestParam` 和 `@ModelAttribute` 主要用于处理简单类型（如字符串、整数、布尔值等）的转换。具体转换如下：
       - 字符串类型的请求参数会自动转换为整型、浮点型等。
       - 布尔值字符串（如 "true" 或 "false"）会转换为 `boolean` 类型

     

2. **请求体**：

   - **复杂数据结构**： 请求体主要用于 `POST`、`PUT` 等请求方法，可以传递更复杂的数据结构，如 JSON 或 XML。

   - **数据映射**：

     - 使用 `@RequestBody` 注解将请求体中的数据映射到 POJO 类的属性上。常见的内容类型包括 `application/json` 和 `application/xml`。示例：

       ```
       @PostMapping("/items")
       public ResponseEntity<Item> createItem(@RequestBody Item item) {
           // 处理请求
       }
       ```

   - **依赖和配置**：

     - 使用 `@RequestBody` 注解的前提是引入 Jackson 依赖，并且在 Spring 配置中开启注解驱动。

   - **注意事项**：

     - `@RequestBody` 不适合用于处理简单类型之间的转换，它主要针对复杂对象的序列化和反序列化。




- ##### @RequestParam

  - 处理方法

  ```java
  /*
  注意事项：
  	1. @RequestParam 拥有三个属性：value、required、defaultValue
  	2. @RequsetParam 的 required 默认是 true
  	3. 这种方式不用保证名称一致，如果名称一致，@RequestParam 可以省略
      1. 如果某参数未匹配，并且在 @RequestParam 注解中没有设置 required = false 和默认值，将会抛出 MissingServletRequestParameterException 异常，而不是将参数设置为 null 
  */
  
  @PostMapping(value = "/register")
  public String register(
          @RequestParam(value="username",required = false) // 表示此参数不是必需的
          String a,
          @RequestParam(value="password",defaultValue = "123456") // 表示此参数的默认值是 123456
          String b,
          @RequestParam(value="sex")
          String c,
          @RequestParam(value="hobby")
          String[] d,
          @RequestParam(name="intro")
          String e) {
      return "success";
  }
  ```

  

- ##### @ModelAttribute

  - 处理方法

  ```java
  /*
  注意事项：
  	1. 一般情况下，@ModelAttribute 可以省略
  */
  
  @PostMapping("/register")
  public String register(@ModelAttribute User user){
      System.out.println(user);
      return "success";
  }
  ```

  - POJO 

  ```java
  /*
  注意事项：
  	1. POJO 类的属性名 必须和 请求参数的参数名 保持一致
  	2. 如果请求中有不匹配的参数，String 类型的属性会被赋值为 null，而基本数据类型（如 int、boolean 等）将抛出 HttpMessageNotReadableException。可以考虑将基本数据类型改为其包装类（如 Integer、Boolean），这样未提供时会赋值为 null。
  */
  public class User {
      private Long id;
      private String username;
      private String password;
      private String sex;
      private String[] hobby;
      private String intro;
  
      ...........
  }
  ```

  

- ##### @RequestBody

  - 引入  Jackson 依赖

    ```xml
    <!-- Jackson 是 Spring 默认的 JSON 处理库，用于将 JSON 数据转换为 Java 对象，以及反向转换。 -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.17.0</version>
    </dependency>
    ```

  - 处理方法

    ```java
    @RestController
    public class UserController {
    
        @PostMapping("/users")
        public ResponseEntity<String> createUser(@RequestBody User user) {
            // 处理用户数据
            return ResponseEntity.ok("用户 " + user.getName() + " 已创建，年龄 " + user.getAge());
        }
    }
    ```

  - POJO 

    ```java
    public class User {
        private String name;
        private int age;
    	
        ......
    }
    ```

    

**2、书写响应信息**

在前后端分离的架构中，后端的响应通常有两种情况：直接返回 JSON 数据和定制 `ResponseEntity` 类。

- ##### 直接返回 JSON 数据

  - 返回的数据通常保存在 POJO 对象中，Spring 会自动将该对象转换为 JSON 格式。
  - 注意：前提是已引入 Jackson 依赖、启用了注解支持，并且使用 `@ResponseBody` 标注方法，或使用 `@RestController` 标注类。

  ```java
  @Controller
  public class UserController {
  
      @GetMapping("/api/user")
      @ResponseBody
      public User getUser() {
          return new User("张三", 30); 
      }
  }
  
  或
      
  @RestController  // 相当于 @Controller 并为每个方法添加 @ResponseBody
  public class UserController {
  
      @GetMapping("/api/user")
      public User getUser() {
          return new User("张三", 30); 
      }
  }
  ```

- ##### 定制 `ResponseEntity` 类






