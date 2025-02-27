
# 1、概述

# 2、核心功能

# 3、配置文件


# 4、日志框架
# 5、异常处理

### 1、Spring Boot 异常处理机制

Spring Boot 内置了一个完善的异常处理机制，不需要引入额外的依赖，就可以使用 Boot 提供的默认异常处理机制。一般会按照以下步骤依次处理错误：
1. **优先检查 @ExceptionHandler**
2. **其次检查 @ResponseStatus**
3. **最后交由 Spring Boot 内置的全局错误处理机制**

> [!NOTE] 注意：
>一般将异常有关类都放到 `exception` 包下，例如自定义异常类、全局异常处理器



##### 1、优先检查 @ExceptionHandler
当控制器抛出异常时，首先会检查该控制器或全局异常处理类中是否定义了相应的 `@ExceptionHandler` 来处理该异常 ，它是最优先的处理机制，针对特定异常类型自定义响应。
`@ExceptionHandler` 主要用于定制系统已有的异常（如系统异常和自定义异常）的响应。它既可以应用于单个控制器类，也可以通过 `@ControllerAdvice` 来实现全局的异常处理（注意：局部 > 全局）

- **单个控制器类**
``` java
@RestController
@RequestMapping("/api")
public class UserController {

    @GetMapping("/user/{id}")
    public User getUser(@PathVariable("id") Long id) {
        if (id == null) {
            throw new IllegalArgumentException("User ID cannot be null");
        }
        // 获取用户逻辑
        return new User();
    }

    // 单个控制器异常处理器
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<Map<String, String>> handleIllegalArgumentException(IllegalArgumentException ex) {
        Map<String, String> response = new HashMap<>();
        response.put("error", "Invalid argument");
        response.put("message", ex.getMessage());
        return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
    }
}
```
- **全局异常处理**
``` java
@ControllerAdvice // 全局异常处理器
public class GlobalExceptionHandler {

    // 处理所有控制器中的 IllegalArgumentException
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<Map<String, String>> handleIllegalArgumentException(IllegalArgumentException ex) {
        Map<String, String> response = new HashMap<>();
        response.put("error", "Invalid argument");
        response.put("message", ex.getMessage());
        return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
    }

    // 处理所有控制器中的 NullPointerException
    @ExceptionHandler(NullPointerException.class)
    public ResponseEntity<Map<String, String>> handleNullPointerException(NullPointerException ex) {
        Map<String, String> response = new HashMap<>();
        response.put("error", "Null pointer exception");
        response.put("message", ex.getMessage());
        return new ResponseEntity<>(response, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

##### 2、其次检查 @ResponseStatus
`@ResponseStatus` 常与自定义异常绑定，用于自定义异常类的 HTTP 状态码和原因短语，简洁明了，不需要额外编写 `@ExceptionHandler`，但是响应内容较固定，适用于简单场景。也可以使用 `@ExceptionHandler` 进行定制，不过 HTTP状态码和原因短语就不用写了。

- **自定义异常**
``` java
@ResponseStatus(value = HttpStatus.NOT_FOUND, reason = "Resource Not Found")
// 自定义异常类：继承 RutimeException
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```
- **抛出异常**
``` java
@RestController
@RequestMapping("/api")
public class MyController {

    @GetMapping("/resource/{id}")
    public ResponseEntity<String> getResourceById(@PathVariable String id) {
        if ("123".equals(id)) {
            return ResponseEntity.ok("Resource found");
        } else {
            throw new ResourceNotFoundException("Resource with ID " + id + " not found");
        }
    }
}
```

##### 3、交由全局错误处理机制
Spring Boot 默认提供了一套标准的错误处理机制，会捕获未处理的异常并返回相关响应，这一机制由 `BasicErrorController` 实现，负责统一处理所有未捕获的错误并生成相应的错误响应，步骤如下：

![[Pasted image 20241015230933.png]]
对于前后端分离，不需要关注页面，只需要关注返回 JSON 数据，默认返回的 JSON 响应格式一般如下：
``` json
{
    "timestamp": "2024-10-15T12:30:45.678+00:00", // 时间戳
    "status": 500,  // HTTP 状态码
    "error": "Internal Server Error",  // 状态码对于的错误描述
    "message": "Some detailed message",  // 错误的详细信息
    "path": "/api/some-endpoint"  // 发生错误的请求路径
}
```
如果需要自定义返回的错误格式，可以通过实现 `ErrorAttributes` 接口来定制错误的内容。
``` java
@Component
public class MyErrorAttributes extends DefaultErrorAttributes {

