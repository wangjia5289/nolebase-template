### 1、Cookie 概述

Cookie 是由服务器发送到用户浏览器并保存的**字符串键值对**的小型文本数据，浏览器在后续请求中自动回传在该域名和路径有效的 Cookie，使服务器能识别用户或维护会话状态。

> [!NOTE] 注意事项
> 1. Cookie 采用 **域名+路径** 进行存储，只有当请求的 **域名和路径匹配** 时， 浏览器才会在后续请求中自动携带有效的 Cookie
> 	1. 子域名中 Cookie 有效，例如 `example.com` 设置的 Cookie，可被 `sub.example.com` 访问。
> 	2. 子路径下 Cookie 有效，例如 `/app` 设置的 Cookie，在 `/app/dashboard` 依然有效，但 `/admin` 无法访问。
> 2. Cookie 可分为会话 Cookie 和持久 Cookie：会话 Cookie 在浏览器关闭后失效，而持久 Cookie 在设定的过期时间后失效。Cookie 也可手动删除。
> 3. 每个 Cookie 的大小通常限制在 4KB 左右，这意味着存储较大数据时，可能需要拆分为多个 Cookie，或选择其他存储方式。
> 4. 大多数浏览器对单个域名下的 Cookie 数量有限制，通常在 20 至 50 个之间。
> 5. 默认情况下，Cookie 不会加密，容易受到 XSS 和 CSRF 攻击，应结合安全策略防范。
> 6. Cookie 可用于跟踪用户行为，可能引发隐私问题。建议在使用 Cookie 前明确告知用户用途，并征得同意。
> 7. 每次 HTTP 请求都会携带相关的 Cookie，增加请求大小和处理时间。在高流量场景下，这可能影响性能，建议定期清理过期或无用的 Cookie 以降低负担。
> 8. Cookie 可由前端或后端创建，由浏览器存储。前端创建的 Cookie 直接存储在浏览器中，而后端创建的 Cookie 需要通过 HTTP 响应发送至浏览器后才能存储。
> 9. LocalStorage 不支持事务，无法批量操作多个键值对
> 10. LocalStorage 不支持索引，无法高效查询数据

---



### 2、Cookie 应用场景

Cookie 常应用于以下几种场景：
1. <font color="#00b0f0">用户身份验证</font>：在 Cookie 中存储 `JSESSIONID` 并将用户的身份凭证保存在 `JSESSIONID` 对应的 `HttpSession` 中，从而在后续的请求中，被服务器识别用户的身份，保持登录状态。
2. <font color="#00b0f0">个性化设置</font>：可以将用户的个性化设置保存在 Cookie，例如网站的主题颜色、字体大小、布局、偏好语言等，在下次访问时自动应用。
3. <font color="#00b0f0">广告跟踪与用户行为跟踪</font>：广告公司使用 cookie 来跟踪用户的浏览行为，记录访问过的网站、点击的广告、停留的时间等，根据这些数据投放更相关的广告

---



### 3、Cookie 工作原理

1. ==首次请求==
	- <font color="#00b0f0">用户行为</font>：用户首次访问网站（如 `https://example.com`），浏览器发送无 Cookie 的 HTTP 请求到服务器。
	- <font color="#00b0f0">服务器响应</font>：服务器检测请求头中无有效 Cookie（如 `JSESSIONID`、`PHPSESSID` 等，具体名称依赖服务端框架），生成唯一会话标识符（如 `session_id=abc123`）和关联的会话数据（如 `HttpSession` ，存储于服务器内存/数据库），并通过响应头的 `Set-Cookie` 字段返回标识符给浏览器。

2. ==浏览器存储 Cookie==
	- <font color="#00b0f0">解析和存储</font>：浏览器解析 `Set-Cookie` 头部，按**域名**（Domain）、**路径**（Path）分类存储 Cookie。这里要注意区分会话 Cookie（未设 `Expires`/`Max-Age`，关闭浏览器即失效）和持久 Cookie（设有效期，存于磁盘）。

3. ==后续请求==
	- <font color="#00b0f0">自动携带 Cookie</font>：浏览器发送请求时，会检查本地 Cookie 的 `Expires`/`Max-Age`，自动清理过期 Cookie 并且会自动匹配符合条件的 Cookie，并将其附加到 HTTP 请求中，发送到服务器。
	- <font color="#00b0f0">浏览器存储和更新 Cookie</font>：若服务器返回 Cookie（通常由开发者设置）时，浏览器会将其存储。如果响应中的 Cookie 名称与本地 Cookie 的名称相同，浏览器会更新该 Cookie；否则，浏览器会将其作为一个新 Cookie 存储。

> [!NOTE] 补充：Cookie 的形式
```
Set-Cookie: session_id=abc123; Expires=Wed, 21 Oct 2024 07:28:00 GMT; Path=/; Domain=.example.com; Secure; HttpOnly; SameSite=Lax
```

---



