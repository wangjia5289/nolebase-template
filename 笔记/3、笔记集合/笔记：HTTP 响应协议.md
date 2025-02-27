### 1、HTTP 响应概述

HTTP 响应协议，包含四部分内容：状态行、响应头、空白行、响应体
```yaml
# 状态行
HTTP/1.1 200 OK
# 响应头
Content-Type: application/json
Content-Length: 34
Date: Sun, 16 Sep 2024 12:00:00 GMT
Server: Apache/2.4.41 (Ubuntu)
# 空白行

# 响应体
{"message": "Hello, world!"}
```

HTTP响应常包含以下几个部分：
1. <font color="#00b0f0">状态行</font>：包含HTTP版本、状态码和状态描述
2. <font color="#00b0f0">响应头</font>：包含关于服务器和响应的附加信息、
3. <font color="#00b0f0">空白行</font>：响应头和响应体之间有一个空白行，表示响应头的结束
4. <font color="#00b0f0">响应体</font>：包含实际的数据内容，如HTML页面、JSON数据、图像等。

---



### 2、状态行

状态行包含三部分内容：HTTP 版本、状态码、状态消息
```
HTTP/1.1 200 OK
```
1. ==HTTP 版本==：表示所使用的 HTTP 协议版本，例如 `HTTP/1.1` 或 `HTTP/2`
2. ==状态码 + 状态消息==：状态码是一个三位数的数字，用于表示服务器对请求的处理结果；状态消息则是对状态码的简要描述，通常以短语形式呈现。
	1. <font color="#00b0f0">1xx（信息性状态码）</font>：
	     - 这些状态码表示请求已被接收，继续处理。
	     - 例如：`100 Continue`。
	  1. <font color="#00b0f0">2xx（成功状态码）</font>：
	     - 表示请求已经成功被服务器接收、理解和处理。
	     - 例如：
	       - `200 OK`：请求成功。
	       - `201 Created`：请求成功并创建了资源。
	       - `204 No Content`：请求成功但没有返回内容。
	  2. <font color="#00b0f0">3xx（重定向状态码）</font>：
	     - 表示需要客户端进一步操作才能完成请求。
	     - 例如：
	       - `301 Moved Permanently`：资源已被永久移动到新位置。
	       - `302 Found`：资源临时移动到新位置。
	       - `304 Not Modified`：资源未被修改，可以使用缓存的版本。
	  3. <font color="#00b0f0">4xx（客户端错误状态码）</font>：
	     - 表示请求有语法错误或无法完成。
	     - 例如：
	       - `400 Bad Request`：请求无效。
	       - `401 Unauthorized`：需要身份验证。
	       - `404 Not Found`：请求的资源未找到。
	  4. <font color="#00b0f0">5xx（服务器错误状态码）</font>：
	     - 表示服务器在处理请求时发生错误。
	     - 例如：
	       - `500 Internal Server Error`：服务器内部错误。
	       - `503 Service Unavailable`：服务器暂时无法处理请求。

---



### 3、响应头

响应头可以包括响应的状态信息、服务器信息、响应内容、缓存控制等。以下是一些常见的HTTP响应头：


1. ==通用响应头==
    1. <font color="#00b0f0">Connection</font>：控制连接的行为，例如：`Connection:keep-alive`
	    1. <font color="#00b0f0">keep-alive</font>：处理完一个请求后，客户端和服务器之间的 TCP 连接不关闭，保持打开状态，以便可以复用该连接进行后续的请求和响应。
	    2. . <font color="#00b0f0">close</font>：完成当前请求/响应后，服务器或客户端希望关闭 TCP 连接，不再复用该连接。
    2. <font color="#00b0f0">Date</font>：服务器生成响应的日期和时间。例如，`Date: Wed, 21 Oct 2015 07:28:00 GMT`。
    3. <font color="#00b0f0">Server</font>：服务器软件的信息。例如，`Server: Apache/2.4.1 (Unix)`。
2. ==实体头（与响应头有关）==
    1. <font color="#00b0f0">Content-Type</font>：**响应体**的 MIME 类型（数据库实际返回的数据类型） 和字符集。例如，`Content-Type: text/html; charset=UTF-8`。
    2. <font color="#00b0f0">Content-Length</font>：响应体的字节长度。例如，`Content-Length: 348`。
    3. <font color="#00b0f0">Content-Encoding</font>：响应体的编码方式（如`gzip`）
    4. <font color="#00b0f0">Content-Language</font>：响应体使用的语言。
    5. <font color="#00b0f0">Content-Location</font>：响应体的实际位置。
3. ==缓存控制头==
    1. <font color="#00b0f0">Cache-Control</font>：指定请求和响应遵循的缓存机制。例如，`Cache-Control: no-cache`。
    2. <font color="#00b0f0">Expires</font>：响应过期的日期和时间。例如，`Expires: Thu, 01 Dec 1994 16:00:00 GMT`。
    3. <font color="#00b0f0">ETag</font>：资源的特定版本标识符。例如，`ETag: "xyzzy"`。
    4. <font color="#00b0f0">Last-Modified</font>：资源的最后修改日期。例如，`Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT`
