### 1、URL

URL（统一资源定位符），俗称网址，一个完整的 URL 示例可能是：

```bash
https://www.example.com:443/folder/file.html?key1=value1&key2=value2#section1
```

包括以下几部分：

1. **协议（Scheme）**：指定访问资源所使用的协议，例如 http、https、ftp 等。

2. **主机名（Host）**：指定资源所在的服务器，通常是域名（如 www.example.com）或 IP 地址。

3. **端口号（Port）**：可选部分，指定访问服务器的端口，默认情况下，HTTP 使用 80，HTTPS 使用 443。

4. **资源路径（Path）**：指定服务器上资源的具体位置，例如 /folder/file.html。

5. **查询字符串（Query）**：可选部分，通常用于传递参训参数，格式为 `key1=value1&key2=value2`。

6. **锚点（Fragment）**：可选部分，指定文档内的某个位置，格式为` #section1` 。

---



### 2、RESTful URL

==1.RESTful URL 概述==
RESTful URL 是一种符合 REST（Representational State Transfer）架构风格的 URL 设计方式，旨在通过简单、统一的 HTTP 请求来操作资源。

简而言之，RESTful URL 的设计核心是 **简洁明了**。它**要求 API 的 URL 结构清晰、直观，能够自然地反映资源的含义和层级关系**。同时，通过**合理使用 HTTP 动词（如 GET、POST、PUT、DELETE）来执行增删改查操作**，确保开发者和使用者能够一目了然地理解每个操作的目的。

==2.RESTful URL 设计原则==
1. <font color="#00b0f0">省略动词</font>：URL 中不应包含动词，操作类型通过 HTTP 方法来区分。例如，不应使用 `/getUsers`，而应该使用 `/users`，并通过 HTTP 方法来定义操作类型：
    - **GET**：读取资源。例如，获取所有用户信息：`GET /users`；获取特定用户信息：`GET /users/{id}`。
    - **POST**：创建资源。例如，创建新用户：`POST /users`。
    - **PUT**：更新资源。例如，更新用户信息：`PUT /users/{id}`。
    - **DELETE**：删除资源。例如，删除特定用户：`DELETE /users/{id}`。
2. <font color="#00b0f0">使用复数形式</font>：资源名称通常使用复数形式来表示资源集合。例如，使用 `/users` 而不是 `/user` 来表示所有用户。
3. <font color="#00b0f0">层级结构清晰</font>：RESTful URL 设计应清晰反映资源之间的层级关系。例如，要访问某个用户的帖子资源，可以设计为：
    - `/users/{id}/posts`：表示特定用户的所有帖子。
    - `/users/{id}/posts/{postId}`：表示特定用户的某个帖子。

==3.RESTful URL 举例==
1. 获取所有书籍：`GET /books`
2. 获取特定书籍：`GET /books/{id}`
3. 创建新书籍：`POST /books`
4. 更新书籍信息：`PUT /books/{id}`
5. 删除书籍：`DELETE /books/{id}`

