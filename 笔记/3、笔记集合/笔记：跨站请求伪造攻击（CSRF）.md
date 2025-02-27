### 1、起因

在传统的 Web 应用中，许多请求（如提交表单、点击链接等）是由浏览器自动发送的，且浏览器会 自动携带与目标站点相关的 Cookie，**无论是跨站请求还是本站请求**。

---



### 2、攻击

假设你已经登录了某银行网站 `bank.com` ，并且浏览器保存了你的登录 Cookie。然后，你不小心访问了一个恶意网站 `malicious.com`。恶意网站可能会通过以下方式发起攻击：
```js
<img src="https://bank.com/transfer?to= 攻击者账号&amount=10000">
```

当你的浏览器加载这个恶意网站时，会尝试加载图片。浏览器会自动向 `bank.com` 发送一个请求：
```
GET /transfer?to=攻击者账号&amount=10000 HTTP/1.1
Host: bank.com 
Cookie: sessionId=123456...
```

由于你已经登录了 `bank.com` ，浏览器会自动携带你的 Cookie（包含你的身份信息）到 `transfer` API，如果银行网站的转账接口没有额外的验证（比如 CSRF Token 或二次确认）而只是例如依赖 `JSESSIONID` 验证 `HttpSession` 中的身份信息，该 API 可能会误认为请求是由登录用户发起的，它会认为这是一个合法的转账请求，并执行转账操作。

---



### 3、防范
防范 CSRF 攻击的常见方式主要有以下几种：

1.==使用 `SameSite` Cookie 属性==
`SameSite` 属性用于限制跨站请求时浏览器是否会携带 Cookie。通过设置为 `Strict` 或 `Lax`，可以有效阻止跨站请求携带用户的身份认证 Cookie
- <font color="#00b0f0">Strict</font>：仅允许同站请求携带 Cookie
- <font color="#00b0f0">Lax</font>：允许部分跨站 **GET** 请求携带 Cookie（默认）
- <font color="#00b0f0">None</font>：允许跨站携带 Cookie（必须配合 `Secure` 使用）

==2.使用 CSRF Token==
为每个敏感操作（如表单提交、删除操作等）生成一个 **随机的 CSRF Token**。每次请求时，客户端都需要把这个 Token 作为请求的一部分（通常是表单字段或 HTTP 头部），后端验证这个 Token 是否有效。只有当 Token 匹配时，操作才会被执行。这种方式有效防止恶意站点伪造请求，因为它无法获得有效的 Token。

==3.使用 `Referer` 或 `Origin` Header 验证==
后端可以检查请求的 **`Referer`** 或 **`Origin`** 请求头，确保请求来源于合法的站点，如果请求来源不符合要求，可以拒绝该请求。
- **`Referer`**：标识请求的来源页面。
- **`Origin`**：标识请求的源站点。

==4.确保敏感操作使用 `POST` 请求==
尽量使用 **`POST`** 请求而非 **`GET`** 请求来执行敏感操作，因为 GET 请求可以通过简单的 URL 链接发起，容易受到 CSRF 攻击。

==5.验证 Cookie 与请求头一致==
利用浏览器的 SameSite Cookie 属性，结合 `Access-Control-Allow-Origin` 等 HTTP 头部，确保跨站请求时不能泄露 Cookie。

==6.双重身份验证（2FA)==
对于敏感操作（如资金转账、删除账户等），可以结合双重身份验证（例如短信或邮件验证码、Google Authenticator）来增加安全性，即使攻击者伪造请求，仍然需要有效的第二步认证。

==7.限制请求来源==
对特定敏感请求，后端可以进行 IP 白名单或请求频率限制，增加防护层级，避免恶意请求的爆发。

==8.最小权限原则==
限制系统用户的权限，确保即使账户被冒用，攻击者也无法进行高权限操作。例如，用户默认只能访问与其身份相关的资源，除非特别授权。

---



### 4、总结

CSRF 攻击就像“冒充你的签名”去办理业务。防范的关键是：
1. <font color="#00b0f0">不让攻击者冒充你</font>（使用 CSRF Token、SameSite Cookie）；
2. <font color="#00b0f0">验证请求是否真的由你发起</font>（检查 Referer、Origin 头）；
3. <font color="#00b0f0">增加额外的安全层</font>（如 2FA、频率限制）通过。

---



### 5、反攻