# 绑定域名

要将阿里云注册的域名绑定到 Vercel 上的项目，您需要按照以下步骤操作：

### 1. **在 Vercel 中配置自定义域名**

1. 登录 Vercel 控制台。
    
2. 进入您要绑定域名的项目页面。
    
3. 在项目的 **Settings**（设置）中，选择 **Domains**（域名）。
    
4. 点击 **Add**（添加）按钮。
    
5. 输入您在阿里云注册的域名（例如 `example.com`），然后点击 **Add**。
这三个选项分别表示如何配置您的域名和子域名的使用方式，具体区别如下：

1. **Add [www.wangjia.xin](http://www.wangjia.xin/) and redirect wangjia.xin to it (Recommended)**
    
    - **含义**：选择此项后，`www.wangjia.xin` 将作为主域名，裸域名 `wangjia.xin` 会自动重定向到 `www.wangjia.xin`。
    - **适用场景**：如果您希望用户始终通过 `www.wangjia.xin` 访问网站，这是推荐选项。这样有助于统一域名访问，避免裸域名和子域名的混乱。
2. **Add wangjia.xin and redirect [www.wangjia.xin](http://www.wangjia.xin/) to it**
    
    - **含义**：选择此项后，裸域名 `wangjia.xin` 将作为主域名，而 `www.wangjia.xin` 会自动重定向到 `wangjia.xin`。
    - **适用场景**：如果您更倾向于使用裸域名（没有 `www` 的形式），选择此项。
3. **Add wangjia.xin**
    
    - **含义**：选择此项后，您仅添加了裸域名 `wangjia.xin`，而不处理 `www.wangjia.xin` 的解析和重定向。
    - **适用场景**：如果您只想使用裸域名 `wangjia.xin`，且不希望用户访问 `www.wangjia.xin`。

---

### **建议选择**

根据通用推荐，您可以选择第一个选项（**Add [www.wangjia.xin](http://www.wangjia.xin/) and redirect wangjia.xin to it**），这样用户无论输入 `wangjia.xin` 还是 `www.wangjia.xin`，都能被正确引导到 `www.wangjia.xin`，更有利于 SEO 和访问一致性。

如果您更喜欢裸域名，可以选择第二个选项。

### **下一步操作**

无论选择哪个，Vercel 会为您提供相应的 DNS 解析记录（CNAME 或 A 记录），然后您需要按照上述记录在阿里云的 DNS 解析中进行配置。

    Vercel 会为您提供一个 CNAME 或 A 记录的值，这将用于在阿里云配置域名解析。
    

### 2. **在阿里云配置域名解析**

1. 登录阿里云控制台，进入 **云解析 DNS** 管理页面。
    
2. 选择您的域名，点击进入管理界面。
    
3. 在 **解析设置** 页面，添加一个新的记录：
    
    - 如果 Vercel 提供的是 **CNAME** 记录，添加一个 **CNAME** 记录。
        - **主机记录**：您要绑定的子域名（例如 `www`）。
        - **记录类型**：选择 `CNAME`。
        - **记录值**：填写 Vercel 提供的 CNAME 地址（通常是 `cname.vercel-dns.com` 或类似的地址）。
        - **TTL**：设置为默认值或 600 秒。
    - 如果 Vercel 提供的是 **A 记录**，则添加一个 **A** 记录。
        - **主机记录**：您要绑定的子域名（例如 `www`）。
        - **记录类型**：选择 `A`。
        - **记录值**：填写 Vercel 提供的 IP 地址。
        - **TTL**：设置为默认值或 600 秒。
4. 保存配置。
    

### 3. **验证域名配置**

1. 等待一段时间（通常需要几分钟到 1 小时），然后返回 Vercel 控制台查看域名状态。
2. 如果配置正确，Vercel 会显示该域名的状态为 **Verified**（已验证）。
3. 您可以通过访问 `https://yourdomain.com` 来确认是否正确绑定。

### 4. **使用 HTTPS**

Vercel 会自动为您的自定义域名配置 SSL/TLS 证书，您不需要手动配置 HTTPS。只要域名解析生效并被 Vercel 验证，Vercel 会自动为其启用 HTTPS。

### 额外提示：

- **DNS 解析生效时间**：DNS 记录的生效时间可能会因网络延迟而有所不同，通常需要 5 分钟到 1 小时。
- **子域名配置**：如果您只想绑定一个子域名（例如 `www.example.com`），可以在阿里云 DNS 设置中添加相应的 CNAME 记录。如果要绑定裸域名（例如 `example.com`），建议将其指向 Vercel 提供的 IP 地址。

通过以上步骤，您可以顺利将阿里云的域名绑定到 Vercel 项目上。












# 原理
将阿里云注册的域名绑定到 Vercel 上的原理涉及两个主要部分：**DNS 解析**和**域名验证**。具体操作流程如下：

### 1. 配置自定义域名（Vercel）

在 Vercel 中，您首先需要配置自定义域名。Vercel 为您的项目提供一个特定的 DNS 记录（通常是 **CNAME 记录** 或 **A 记录**），这将用于将您的域名与 Vercel 项目绑定。这些记录的作用如下：

- **CNAME 记录**：用于将您的子域名（如 [www.example.com）指向](http://www.example.xn--com)-jl1gl60f/) Vercel 提供的域名（通常是 `cname.vercel-dns.com`）。这意味着，当访问您的子域名时，DNS 服务器会将请求转发到 Vercel。
- **A 记录**：如果提供的是 A 记录，您需要将域名指向一个 Vercel 提供的 IP 地址，这通常用于绑定裸域名（如 example.com）。

### 2. 配置域名解析（阿里云）

域名解析是将您域名的访问请求与实际服务器的 IP 地址或主机名关联起来。在阿里云中，您需要在 DNS 管理控制台中配置解析记录，将您的域名指向 Vercel 提供的 CNAME 或 A 记录。

- **CNAME 记录**：当您使用 CNAME 记录时，DNS 系统会将请求重定向到 Vercel 的服务器（例如 `cname.vercel-dns.com`）。这样，任何访问您的域名的请求都会被转发到 Vercel 托管的应用。
- **A 记录**：如果您使用 A 记录，则会直接将域名解析到一个或多个 IP 地址上，这通常是 Vercel 提供的负载均衡 IP 地址，用于将请求路由到合适的服务器。

### 3. DNS 生效与验证

DNS 配置完成后，您需要等待 DNS 记录生效。DNS 缓存和传播时间可能从几分钟到一个小时不等，这取决于网络的延迟和 TTL（Time To Live）设置。

- 一旦 DNS 解析生效，Vercel 会验证您的域名配置是否正确。如果成功，Vercel 会显示域名为 "Verified"（已验证）状态，表示您的域名与 Vercel 项目成功绑定。

### 4. HTTPS 配置

Vercel 会自动为您的域名启用 SSL/TLS 证书，这意味着您的网站将支持 HTTPS。SSL/TLS 证书用于加密客户端与服务器之间的通信，确保数据传输的安全性。在 Vercel 中，域名一旦验证成功，HTTPS 会自动配置，无需您手动操作。

### 总结：

- **CNAME 记录**用于将子域名指向 Vercel 提供的地址。
- **A 记录**用于将裸域名指向 Vercel 提供的 IP 地址。
- DNS 记录在阿里云配置后，需要时间生效。
- Vercel 会自动为您的自定义域名配置 HTTPS。

通过这种方式，您可以将阿里云域名与 Vercel 项目连接，确保您的网站能够通过自定义域名访问，并且具备 HTTPS 安全连接。



# /blog

要将 `wangjia.xin/blog` 绑定到您在 Vercel 上的项目，并确保在该路径下正确显示您的内容，可以按照以下步骤操作：

### 1. 在 Vercel 上配置自定义域名

首先，您需要在 Vercel 中配置 `wangjia.xin` 域名，确保 Vercel 可以识别并处理该域名。

1. **登录 Vercel 控制台**，并进入您的项目。
2. 在项目的 **Settings（设置）** 页面，选择 **Domains（域名）**。
3. 点击 **Add** 按钮，输入您的主域名 `wangjia.xin`（您可以使用裸域名或者子域名，比如 `www.wangjia.xin`）。
4. Vercel 会提供一个 CNAME 记录或者 A 记录，您将需要在阿里云 DNS 控制台中添加该记录。

### 2. 在阿里云配置域名解析

在阿里云的 **DNS 控制台** 中，您需要配置正确的 DNS 解析，使得访问 `wangjia.xin` 的请求能够到达 Vercel 的服务器。

1. 登录阿里云控制台，进入 **云解析 DNS 管理** 页面。
2. 选择您的域名 `wangjia.xin`，进入 **解析设置** 页面。
3. 配置以下记录：
    - **CNAME 记录**：
        - 主机记录：`www`（如果你希望通过 `www.wangjia.xin` 访问）或者直接使用裸域名（即 `wangjia.xin`）。
        - 记录类型：`CNAME`。
        - 记录值：填写 Vercel 提供的 CNAME 地址，通常是 `cname.vercel-dns.com`。
        - TTL：设置为默认值或 600 秒。
    - 如果 Vercel 提供的是 **A 记录**（针对裸域名），您可以使用 A 记录来将域名解析到 Vercel 提供的 IP 地址。

### 3. 配置 `wangjia.xin/blog` 路径

要将 `wangjia.xin/blog` 路径绑定到 Vercel 上的项目，您有以下几种选择，具体方法取决于您的 Vercel 项目的结构和需要：

#### 方法 1：在 Vercel 项目中配置 `/blog` 路径

如果您使用的框架是 **Next.js** 或者其他支持基于路径的路由框架，您可以直接在您的项目中创建一个 `/blog` 路径。

- **在 Next.js 中**，您只需要在 `pages` 目录下创建一个 `blog.js` 文件，Vercel 会自动处理这个路径。
    
    ```bash
    /pages
      ├── index.js       # 主页面
      └── blog.js        # /blog 页面
    ```
    
    然后在 `blog.js` 中创建您博客页面的内容：
    
    ```js
    // pages/blog.js
    export default function Blog() {
      return (
        <div>
          <h1>Welcome to my blog!</h1>
          {/* 这里是博客内容 */}
        </div>
      );
    }
    ```
    
    这样，访问 `https://wangjia.xin/blog` 时，Vercel 会自动渲染该页面。
    

#### 方法 2：使用自定义路由重定向

如果您的 `/blog` 路径需要指向另一个 Vercel 项目或特定的页面，您可以使用 **vercel.json** 配置文件进行重定向。

在项目根目录创建一个 `vercel.json` 文件，并添加如下重定向配置：

```json
{
  "redirects": [
    {
      "source": "/blog",
      "destination": "https://your-other-vercel-project-url.vercel.app/blog",
      "permanent": true
    }
  ]
}
```

这个配置会将访问 `https://wangjia.xin/blog` 的请求重定向到其他 Vercel 项目的 `/blog` 页面。

### 4. 验证 DNS 配置和 Vercel 配置

完成上述设置后，您需要等待 DNS 配置生效（通常需要几分钟到一小时）。生效后，您可以进行以下验证：

1. **访问** `https://wangjia.xin`，确保主域名正确指向您的 Vercel 项目。
2. **访问** `https://wangjia.xin/blog`，检查该路径是否正确显示您的博客内容（无论是通过 Next.js 页面、重定向或其他方式）。

### 5. 启用 HTTPS

Vercel 会自动为您配置 SSL/TLS 证书，并启用 HTTPS。所以一旦域名生效，`https://wangjia.xin` 和 `https://wangjia.xin/blog` 会自动启用 HTTPS。

### 总结

1. 在 **Vercel** 中为 `wangjia.xin` 配置自定义域名。
2. 在 **阿里云 DNS** 中配置 CNAME 或 A 记录，将域名指向 Vercel。
3. 在 Vercel 项目中配置 `/blog` 路径，确保该路径有相应内容。
4. 如果需要，将 `wangjia.xin/blog` 重定向到其他 Vercel 项目或路径。
5. 等待 DNS 配置生效并测试访问。

这样，您就可以将 `wangjia.xin/blog` 成功绑定到您的 Vercel 项目。







如果 `nolebase-template` 使用的是 **VitePress**，那么您可以按照以下步骤将 `wangjia.xin/blog` 绑定到 Vercel 上的项目，并确保 VitePress 项目能够正确渲染。

### 1. 部署 VitePress 项目到 Vercel

VitePress 是一个基于 Vite 的静态站点生成器，通常用于构建文档站点。如果 `nolebase-template` 使用 VitePress，您需要将该项目部署到 Vercel。

#### 步骤 1：从模板创建 VitePress 项目

1. **从 GitHub 克隆或 Fork `nolebase-template`**（假设它是一个 GitHub 仓库），然后将其部署到 Vercel。
    - 如果在 GitHub 上有 **Deploy to Vercel** 按钮，点击它直接将项目部署到 Vercel。
    - 如果没有该按钮，可以手动将项目从 GitHub 连接到 Vercel：在 Vercel 控制台中点击 **New Project**，然后选择从 GitHub 连接的仓库。

#### 步骤 2：设置 VitePress 项目

如果模板中已经包含了 VitePress 配置，您无需进行额外的配置。否则，您需要确保以下几个部分配置正确：

1. **安装依赖**：在项目根目录下，运行以下命令以安装所需的依赖项：
    
    ```bash
    npm install
    ```
    
2. **配置 `vitepress`**： VitePress 默认会在项目根目录下查找 `docs` 文件夹。如果您没有该文件夹，可以创建它，并在其中放置您的 Markdown 文件。VitePress 会自动生成静态站点。
    
    如果您的博客内容已经准备好，可以放在 `docs` 文件夹中的 `index.md` 或 `blog.md` 文件中。
    
    目录结构示例：
    
    ```
    /docs
      ├── index.md     # 主页面
      └── blog.md      # 博客页面
    ```
    
    VitePress 会自动根据这些 Markdown 文件生成静态页面。
    
3. **配置 VitePress**（可选）：如果您希望在 `/blog` 路径下展示博客内容，可以在 `vite.config.js` 中进行自定义配置。
    

#### 步骤 3：部署到 Vercel

1. 在 Vercel 上，确保您的 VitePress 项目已成功部署。Vercel 会自动识别静态生成的项目，并为您提供一个 URL。
    
2. 确保 Vercel 部署的项目路径是正确的，您可以通过访问 Vercel 提供的 URL 来查看生成的站点。
    

### 2. 配置 `wangjia.xin/blog`

要让 `wangjia.xin/blog` 显示您的 VitePress 项目，您需要将项目部署到该子路径。一般情况下，Vercel 支持通过自定义域名和路径绑定。

#### 步骤 1：在 Vercel 配置自定义域名

1. 登录 Vercel 控制台，进入您的项目设置页面。
2. 在 **Settings** -> **Domains** 页面中，选择 **Add**，然后输入 `wangjia.xin`。
3. Vercel 会提供一个 CNAME 或 A 记录，您需要将这些记录添加到阿里云的 DNS 配置中。

#### 步骤 2：配置 `wangjia.xin/blog` 子路径

要确保 `wangjia.xin/blog` 路径正确指向 Vercel 上的博客页面（即 `docs/blog.md`），您需要根据 Vercel 项目的结构配置路由。

- **确保 `/blog` 路径在 VitePress 项目中有效**。在 VitePress 中，`docs/blog.md` 将自动映射到 `/blog` 路径。
    
- 您不需要特别做任何额外的路由配置，只要您的 VitePress 项目结构正确，Vercel 会自动处理。
    

#### 步骤 3：更新阿里云 DNS 配置

在阿里云的 **DNS 控制台** 中，您需要确保配置了正确的 DNS 记录。

1. 登录阿里云控制台，进入 **云解析 DNS 管理** 页面。
2. 选择您的域名 `wangjia.xin`，进入 **解析设置** 页面。
3. 设置 CNAME 或 A 记录，将域名解析到 Vercel 项目。

例如：

- 如果您想通过 `www.wangjia.xin` 访问您的 VitePress 项目，您可以设置 CNAME 记录：
    - **主机记录**：`www`
    - **记录类型**：`CNAME`
    - **记录值**：Vercel 提供的域名（例如 `cname.vercel-dns.com`）

### 3. 验证和测试

1. 等待 DNS 记录生效（通常需要几分钟到一小时）。
2. 访问 `https://wangjia.xin/blog`，确认您的博客页面是否正确显示。如果配置正确，Vercel 会将该路径指向您的 VitePress 项目中的 `/blog` 页面。
3. 如果需要，您可以通过 `vercel.json` 配置文件进一步自定义路由或进行重定向。

### 4. 启用 HTTPS

Vercel 会自动为您配置 SSL/TLS 证书，因此 `https://wangjia.xin/blog` 将自动启用 HTTPS。无需手动配置 SSL。

---

### 总结

1. **部署 VitePress 项目到 Vercel**，确保 `/blog` 路径正确配置。
2. 在 **Vercel** 上为 `wangjia.xin` 添加自定义域名。
3. 在 **阿里云 DNS** 配置 CNAME 或 A 记录，确保 `wangjia.xin` 指向 Vercel 项目。
4. **验证并测试** `wangjia.xin/blog` 是否正确显示您的博客内容。

通过这些步骤，您可以成功将 `wangjia.xin/blog` 绑定到 Vercel 上的 VitePress 项目，并通过 HTTPS 安全访问。


![[Pasted image 20241229210646.png]]

然后再把代码中的一些路径上的  笔记 --> blog
