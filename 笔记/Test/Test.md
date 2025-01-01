

## 1、初始化 Node 项目

``` 
npm init
```

- **npm init** 用于在当前目录下初始化一个新的 Node.js 项目
- 同时，它生成一个 `package.json` 文件，这个文件记录了项目的基本信息



## 2、创建 Electron 程序

```
// 1. 官方下载方法，换了镜像也要从 github 上下载（嘎嘎慢）
npm install --save-dev electron  


// 2. 更改临时npm config
npm install --verbose electron --registry=https://registry.npmmirror.com/ --ELECTRON_BUILDER_BINARIES_MIRROR=https://npmmirror.com/mirrors/electron-builder-binaries/ --ELECTRON_MIRROR=https://cdn.npmmirror.com/binaries/electron/


// 3. 更改永久npm config
1. 先设置registry镜像（已做）
npm config set registry https://registry.npmmirror.com/

2. 然后设置electron镜像，在弹出来的文本中加入后面两句话（已做）
npm config edit

ELECTRON_BUILDER_BINARIES_MIRROR=https://npmmirror.com/mirrors/electron-builder-binaries/
ELECTRON_MIRROR=https://cdn.npmmirror.com/binaries/electron/

3. 安装 electron（直接进行）
npm install electron
```



## 3、配置主配置文件：`package.json`
```json
{
  "name": "spritedaway",
  "version": "1.0.0",
  "main": "index.js",  // 主入口文件，Electron 应用从这里开始运行
  
  "scripts": {   
    "start": "electron ."  // 使用 npm start 启动开发模式，若是 testt 就用 npm testt
  },
  
  "author": "wj",
  "license": "ISC",
  "description": "spritedaway",
  "devDependencies": {
    "electron": "^33.0.2"
  }
    "build": "electron-builder"
}
```



## 4、配置主进程：`index.js`

以下是一套模板，您可以直接使用，只需做简单修改即可。
``` js
const { app, BrowserWindow } = require('electron')

let win // 声明为let而不是const

const createWindow = () => {
  win = new BrowserWindow({
    width: 800,
    height: 600,
    autoHideMenuBar: true,
    webPreferences: {
      nodeIntegration: false,  // 禁用Node.js集成
      contextIsolation: true,  // 启用上下文隔离
      enableRemoteModule: false  // 禁用remote模块
    }
  })
  
   // win.loadFile('/index.html')
  win.loadURL('https://www.electronjs.org/zh/docs/latest/')
  
  // 打开开发者工具（可选）
  // win.webContents.openDevTools()

  win.on('closed', () => {
    win = null // 允许重新赋值

  })

  win.on('unresponsive', () => {
    console.log('窗口无响应')
  })
  win.webContents.on('did-fail-load', (event, errorCode, errorDescription) => {
    console.error(`加载失败: ${errorDescription}`)
  })
}

// 应用就绪时创建窗口

app.on('ready', () => {
  createWindow()
  app.on('activate', () => {
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

// 所有窗口关闭时退出
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit()
  }
})
```



## 5、书写渲染进程

渲染进程的编写可以直接采用HTML、CSS和JS标准技术，您可以将其视为类似于在浏览器环境中运行的前端项目。

简单来说，渲染进程的处理方式与前端开发无异，遵循常规的前端编码实践即可。



## 6、运行程序

```
npm start
```



## 7、配置自动重启（若需要）

配置自动重启，指的是我们在调试过程中，如果修改内容，Electron 会自动重启，以便我们调试。

1. 引入 `Nodemon` 依赖
```sh
   npm i nodemon -D
   ```

2. 修改 `package.json` 命令
   ```json
   "scripts": {
     "start": "nodemon --exec electron ."
   },
   ```

3. 配置 `nodemon.json` 规则
   ```json
   {
     "ignore": [
       "node_modules",
       "dist"
     ],
     "restartable": "r",
     "watch": ["*.*"],
     "ext": "html,js,css"
   }
   ```



## 8、配置自定义 安装 / 卸载 页面

- 专业的事，找专业的人来办

