### 1、HTTP 请求概述

HTTP 请求协议，包含四部分内容：请求行、请求头、空白行、请求体

```yaml
# 请求行
POST /submit HTTP/1.1
# 请求头
Host: www.example.com
User-Agent: Mozilla/5.0
Content-Type: application/json
Content-Length: 34
# 空白行

# 请求体
{"name": "John", "age": 30}
```

HTTP请求通常包含以下几个部分：
1. <font color="#00b0f0">请求行 </font>：包含请求方法、请求目标和HTTP版本
2. <font color="#00b0f0">请求头 </font>：包含关于客户端和请求的附加信息
3. <font color="#00b0f0">空白行</font>：请求头和请求体之间有一个空行，表示请求头的结束
4. <font color="#00b0f0">请求体</font>：仅在某些请求方法（如 POST、PUT、PATCH）中发送，包含要发送到服务器的数据。

---



### 2、请求行

请求行包含三部分内容：请求方法、请求目标、HTTP 版本

```yaml
GET /servlet05/getServlet?username=lucy&userpwd=1111 HTTP/1.1
 |                            |                            |
请求方法                   请求目标                        HTTP版本
```

1. ==请求方法==：请求方法指明了客户端希望对资源执行的操作
    1. <font color="#00b0f0">GET</font>：请求指定的资源。通常用于获取数据，不应有副作用（即不改变服务器状态）。
    2. <font color="#00b0f0">POST</font>：提交数据给服务器，通常用来提交表单或上传文件，可能会导致服务器状态改变。
    3. <font color="#00b0f0">PUT</font>：更新指定的资源，通常用于替换资源的当前表示。
    4. <font color="#00b0f0">DELETE</font>：删除指定的资源。
    5. <font color="#00b0f0">HEAD</font>：类似于GET，但服务器只返回响应头，不返回响应体，常用于检查资源的有效性。
    6. <font color="#00b0f0">OPTIONS</font>：描述目标资源的通信选项，通常用于CORS（跨源资源共享）请求。
    7. <font color="#00b0f0">PATCH</font>：对资源进行部分修改。
2. ==请求目标==：请求目标可以是以下几种形式
    1. <font color="#00b0f0">绝对URI</font>：如 `http://www.example.com/index.html`，包含协议、主机名和路径。
    2. <font color="#00b0f0">相对URI</font>：如 `/index.html`，相对于请求的主机和协议。
    3. <font color="#00b0f0">星号（*）</font>：用于OPTIONS请求，表示请求的资源不特指任何URI。
3. ==HTTP版本==：HTTP版本指明了所使用的HTTP协议的版本。
    1. <font color="#00b0f0">HTTP/1.1</font>：当前使用最广泛的版本，支持持久连接和分块传输编码等特性。
    2. <font color="#00b0f0">HTTP/2</font>：相对于1.1版本，支持多路复用、头部压缩等，提高性能。
    3. <font color="#00b0f0">HTTP/3</font>：基于QUIC协议，进一步提高性能，特别是在高延迟网络中。

> [!NOTE] 注意事项
> Cookie 位于请求头中，而非请求行中。

---



### 3、请求头

请求头包含许多不同的字段，每个字段都有特定的作用。以下是一些常见的HTTP请求头：
1. ==通用请求头==
    1. <font color="#00b0f0">Host</font>：指定服务器的域名和端口号，例如：`Host: www.example.com:8080`。
    2. <font color="#00b0f0">User-Agent</font>：发送请求的客户端软件信息（如浏览器类型及版本）。
    3. <font color="#00b0f0">Accept</font>：
        1. 告诉服务器客户端可以处理哪些内容类型（MIME 类型），服务器会根据 `Accept` 头选择合适的内容类型响应，如果服务器无法提供合适的格式，通常会返回 406（Not Acceptable）状态码。
        2. 前端可以明确设置 `Accept` 头，例如可以使用 AJA，轻松地设置。
        3. 如果前端未明确设置 `Accept` 头，浏览器会发送默认的 `Accept` 值，一般为：`Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8`。
    4. <font color="#00b0f0">Accept-Encoding</font>：客户端可接受的内容编码（如 `gzip`, `deflate`）。
    5. <font color="#00b0f0">Accept-Language</font>：客户端可接受的语言。
    6. <font color="#00b0f0">Cache-Control</font>：指定请求和响应遵循的缓存机制。
    7. <font color="#00b0f0">Connection</font>：控制连接的选项（如 `keep-alive`）。
    8. <font color="#00b0f0">Cookie</font>：包含发送到服务器的cookie数据，例如：`Cookie: name=value; name2=value2; ...`。
    9. <font color="#00b0f0">Referer</font>：指明请求来源的URI。
    10. <font color="#00b0f0">Authorization</font>：包含用于认证的凭证（如 Basic, Bearer tokens）。
    11. <font color="#00b0f0">Upgrade-Insecure-Requests</font>：表示客户端希望升级到HTTPS。
2. ==实体头（与请求体有关）==
    1. <font color="#00b0f0">Content-Type</font>：**请求体**的MIME类型和字符集，例如：`Content-Type: text/html; charset=utf-8`。
    2. <font color="#00b0f0">Content-Length</font>：请求体的字节长度，例如：`Content-Length: 348`。
    3. <font color="#00b0f0">Content-Encoding</font>：请求体的编码方式。
    4. <font color="#00b0f0">Content-Language</font>：请求体使用的语言。
    5. <font color="#00b0f0">Content-Location</font>：请求体的实际位置。
3. ==条件请求头==
    1. <font color="#00b0f0">If-Match</font>：仅当实体标记（ETag）匹配时才执行请求。
    2. <font color="#00b0f0">If-None-Match</font>：仅当实体标记（ETag）不匹配时才执行请求。
    3. <font color="#00b0f0">If-Modified-Since</font>：仅当资源自指定时间之后修改过才执行请求。
    4. <font color="#00b0f0">If-Unmodified-Since</font>：仅当资源自指定时间之后未修改才执行请求。
4. ==请求控制头==
    1. <font color="#00b0f0">Range</font>：请求获取部分的内容。
    2. <font color="#00b0f0">Expect</font>：指示服务器行为的期望（如 100-continue）。
    3. <font color="#00b0f0">TE</font>：传输编码的扩展（如 trailers, chunked）。
5. ==代理请求头==
    1. <font color="#00b0f0">Via</font>：告知通过哪些代理服务器。
    2. <font color="#00b0f0">Forwarded</font>：指定代理服务器转发的请求信息。
    3. <font color="#00b0f0">X-Forwarded-For</font>：记录原始客户端的IP地址。
    4. <font color="#00b0f0">X-Forwarded-Proto</font>：记录原始协议（HTTP或HTTPS）。

---



### 4、请求体（正文）
   
请求体主要用于携带需要发送到服务器的数据，在一些特定的HTTP方法中使用，如 POST、PUT 和 PATCH 等
```yaml
Content-Type: application/json
Content-Length: 45

{
    "username": "john_doe",
    "password": "securepassword123"
}
```

> [!NOTE] 注意事项
> 请求体的数据格式（MIME类型）需要通过`Content-Type`请求头指定

---



