### 1、单一模块
```
com.example.模块名称
|
|-- Application.java        // 启动类，包含 main 方法，启动 Spring Boot 应用
|
|-- controller              // 控制器层，处理 HTTP 请求
|   |-- StudentController.java   // 处理学生相关请求
|   |-- ScoreController.java     // 处理成绩相关请求
|
|-- service                 // 业务层，封装核心业务逻辑
|   |-- inter               // 业务层接口，定义服务的方法
|   |-- impl                // 业务层接口实现，具体业务逻辑的实现
|
|-- repository              // 持久层，数据访问层，处理与数据库的交互
|
|-- model                   // 模型层，包含实体类和数据传输对象
|   |-- entity              // 实体类，用于映射数据库表
|   |-- dto                 // 数据传输对象，用于服务之间的数据传递
|   |-- vo                  // 视图对象，用于前端展示的数据格式
|
|-- config                  // 配置类包，包含所有的配置类
|   |-- SecurityConfig.java      // Spring Security 配置
|   |-- DataSourceConfig.java    // 数据源配置类
|   |-- AppConfig.java          // 应用的其他配置类（例如缓存、消息队列等）
|
|-- util                    // 工具类包，包含常用的工具类
|   |-- DateUtils.java         // 日期工具类
|   |-- StringUtils.java       // 字符串工具类
|   |-- EncryptionUtils.java   // 加密解密工具类


```
---


### 2、多个模块

在 Spring Boot 项目中，可以创建多个模块，并将它们作为子包组织在根包下。每个子包中可以按照“单一模块”结构进行定义，保持模块的独立性和可维护性。项目结构示例如下：
```
com.example.根包
|-- module1               // 第一个模块
|   |-- Application.java  // 启动类
|   |-- controller        // 控制器层
|   |   |-- StudentController.java
|   |   |-- ScoreController.java
|   |-- service           // 业务层
|   |   |-- inter         // 业务层接口
|   |   |-- impl          // 接口实现包
|   |-- repository        // 持久层
|   |-- model             // 模型层
|   |   |-- entity        // 实体类
|   |   |-- dto           // 数据传输对象
|   |   |-- vo            // 视图对象
|-- module2               // 第二个模块
|   |-- Application.java  // 启动类
|   |-- controller        // 控制器层
|   |-- service           // 业务层
|   |-- repository        // 持久层
|   |-- model             // 模型层
|-- module3               // 第三个模块
|   |-- Application.java  // 启动类
|   |-- controller        // 控制器层
|   |-- service           // 业务层
|   |-- repository        // 持久层
|   |-- model             // 模型层
|-- application           // 通用配置或工具包
|   |-- config            // 配置文件
|   |-- util              // 工具类

```

---