    @Override
    public Map<String, Object> getErrorAttributes(WebRequest webRequest, ErrorAttributeOptions options) {
        Map<String, Object> errorAttributes = super.getErrorAttributes(webRequest, options); // 保留默认错误属性
        // 修改默认错误属性（直接覆盖就好）
        errorAttribute.put("statue",9999); 
    
	    // 移除默认错误属性
	    errorAttributes.remove("trace"); // 移除 trace 属性
	
        // 添加自定义的错误属性
        errorAttributes.put("customMessage", "这是一个自定义错误信息");
        return errorAttributes;
    }
}
```

### 2、常见异常类型

##### 1、常见系统异常
**1、`Exception`**
- **简介**：`Exception` 是所有异常类的父类，代表程序中的一般性异常。它是一个受检异常（checked exception），意味着必须处理它或在方法签名中声明。
- **场景**：任何可能的异常情况。一般不直接使用 `Exception`，而是使用它的具体子类。
- **处理示例**：
``` java
@ExceptionHandler(Exception.class)
public ResponseEntity<Map<String, Object>> handleAllExceptions(Exception ex) {
    Map<String, Object> response = new HashMap<>();
    response.put("error", "Internal Server Error");
    response.put("message", ex.getMessage());
    return new ResponseEntity<>(response, HttpStatus.INTERNAL_SERVER_ERROR);
}
```


**2、`NullPointerException`**
- **简介**：`NullPointerException` 是未受检异常（unchecked exception），表示程序试图在 `null` 值上调用方法或访问其成员变量时出现的错误。
- **场景**：试图调用一个 `null` 对象的方法或属性时。例如，未正确初始化对象时可能会抛出该异常。
- **处理示例**：
  ```java
  @ExceptionHandler(NullPointerException.class)
  public ResponseEntity<Map<String, Object>> handleNullPointerException(NullPointerException ex) {
      Map<String, Object> response = new HashMap<>();
      response.put("error", "Null Pointer Exception");
      response.put("message", "A null value was encountered.");
      return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
  }
  ```

**3、`IllegalArgumentException`**
- **简介**：`IllegalArgumentException` 是未受检异常，用于表示传递给方法的参数不合法或不正确。
- **场景**：当调用方法时，传递了一个不符合方法要求的参数。例如，方法要求一个正数，但传入了负数。
- **处理示例**：
  ```java
  @ExceptionHandler(IllegalArgumentException.class)
  public ResponseEntity<Map<String, Object>> handleIllegalArgumentException(IllegalArgumentException ex) {
      Map<String, Object> response = new HashMap<>();
      response.put("error", "Illegal Argument Exception");
      response.put("message", ex.getMessage());
      return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
  }
  ```

**4、`HttpMessageNotReadableException`**
- **简介**：`HttpMessageNotReadableException` 是 Spring 中的一个异常，表示在处理 HTTP 请求的过程中，无法将请求体反序列化为对象。通常与 JSON 格式不正确或字段类型不匹配相关。
- **场景**：当接收到的请求体内容不完整、不符合预期的格式（如 JSON 无法正确解析）时，会抛出此异常。
- **处理示例**：
  ```java
  @ExceptionHandler(HttpMessageNotReadableException.class)
  public ResponseEntity<Map<String, Object>> handleHttpMessageNotReadableException(HttpMessageNotReadableException ex) {
      Map<String, Object> response = new HashMap<>();
      response.put("error", "Bad Request");
      response.put("message", "Request body is not readable.");
      return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
  }
  ```

**5、`MethodArgumentNotValidException`**
- **简介**：`MethodArgumentNotValidException` 是 Spring 框架中的一个异常，表示在方法参数校验（例如 `@Valid` 注解）过程中，传入的参数未通过校验规则时抛出。
- **场景**：当方法参数带有验证注解（如 `@NotNull`、`@Size` 等），但传递的参数不符合注解定义的验证规则时，会抛出该异常。
- **处理示例**：
  ```java
  @ExceptionHandler(MethodArgumentNotValidException.class)
  public ResponseEntity<Map<String, Object>> handleMethodArgumentNotValidException(MethodArgumentNotValidException ex) {
      Map<String, Object> response = new HashMap<>();
      response.put("error", "Validation Failed");
      response.put("message", "Invalid input data.");
      return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
  }
  ```

**6、`SQLException`**
- **简介**：`SQLException` 是用于处理与数据库访问相关的异常。通常与 JDBC（Java 数据库连接）操作相关联。
- **场景**：数据库操作时出现问题（如查询失败、插入失败、数据库连接断开等）时抛出此异常。
- **处理示例**：
  ```java
  @ExceptionHandler(SQLException.class)
  public ResponseEntity<Map<String, Object>> handleSQLException(SQLException ex) {
      Map<String, Object> response = new HashMap<>();
      response.put("error", "Database Error");
      response.put("message", ex.getMessage());
      return new ResponseEntity<>(response, HttpStatus.INTERNAL_SERVER_ERROR);
  }
  ```

##### 2、自定义异常

###### 1、创建自定义异常类
自定义异常类通常继承自 `RuntimeException` 或 `Exception`。在 Spring 应用中，建议继承 `RuntimeException`，因为它是非检查异常，不需要强制在方法签名中声明。
``` java
public class ResourceNotFoundException extends RuntimeException {
    