- 如果未启用自定义安装路径，分发包将默认安装至以下目录：`C:\Users\<用户名>\AppData\Local\<应用程序名>`



## 9、程序打包



### 9.1、安装 Electron Forge CLI 工具

Electron-forge 是 Electron 的脚手架工具，可以自动生成项目文件
```
npm install --save-dev @electron-forge/cli
```



### 9.2、导入 Electron 程序

```
npx electron-forge import
```
- 本操作是将 Electron 应用程序导入到 Electron Forge 项目结构中

- 导入成功后，会自动生成 `forge.config.js` 文件，其是 Electron Forge 项目的配置文件



### 9.3、配置 `forge.config.js`

```js
const { FusesPlugin } = require('@electron-forge/plugin-fuses');
const { FuseV1Options, FuseVersion } = require('@electron/fuses');

module.exports = {
  packagerConfig: {
    // 基础配置
    name: 'MyElectronApp', // 应用程序的名称，用于生成安装包文件名
    productName: 'My Electron App', // 应用的展示名称，通常用于在操作系统中显示
    icon: 'path/to/icon.png', // 应用程序的图标路径，打包时会嵌入到安装包中
    out: 'build/', // 打包后的文件输出目录，所有生成的文件（如安装包）将存放在此目录中
    overwrite: true, // 如果输出目录已经存在同名文件，是否覆盖它
    asar: true, // 是否启用 ASAR 格式打包，ASAR 是 Electron 特有的归档格式，可以将应用程序的资源文件压缩，提高加载效率
    version: '1.0.0', // 应用程序的版本号，用于标识当前的应用版本
    copyright: 'Copyright © 2023', // 应用程序的版权信息，通常用于安装包中显示
    ignore: [ // 在打包时忽略的文件或文件夹，避免将不必要的文件包含在内
      '.git', // Git 版本控制文件夹
      '.vscode', // VSCode 配置文件夹
      'node_modules/.cache', // Node.js 的缓存文件夹
      'src' // 源代码文件夹（如果不想打包源代码）
    ],
    
    // 针对不同平台的特定配置
    win: {
      target: 'nsis', // 在 Windows 平台使用 NSIS 格式打包，这将生成一个 Windows 安装程序
      icon: 'path/to/windows/icon.ico', // Windows 平台使用的图标文件路径
      publisherName: 'My Company', // 在 Windows 安装程序中显示的发布者名称（用于数字签名时显示）
      fileAssociations: [ // 文件关联配置，用于指定哪些文件扩展名由该应用打开
        {
          ext: 'myext', // 文件扩展名
          name: 'My Extension', // 文件类型的名称
          description: 'Open My Extension files', // 文件类型的描述
          role: 'Editor' // 文件类型的角色（如：Viewer, Editor）
        }
      ],
      // 其他 Windows 配置（例如，数字证书配置等）可以在此添加
    },
    
    mac: {
      target: 'dmg', // 在 macOS 平台使用 DMG 格式打包，这将生成一个磁盘映像文件（适用于 macOS 安装）
      icon: 'path/to/mac/icon.icns', // macOS 平台使用的图标文件路径（支持 .icns 格式）
      identity: 'Developer ID Application: My Company (TeamID)', // macOS 发布者的身份，通常需要配置 Apple Developer 证书
      category: 'public.app-category.utilities', // 应用程序的类别，便于在 macOS App Store 中分类
      extendInfo: { // 额外的 macOS 配置信息
        NSAppTransportSecurity: {
          NSAllowsArbitraryLoads: true // 允许加载任意网络资源（此选项通常不建议开启，除非必要）
        }
      }
    },
    
    linux: {
      target: 'deb', // 在 Linux 平台使用 DEB 格式打包，这适用于基于 Debian 的 Linux 发行版
      icon: 'path/to/linux/icon.png', // Linux 平台使用的图标文件路径（支持 PNG 格式）
      category: 'Utility', // 应用程序的分类，用于 Linux 桌面环境中的应用列表
      description: 'My Electron App', // 应用程序的简短描述
      desktop: { // Linux 桌面快捷方式的配置
        Name: 'My Electron App', // 快捷方式名称
        Comment: 'My Electron App', // 快捷方式注释，通常显示在鼠标悬停时
        Exec: './MyElectronApp', // 执行命令，启动应用的命令（通常是可执行文件的路径）
        Terminal: false // 是否需要在终端中打开应用，false 表示不需要
      }
    }
  },
  
  rebuildConfig: {}, // 重新构建时的配置，通常用于修改或重新构建原生模块
  makers: [ // 打包工具配置，指定使用哪些打包器来生成安装包
    {
      name: '@electron-forge/maker-squirrel', // 使用 Squirrel 打包器生成 Windows 安装包
      config: {}, // Squirrel 的具体配置（根据需要填写）
    },
    {
      name: '@electron-forge/maker-zip', // 使用 ZIP 打包器在 macOS 平台生成 ZIP 文件
      platforms: ['darwin'], // 仅在 macOS 平台上使用该打包器
    },
    {
      name: '@electron-forge/maker-deb', // 使用 DEB 打包器生成 Linux DEB 包
      config: {}, // DEB 包的具体配置（根据需要填写）
    },
    {
      name: '@electron-forge/maker-rpm', // 使用 RPM 打包器生成 Linux RPM 包
      config: {}, // RPM 包的具体配置（根据需要填写）
    },
  ],
  
  plugins: [ // 插件配置，用于扩展 Electron Forge 功能
    {
      name: '@electron-forge/plugin-auto-unpack-natives', // 自动解压本地模块插件
      config: {}, // 插件的具体配置（根据需要填写）
    },
    // Fuses 用于在打包时启用/禁用 Electron 的某些功能
    // 在代码签名之前启用或禁用特定功能
    new FusesPlugin({
      version: FuseVersion.V1, // 使用 Fuses v1 版本
      [FuseV1Options.RunAsNode]: false, // 禁用将应用作为 Node.js 运行
      [FuseV1Options.EnableCookieEncryption]: true, // 启用 Cookie 加密
      [FuseV1Options.EnableNodeOptionsEnvironmentVariable]: false, // 禁用 Node.js 选项环境变量
      [FuseV1Options.EnableNodeCliInspectArguments]: false, // 禁用 Node.js CLI inspect 参数
      [FuseV1Options.EnableEmbeddedAsarIntegrityValidation]: true, // 启用 ASAR 文件完整性验证
      [FuseV1Options.OnlyLoadAppFromAsar]: true, // 仅从 ASAR 文件加载应用程序，增强安全性
    }),
  ],
};
```



