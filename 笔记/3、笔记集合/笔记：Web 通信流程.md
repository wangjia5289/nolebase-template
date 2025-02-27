1. ==客户端请求==
	1. <font color="#00b0f0">用户输入 URL</font>
		1. 当你在浏览器地址栏输入一个 URL（如 `http://www.example.com` ），浏览器会尝试访问这个地址
		2. 如果 URL 是 HTTPS（如 `https://www.example.com` ），浏览器会优先使用加密协议（SSL/TLS）来保护数据传输的安全性
		3. 如果未指定端口号，HTTP 默认使用 80 端口，HTTPS 默认使用 443 端口
	2. <font color="#00b0f0">DNS 查询</font>
		1. DNS（域名系统）就像一个电话簿，将人类易读的域名（如 `example.com` ）转换为计算机易读的 IP 地址（如 `192.168.1.1`）
		2. 浏览器会先检查本地 DNS 缓存（如果之前访问过该域名，可能会有缓存记录），如果没有找到，则会向 DNS 服务器发送请求
		3. 如果 DNS 服务器也没有记录，它会逐级向上查询根域名服务器，直到找到目标域名对应的 IP 地址
	3. <font color="#00b0f0">建立 TCP 连接</font>
		1. 浏览器和服务器之间需要通过 TCP（传输控制协议）建立可靠的数据传输通道
		2. 三次握手是 TCP 连接建立的标准过程：
			1. 浏览器发送一个 SYN 数据包
			2. 服务器收到后，发送一个 SYN-ACK 数据包
			3. 浏览器确认后，发送一个 ACK 数据包
		3. 连接建立后，双方可以开始传输数据
	4. <font color="#00b0f0">发送 HTTP 请求</font>
		1. 浏览器通过 HTTP 协议向前端服务器发送请求
2. ==前端应用==
	1. <font color="#00b0f0">返回响应</font>
		1. 服务器会返回一个 HTTP 响应
	2. <font color="#00b0f0">渲染前端页面</font>
		1. 浏览器收到 HTML 文件后，会按照文档结构逐步解析并渲染页面
			1. HTML 解析：浏览器将 HTML 转换为 DOM 树（文档对象模型）
			2. CSS 加载：浏览器加载 CSS 文件，并根据样式规则为 DOM 元素添加样式
			3. JavaScript 执行：浏览器执行 JavaScript 代码，动态修改页面内容或添加交互功能
		2. 渲染完成后，用户可以看到最终的网页界面
	3. <font color="#00b0f0">发起 API 请求</font>
		1. 在前端应用中，用户操作（如点击按钮、提交表单）可能会触发与后端的 API 请求
		2. 这些请求通常通过 AJAX（Asynchronous JavaScript and XML）实现，允许在不刷新页面的情况下与服务器通信
	4. <font color="#00b0f0">处理 API 响应</font>
		1. 前端应用通过 JavaScript 接收后端返回的数据（通常是 JSON 格式）
		2. 根据业务需求，前端可以更新页面内容、显示提示信息或执行其他操作
3. ==后端应用==
	1. <font color="#00b0f0">接受 API 请求</font>
		1. 后端服务器（如 Node.js 、Python、Java 等）接收到前端发来的 API 请求
	2. <font color="#00b0f0">处理 API 请求</font>
		1. 后端根据请求的内容进行处理
	3. <font color="#00b0f0">生成 API 响应</font>
		1. 后端生成一个 HTTP 响应
	4. <font color="#00b0f0">发送 API 响应</font>
		1. 后端将响应数据通过网络发送回前端应用
		2. 前端接收到响应后，可以根据需要更新页面内容或执行其他操作