### 4、Cookie 的关键属性
| **属性**          | **作用**                                                                                                                                                                                                  |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name & Value    | Cookie 的名称和值（必填，通常需要进行编码以避免特殊字符）。                                                                                                                                                                       |
| Expires/Max-Age | 设置 Cookie 的过期时间：`Expires` 为具体的过期日期，`Max-Age` 为存活的秒数。如果未设置，则默认为会话 Cookie（浏览器关闭时失效）。                                                                                                                      |
| Domain          | 指定 Cookie 的有效域名，只有在该域名及其子域名下，浏览器才会发送携带这些 Cookie 的 HTTP 请求到服务端。Domain 默认为当前域名。                                                                                                                           |
| Path            | 指定 Cookie 的有效路径，只有在该路径及其子路径下，浏览器才会发送携带这些 Cookie 的 HTTP 请求到服务端。Path 默认为根路径（"/"），意味着整个网站都可以访问这个 cookie。                                                                                                   |
| Secure          | 设置此标志后，Cookie 只会通过 HTTPS 协议发送，防止在不安全的 HTTP 连接中被窃取                                                                                                                                                       |
| HttpOnly        | 设置此标志后，JavaScript 无法访问该 Cookie，主要用于防范 [[笔记：跨站脚本攻击（XSS）\|XSS]] 攻击窃取 Cookie。                                                                                                                              |
| SameSite        | 控制浏览器在跨站发送 HTTP 请求到本站时，是否携带本站的 Cookie，主要用于防范 [[笔记：跨站请求伪造攻击（CSRF）\|CSRF ]] 攻击：  <br>- `Strict`：仅同站请求携带。  <br>- `Lax`：允许部分跨站 <font color="#00b0f0">GET</font> 请求（默认）。  <br>- `None`：允许跨站携带（需配合 `Secure`）。 |

> [!NOTE] 有了 Domain 和 Path，为什么还要有 SameSite？
> **Domain** 和 **Path** 控制了浏览器在发送 HTTP 请求时，在哪些域名和路径下会携带 Cookie。然而，在跨站请求这些域名和路径时，浏览器依然会自动发送携带 Cookie 的请求。
> 
> 例如，恶意网站向本站（如转账 API 或删除账户 API）发起请求时，浏览器会自动携带本站的 **JSESSIONID**。如果身份认证信息存储在 **HttpSession** 中，并通过 **Session** 进行验证，那么恶意请求会被误认为是合法用户发起的请求，从而通过 API 的身份验证并触发 API 操作，执行恶意行为。
> 
> **SameSite** 就是用来控制跨站请求时，是否携带本站的 Cookie，从而防止此类攻击。

---



### 5、Cookie 操作方法

#### 5.1、创建 Cookie

==1.前端创建 Cookie==
```js
/** 
 * 创建 Cookie
 * 
 * expires：设置最大存活时间
 * domain：设置域名范围
 * path：设置路径限定
 * secure：设置安全标志
 */
 
const date = new Date();
date.setTime(date.getTime() + (1 * 24 * 60 * 60 * 1000)); // 1 天
document.cookie = `username=JohnDoe; expires=${date.toUTCString()}; domain=.example.com; path=/; secure;`;
```

==2.后端创建 Cookie==
```java
/** 
 * 创建 Cookie
 * 
 * setMaxAge：设置最大存活时间
 * setDomain：设置域名范围
 * setpath：设置路径限定
 * setsecure：设置安全标志
 * setHttpOnly：设置 HttpOnly 标志
 */

Cookie ck = new Cookie("username", "JohnDoe"); // 键值对都要是 String 类型

// 最大存活时间
ck.setMaxAge(86400);

// 域名范围
ck.setDomain(".example.com");

// 路径限定
ck.setPath("/");

// 安全标志
ck.setSecure(true);

// HttpOnly 标志
ck.setHttpOnly(true);
```

> [!NOTE] 注意事项
> `HttpOnly` 标志只能通过后端设置，前端无法直接设置。

---



#### 5.2、存储 Cookie
==1.前端存储 Cookie==
```js
document.cookie = "username=JohnDoe; path=/; expires=Fri, 31 Dec 2024 23:59:59 GMT";
```

==2.后端存储 Cookie==
```java
response.addCookie(ck);
```

> [!NOTE] 注意事项
> 1. 前端（JavaScript）创建 Cookie 后，浏览器会自动将其保存。
> 2. 后端创建的 Cookie，需要将 Cookie 写入到响应中，返回浏览器被解析后才能被存储
> 3. 如果 Cookie 名称与本地 Cookie 的名称相同，浏览器会更新该 Cookie；

---



#### 5.3、读取 Cookie

由于 Cookie 是跟随大部队返回服务器，要先将 HTTP 请求中所有 Cookie 获取，再通过遍历根据单个 Cookie ，获取 Cookie 和 Cookie 相关信息
```java
public class SaServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
        Cookie[] cookies = request.getCookies();
        if (cookies != null) {
            for (Cookie cookie : cookies) {
                if ("username".equals(cookie.getName())) {
                    String name = cookie.getName(); // 获取名称
                    String value = cookie.getValue(); // 获取值
        }
    }
}
```

---



#### 5.4、获取 Cookie 的关键属性
```java
/** 
 * 获取 Cookie 的关键属性
 * 
 * getName：获取名称
 * getValue：获取值
 * getMaxAge：获取最大存活时间
 * getDomain：获取域名范围
 * getPath：获取路径限定
 * getSecure：获取安全标识
 * isHttpOnly：获取 HttpOnly 标识
 */
 
String name = cookie.getName();
String value = cookie.getValue(); 
int maxAge = ck.getMaxAge();
String domain = ck.getDomain();
String path = ck.getPath();
boolean isSecure = ck.getSecure();
boolean isHttpOnly = ck.isHttpOnly();
```

---



#### 3.5、释放 Cookie

==1.默认情况==
会话 Cookie：即未设置 `Expires/Max-Age` 的 Cookie，在浏览器关闭后会被清楚
持久 Cookie：即设置了最大存活时间的 Cookie，在到达时间后被清除

==2.直接释放 Cookie==
我们可以利用 Cookie 的更新机制，创建一个与要删除 Cookie 同名的 Cookie，将最大存活时间设置为 0，然后将此 Cookie 添加到响应中，让浏览器释放 Cookie。
```java
// 创建同名 Cookie
Cookie ck = new Cookie("myCookie", null);

// 最大存活时间为 0
ck.setMaxAge(0);

// 添加到响应中
response.addCookie(cookie);
```
---