    // 构造方法，可以传递异常信息
    public ResourceNotFoundException(String message) {
        super(message); // 将信息传递给父类 RuntimeException
    }

    // 还可以添加更多的构造方法和字段
    public ResourceNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }
}

```

###### 2、抛出自定义异常
在你的业务逻辑或控制器中，当遇到特定的错误条件时，可以抛出你定义的异常。
``` java
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        User user = userService.findUserById(id);
        if (user == null) {
            // 当资源未找到时，抛出自定义的 ResourceNotFoundException
            throw new ResourceNotFoundException("User not found with id: " + id);
        }
        return ResponseEntity.ok(user);
    }
}

```

###### 3、常见自定义异常
在开发 Spring Boot 应用时，创建自定义异常类是为了更好地处理应用中出现的各种异常情况，并且能够对外返回更易于理解的错误信息。以下是一些常见的自定义异常类，它们通常用于特定的业务逻辑或应用场景。


**1、ResourceNotFoundException**  
   - **用途**：表示找不到指定资源（如数据库中的某条记录、文件、API 资源等）。
   - **常用场景**：查询某个实体时，发现该实体在数据库中不存在。
   
   ```java
   public class ResourceNotFoundException extends RuntimeException {
       public ResourceNotFoundException(String message) {
           super(message);
       }
   }
   ```

 **2、BadRequestException**  
   - **用途**：表示客户端请求的参数或内容不符合服务器的要求。
   - **常用场景**：请求的参数无效、缺少必要的参数、请求格式错误等。

   ```java
   public class BadRequestException extends RuntimeException {
       public BadRequestException(String message) {
           super(message);
       }
   }
   ```

**3、UnauthorizedException**  
   - **用途**：表示用户未通过身份验证，无法访问资源。
   - **常用场景**：用户未登录或提供的凭证无效时抛出。

   ```java
   public class UnauthorizedException extends RuntimeException {
       public UnauthorizedException(String message) {
           super(message);
       }
   }
   ```

**4、ForbiddenException**  
   - **用途**：表示用户虽然通过了身份验证，但没有访问某个资源的权限。
   - **常用场景**：当用户尝试访问其角色或权限不允许的资源时抛出。

   ```java
   public class ForbiddenException extends RuntimeException {
       public ForbiddenException(String message) {
           super(message);
       }
   }
   ```

**5、ConflictException**  
   - **用途**：表示请求中的数据与服务器已有的资源冲突。
   - **常用场景**：当数据库中已经存在某个实体（如用户名、邮箱等唯一字段），而客户端又提交相同的数据时抛出。

   ```java
   public class ConflictException extends RuntimeException {
       public ConflictException(String message) {
           super(message);
       }
   }
   ```

**6、InternalServerErrorException**  
   - **用途**：表示服务器内部出现了无法处理的异常（通常是意料之外的错误）。
   - **常用场景**：当捕获到某个未预料的异常或者第三方服务出错时抛出。

   ```java
   public class InternalServerErrorException extends RuntimeException {
       public InternalServerErrorException(String message) {
           super(message);
       }
   }
   ```

 **7、ValidationException**  
   - **用途**：表示用户输入数据不符合验证规则。
   - **常用场景**：当请求的数据未通过后端验证规则（如字段长度、格式等）时抛出。

   ```java
   public class ValidationException extends RuntimeException {
       public ValidationException(String message) {
           super(message);
       }
   }
   ```

**8、DataIntegrityViolationException**  
   - **用途**：表示数据违反了完整性约束（例如数据库约束，外键约束等）。
   - **常用场景**：数据库操作时违反唯一性约束、外键约束等规则时抛出。

   ```java
   public class DataIntegrityViolationException extends RuntimeException {
       public DataIntegrityViolationException(String message) {
           super(message);
       }
   }
   ```

**9、ServiceUnavailableException**  
   - **用途**：表示服务暂时不可用，可能是外部依赖服务不可用或内部服务过载。
   - **常用场景**：当依赖的第三方 API 不可用，或服务器过载时抛出。

   ```java
   public class ServiceUnavailableException extends RuntimeException {
       public ServiceUnavailableException(String message) {
           super(message);
       }
   }
   ```

**10、TimeoutException**  
 - **用途**：表示操作超时，通常是等待外部服务或操作超时导致的。
- **常用场景**：请求第三方服务或执行耗时操作时超时抛出。

    ``` java
    public class TimeoutException extends RuntimeException {
        public TimeoutException(String message) {
            super(message);
        }
    }
    ```



##### 3、如何处理异常
###### 1、捕获和处理
使用 `try-catch` 块处理异常，特别是受检异常。
``` java
try {
    // 可能抛出 SQLException 的代码
} catch (SQLException e) {
    e.printStackTrace();  // 打印异常堆栈信息
}
```

###### 2、局部异常处理
通过 Spring 的 `@ExceptionHandler` 捕获局部异常，返回错误响应
``` java
@RestController
@RequestMapping("/api")
public class UserController {

