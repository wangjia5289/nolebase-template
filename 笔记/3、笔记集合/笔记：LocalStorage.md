### 1、LocalStorage 概述

**LocalStorage** 是一种基于浏览器的客户端存储技术，用于在浏览器中持久化**字符串键值对**的数据。与 Cookie 不同，LocalStorage 的数据不会随着 HTTP 请求发送到服务器，而是完全存储在客户端浏览器中。它支持存储较大的数据总量（通常为 5MB 到 10MB，具体取决于浏览器），并且数据在浏览器关闭后仍然保留，除非手动清空。

> [!NOTE] 注意事项
> 1. LocalStorage 采用 **同源策略** 进行存储，只有 **协议、主机、端口号** 完全相同时，才能共享 LocalStorage 数据
> 	1. 不同协议之间无法共享 LocalStorage 数据，例如 `http://example.com` 与 `https://example.com`。
> 	2. 不同主机（域名和子域名）之间无法共享 LocalStorage 数据，例如 `example.com` 与 `blog.example.com`。
> 	3. 不同端口号之间无法共享 LocalStorage 数据，例如 `example.com:80` 与 `example.com:8080`。
> 2. LocalStorage 中的数据为持久化存储，不会过期，即使关闭浏览器也不会丢失，除非手动清除
> 3. LocalStorage 的存储上限通常限制在 5MB 到 10MB，超出限制可能会导致浏览器报错
> 4. LocalStorage 由前端通过 JavaScript API 创建和管理，数据直接存储在浏览器的本地文件中。后端无法直接访问 LocalStorage 中的数据，除非前端主动将数据发送到服务器用于持久化存储。
> 5. 虽然 LocalStorage 的数据不会被发送到服务器，但与 SessionStorage 相比，LocalStorage 并不适合存储敏感信息。注意 LocalStorage 安全性较低，不受到 HttpOnly 的保护，容易受到 XSS 攻击，建议在使用 LocalStorage 存储任何数据之前，应该对数据进行加密处理，这样即使数据被窃取，攻击者也无法直接读取内容。
> 6. LocalStorage 不支持事务功能，这意味着在进行多个键值对的操作时，无法保证操作的原子性。
> 7. LocalStorage 不支持索引功能，这意味着无法通过索引来高效查询数据。

---



### 2、LocalStorage 应用场景

LocalStorage 常应用于以下几种场景：
1. <font color="#00b0f0">个性化设置</font>：可以将用户的个性化设置保存在 LocalStorage。例如网站的主题颜色、字体大小、布局、偏好语言等，在下次访问时自动应用。
2. <font color="#00b0f0">离线功能</font>：当在线应用需要支持离线使用时，可以利用 LocalStorage 缓存必要的数据。例如，离线编辑器、地图导航、待办事项列表等。
3. <font color="#00b0f0">数据缓存</font>：对于频繁访问的数据，可以将其缓存到 LocalStorage 中，这样可以减少服务器的负载，加快页面加载速度。例如新闻列表、商品信息、用户资料等。
4. <font color="#00b0f0">游戏开发</font>：在网页游戏中，可以利用 LocalStorage 保存玩家的游戏进度、得分、成就等信息。
5. <font color="#00b0f0">表单数据保存</font>：用户填写的表单数据可以通过 LocalStorage 保存，这样可以防止因意外操作（如页面刷新）导致的数据丢失，减少用户重复填写的麻烦。例如，注册表单、调查问卷等。
6. <font color="#00b0f0">移动 Web 应用</font>：移动 Web 应用可以利用 LocalStorage 存储用户的本地数据，提升应用的响应速度和用户体验。例如，移动购物车、本地联系人列表等

---



### 3、LocalStorage 操作方法

#### 3.1、存储数据
```js
// 存储字符串 
localStorage.setItem('username', 'JohnDoe');

// 存储对象
const user = { name: 'JohnDoe', age: 30 }; 
localStorage.setItem('user', JSON.stringify(user));
```

> [!NOTE] 注意事项
> 1. 前端（JavaScript）存储 LocalStorage 后，浏览器会自动将其保存。
> 2. LocalStorage 只能存储字符串数据类型，要想存储对象，可以将对象转为字符串类型
> 3. 如果 LocalStorage 的键与本地 LocalStorage 的键相同，浏览器会更新该 LocalStorage

---



#### 3.2、读取数据
```
// 读取字符串 
const username = localStorage.getItem('username'); // 'JohnDoe'

// 读取对象 
const user = JSON.parse(localStorage.getItem('user'));
```

---



#### 3.3、删除数据
```
// 删除单个键
localStorage.removeItem('username');

// 清空所有数据
localStorage.clear();
```

---


