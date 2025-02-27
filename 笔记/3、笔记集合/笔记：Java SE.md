# 一、理论 + [导图：Java SE](../5、导图集合/导图：JavaSE.xmind)


## List

以下是对你提供内容的格式优化版本：

---

### 在Java中，`Arrays.asList()`是一个常用的数组转集合方法，但它返回的`List`对象与常规`java.util.ArrayList`存在显著差异。以下是结合官方文档和开发实践的全面解析：

---

### 一、`Arrays.asList()`的本质

1. **返回类型**  
    `Arrays.asList()`返回的是`java.util.Arrays.ArrayList`（静态内部类），而非`java.util.ArrayList`
    
    ```java
    List<String> list = Arrays.asList("A", "B", "C"); // 固定大小的List 
    ```
    
2. **核心特性**
    
    - **不可变结构**：不支持`add()`、`remove()`、`clear()`等修改操作，调用会抛出`UnsupportedOperationException`
    - **视图机制**：列表与原始数组双向绑定，修改数组元素会同步到List
    - **基本类型陷阱**：传入基本类型数组（如`int[]`）会被视为单个元素，需用包装类数组解决
        
        ```java
        int[] arr = {1,2,3};
        List<int[]> wrongList = Arrays.asList(arr); // size=1 
        Integer[] arr2 = {1,2,3};
        List<Integer> correctList = Arrays.asList(arr2); // size=3 
        ```
        

---

### 二、List基础操作方法

#### 1. 创建List

|方法|示例|可变性|
|---|---|---|
|`Arrays.asList()`|`List<String> list = Arrays.asList("A","B")`|不可变|
|`new ArrayList()`|`List<String> list = new ArrayList<>()`|可变|
|包装转换|`List<String> list = new ArrayList<>(Arrays.asList("A","B"))`|可变|

#### 2. 增删改查操作

|方法|作用|可变List支持|Arrays.asList支持|
|---|---|---|---|
|`add(E e)`|添加元素|✔️|❌（抛异常）|
|`remove(int index)`|移除元素|✔️|❌|
|`set(int index, E e)`|修改元素|✔️|✔️（原始数组同步修改）|
|`get(int index)`|获取元素|✔️|✔️|
|`size()`|获取元素数量|✔️|✔️|

---

### 三、List高级操作方法

#### 1. 遍历与过滤

```java
// 遍历（所有List支持）
list.forEach(System.out::println);

// 流式过滤（Java 8+）
List<String> filtered = list.stream()
    .filter(s -> s.startsWith("A"))
    .collect(Collectors.toList());
```

#### 2. 排序与转换

```java
Collections.sort(list); // 自然排序
list.sort(Comparator.reverseOrder()); // 自定义排序

// 转数组（可变List专用）
String[] array = list.toArray(new String[0]);
```

#### 3. 批量操作

```java
list.addAll(Arrays.asList("D","E")); // 可变List支持
list.retainAll(Arrays.asList("A","B")); // 交集保留
```

---

### 四、实用建议

#### 1. 可变List转换技巧

```java
List<String> mutableList = new ArrayList<>(Arrays.asList("A","B"));
```

#### 2. 基本类型数组处理

- Java 8+流式方案：
    
    ```java
    int[] arr = {1,2,3};
    List<Integer> list = Arrays.stream(arr).boxed().collect(Collectors.toList());
    ```
    

#### 3. 防御性编程

```java
// 避免直接修改Arrays.asList的结果 
List<String> safeList = Collections.unmodifiableList(Arrays.asList("A","B"));
```

---

### 五、完整操作速查表

|操作类型|方法示例|适用场景|
|---|---|---|
|创建|`List.of("A","B")` (Java 9+)|不可变集合初始化|
|查询|`contains(Object o)`|元素存在性检查|
|批量操作|`subList(0,2).clear()`|局部数据清除（影响原List）|
|转换|`List.copyOf(collection)`|创建不可修改副本|

---

完整API文档建议参考Oracle官方Javadoc。

---

这样分段与表格更加清晰易读，帮助理解不同方法与操作的区别和适用场景。如果有任何其他调整需求，随时告诉我！