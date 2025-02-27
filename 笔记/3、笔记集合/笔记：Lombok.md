# 一、理论

### 1、Lombok 概述
Lombok 是一个 Java 库，旨在通过注解简化代码，减少冗长的样板代码（boilerplate code），从而提高开发效率。它通过注解处理器（annotation processor）在编译时自动生成 getter、setter、toString、equals、hashCode 等方法，避免了手动编写这些常见的代码。

---



# 二、实操

### 1、改 pom（引依赖）
对于Spring Boot 2.1.x及以上版本，可以直接添加如下依赖：
```
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```


### 2、模型类（写模型类/实体类）
```java
@Data
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    
    @EqualsAndHashCode.Exclude
    private int age;
}
```
- <font color="#00b0f0">@Data</font>：会生成 `getter`、`setter`、`toString()`、`equals()` 和 `hashCode()` 方法。
- <font color="#00b0f0">@Getter</font>：会生成 `getter` 方法
- <font color="#00b0f0">@Setter</font>：会生成 `setter` 方法
- <font color="#00b0f0">@NoArgsConstructor</font>：会生成无参构造器。
- <font color="#00b0f0">@AllArgsConstructor</font>：会生成有参构造器
- <font color="#00b0f0">@EqualsAndHashCode.Exclude</font>：如果你不想让某些字段参与 `quals()` 比较，可以用此来排除这些字段