### 9.4、程序打包



#### 1、Windows

**1.1、`.exe` 安装包**

`@electron-forge/maker-squirrel` 用于在 Windows 平台上生成 `.exe` 安装包`

- **64-bit**
```
npm run make --platform=win32 --arch=x64
```

- **32-bit**
```
npm run make --platform=win32 --arch=ia32
```



#### 2、MacOS

**2.1、`.zip` 安装包**

`@electron-forge/maker-zip` 用于在 macOS 平台上生成 `.zip` 文件

```
npm run make --platform=darwin
```
你需要在 macOS 上运行该命令，或者在支持 macOS 的构建环境（如 macOS 虚拟机或 CI/CD 服务）中运行
果是在 Windows 或 Linux 上运行此命令，Electron Forge 不会真正生成 macOS 的 ZIP 文件（因为 Electron 打包器依赖于平台特定工具，比如 `dmg` 和 macOS 相关签名工具）


**2.2、`dmg` 安装包**



#### 3、Linux

**3.1、`.deb` 安装包**

`@electron-forge/maker-deb` 用于在 Linux 平台上生成 `.deb` 安装包。

```
npm run make --platform=linux
```


**3.2、`.rpm` 安装包**

`@electron-forge/maker-rpm`用于在 Linux 平台上生成 `.rpm` 安装包

```
npm run make --platform=linux

```



## 10、注意事项

1. 项目路径不包含中文字符。

2. package.json 中最好别出现中文，不能有注释

3. 打包这个事情纯靠运气和网络，现在不行，等一会可能就行了