4. ==重定向头==
    1. <font color="#00b0f0">Location</font>：用于重定向的URL。例如，`Location: http://www.example.org/`。
5. ==安全头==
    1. <font color="#00b0f0">Set-Cookie</font>：设置HTTP cookie。例如，`Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1`。
    2. <font color="#00b0f0">Strict-Transport-Security</font>：强制客户端使用HTTPS。例如，`Strict-Transport-Security: max-age=31536000; includeSubDomains`。
    3. <font color="#00b0f0">X-Content-Type-Options</font>：防止浏览器猜测内容类型。例如，`X-Content-Type-Options: nosniff`。
    4. <font color="#00b0f0">Content-Security-Policy</font>：控制资源加载策略。例如，`Content-Security-Policy: default-src 'self'`。
    5. <font color="#00b0f0">X-Frame-Options</font>：防止点击劫持（clickjacking）。例如，`X-Frame-Options: DENY`。
6. ==其他头==
    1. <font color="#00b0f0">Accept-Ranges</font>：是否支持范围请求。例如，`Accept-Ranges: bytes`。
    2. <font color="#00b0f0">Allow</font>：允许的HTTP方法。例如，`Allow: GET, POST, HEAD`。
    3. <font color="#00b0f0">Vary</font>：指定哪些头部用来决定缓存的版本。例如，`Vary: Accept-Encoding`。
7. ==通用响应头==
	1. <font color="#00b0f0">Connection</font>：控制连接的行为，例如：`Connection: keep-alive`
	2. <font color="#00b0f0">keep-alive</font>：处理完一个请求后，客户端和服务器之间的 TCP 连接不关闭，保持打开状态，以便可以复用该连接进行后续的请求和响应。
	    1. close：完成当前请求/响应后，服务器或客户端希望关闭 TCP 连接，不再复用该连接。
	3. <font color="#00b0f0">Date</font>：服务器生成响应的日期和时间。例如，`Date: Wed, 21 Oct 2015 07:28:00 GMT`。
	4. <font color="#00b0f0">Server</font>：服务器软件的信息。例如，`Server: Apache/2.4.1 (Unix)`。
8. ==实体头（与响应头有关）==
	1. <font color="#00b0f0">Content-Type</font>：**响应体**的 MIME 类型（数据库实际返回的数据类型） 和字符集。例如，`Content-Type: text/html; charset=UTF-8`。
	2. <font color="#00b0f0">Content-Length</font>：响应体的字节长度。例如，`Content-Length: 348`。
	3. <font color="#00b0f0">Content-Encoding</font>：响应体的编码方式（如 `gzip`）。
	4. <font color="#00b0f0">Content-Language</font>：响应体使用的语言。
	5. <font color="#00b0f0">Content-Location</font>：响应体的实际位置。
9. ==缓存控制头==
	1. <font color="#00b0f0">Cache-Control</font>：指定请求和响应遵循的缓存机制。例如，`Cache-Control: no-cache`。
	2. <font color="#00b0f0">Expires</font>：响应过期的日期和时间。例如，`Expires: Thu, 01 Dec 1994 16:00:00 GMT`。
	3. <font color="#00b0f0">ETag</font>：资源的特定版本标识符。例如，`ETag: "xyzzy"`。
	4. <font color="#00b0f0">Last-Modified</font>：资源的最后修改日期。例如，`Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT`。
10. ==重定向头==
    1. <font color="#00b0f0">Location</font>：用于重定向的URL。例如，`Location: http://www.example.org/`。
11. ==安全头==
	1. <font color="#00b0f0">Set-Cookie</font>：设置HTTP cookie。例如，`Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1`。
	2. <font color="#00b0f0">Strict-Transport-Security</font>：强制客户端使用HTTPS。例如，`Strict-Transport-Security: max-age=31536000; includeSubDomains`。
	3. <font color="#00b0f0">X-Content-Type-Options</font>：防止浏览器猜测内容类型。例如，`X-Content-Type-Options: nosniff`。
	4. <font color="#00b0f0">Content-Security-Policy</font>：控制资源加载策略。例如，`Content-Security-Policy: default-src 'self'`。
	5. <font color="#00b0f0">X-Frame-Options</font>：防止点击劫持（clickjacking）。例如，`X-Frame-Options: DENY`。
12. ==其他头==
	1. <font color="#00b0f0">Accept-Ranges</font>：是否支持范围请求。例如，`Accept-Ranges: bytes`。
	2. <font color="#00b0f0">Allow</font>：允许的HTTP方法。例如，`Allow: GET, POST, HEAD`。
	3. <font color="#00b0f0">Vary</font>：指定哪些头部用来决定缓存的版本。例如，`Vary: Accept-Encoding`。

****



### 4、响应体

响应体包含服务器返回的实际数据
```yaml
Content-Type: application/json
Content-Length: 123

{
  "message": "Hello, world!",
  "status": "success"
}
```

> [!NOTE] 注意事项
> 响应体的数据格式（MIME类型）需要通过 `Content-Type` 响应体指定


