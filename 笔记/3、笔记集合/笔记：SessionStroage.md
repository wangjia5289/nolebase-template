### 1、SessionStorage 概述

SessionStorage 是一种在浏览器中存储数据的**内存存储机制**，它允许网页在同一个窗口或标签页中存储**字符串键值对**的数据，并且这些数据会在窗口或标签页关闭后自动清除。与 Cookie 和 LocalStorage 不同，SessionStorage 的数据仅在当前会话中有效，不会持久化到本地存储。

> [!NOTE] 注意事项
> 1. 数据仅在当前窗口或标签页中有效，关闭后数据丢失。不同窗口或标签页之间无法共享数据。此外，不同域名或子域名之间的窗口也无法共享 SessionStorage 数据。同一域名下的不同窗口或标签页也无法共享 SessionStorage 数据。
> 2. 大多数浏览器对 SessionStorage 没有明确的存储上限，但其实际存储能力受限于设备的可用内存。尽量避免存储大量数据，以防占用过多内存，从而导致性能问题或浏览器崩溃。
> 3. SessionStorage 由前端通过 JavaScript API 创建和管理，数据直接存储在浏览器的内存中。后端无法直接访问 SessionStorage 中的数据，除非前端主动将数据发送到服务器用于持久化存储。
> 4. 由于 SessionStorage 的数据不会被发送到服务器，且 SessionStorage 的数据只在当前会话中有效，关闭浏览器后数据就会丢失。这可能更适合存储一些临时性的敏感信息（如一次性验证码、表单输入、用户选择等）。注意 SessionStorage 安全性较低，不受到 HttpOnly 的保护，容易受到 XSS 攻击，建议在使用 LocalStorage 存储任何数据之前，应该对数据进行加密处理，这样即使数据被窃取，攻击者也无法直接读取内容。
> 5. 虽然大多数现代浏览器支持 SessionStorage，但在旧版本浏览器中可能需要使用 Polyfill 或其他替代方案。
> 6. SessionStorage 不支持事务功能，这意味着在进行多个键值对的操作时，无法保证操作的原子性。
> 7. SessionStorage 不支持索引功能，这意味着无法通过索引来高效查询数据。

---



### 2、SessionStorage 应用场景

Session 常应用于以下几种场景：
1. <font color="#00b0f0">临时数据管理</font>：SessionStorage 适用于临时数据管理，如表单数据暂存、购物车功能等。这些数据在会话结束后自动清除，无需额外处理。
2. <font color="#00b0f0">敏感数据存储</font>：由于 SessionStorage 的数据存储在浏览器的内存中，不会被发送到服务器，因此适合存储敏感信息（如一次性验证码）。然而，仍需注意防止 XSS 攻击，确保数据的安全性。

---



### 3、SessionStorage 操作方法
#### 3.1、创建和存储数据
```
// 存储字符串类型的数据 
sessionStorage.setItem('username', 'JohnDoe'); 
sessionStorage.setItem('age', '30'); 

// 存储对象类型的数据（需手动序列化） 
const user = { name: 'JohnDoe', age: 30 }; 
sessionStorage.setItem('user', JSON.stringify(user));
```

---


#### 3.2、读取数据
```
// 读取字符串类型的数据 
const username = sessionStorage.getItem('username'); 
console.log(username); // 输出：JohnDoe 

// 读取对象类型的数据（需手动反序列化） 
const userStr = sessionStorage.getItem('user');
const user = JSON.parse(userStr); 
console.log(user); // 输出：{ name: 'JohnDoe', age: 30 }
```

---



#### 3.3、删除数据
```
// 删除指定键名的数据 
sessionStorage.removeItem('age'); 

// 清除所有数据 
sessionStorage.clear();
```

---