    @GetMapping("/user/{id}")
    public User getUser(@PathVariable("id") Long id) {
        if (id == null) {
            throw new IllegalArgumentException("User ID cannot be null");
        }
        // 获取用户逻辑
        return new User();
    }

    // 单个控制器异常处理器
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<Map<String, String>> handleIllegalArgumentException(IllegalArgumentException ex) {
        Map<String, String> response = new HashMap<>();
        response.put("error", "Invalid argument");
        response.put("message", ex.getMessage());
        return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
    }
}
```


###### 3、全局异常处理
通过 Spring 的 `@ControllerAdvice` 和 `@ExceptionHandler` 全局捕获异常，并返回统一的错误响应。
``` java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(NullPointerException.class)
    public ResponseEntity<String> handleNullPointerException(NullPointerException ex) {
        return new ResponseEntity<>("Null Pointer Exception occurred", HttpStatus.INTERNAL_SERVER_ERROR);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<String> handleValidationException(MethodArgumentNotValidException ex) {
        return new ResponseEntity<>("Validation failed", HttpStatus.BAD_REQUEST);
    }
}
```


##### 4、最佳实战

1. 后端发生的所有错误，使用 `@ControllerAdvice` + `@ExceptionHandler` 进行统一异常处理
2. 后端只注重返回 JSON 数据，虽然 SpringBoot 对返回错误页面的能力也很强大

# 6、Web 开发
