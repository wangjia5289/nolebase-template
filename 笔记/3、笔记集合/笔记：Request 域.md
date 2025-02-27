### 1、Request 域概述

`HttpServletRequest` 对象不仅包含了与 HTTP 请求相关的所有信息，还提供了存储自定义数据的功能。这些数据以 `String` 类型的键值对形式存储，值可以是任何 Java 对象（如 `String`、`Integer`、`List` 或自定义对象等）。由于这些数据仅在当前请求的上下文中有效，因此被称为请求域数据。

---


### 2、Request 域操作方法

#### 2.1、存储数据
```
/**
* 1. setAttribute(String name,Object object)：保存数据
*/

request.setAttribute("userId",12345)
```

---



#### 3.2、获取数据
```
/*
* 1. getAttribute(String name)：获取数据，一般要进行类型转换
*/

Integer userId = (Interger) request.getAttribute("userId");
```

---


