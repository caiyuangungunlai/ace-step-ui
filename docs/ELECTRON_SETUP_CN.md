# Electron 桌面应用 - 完整部署参考 🏗️

## 📖 目录

1. [项目结构](#项目结构)
2. [核心配置](#核心配置)
3. [高级功能](#高级功能)
4. [发布和分发](#发布和分发)
5. [故障排查](#故障排查)

---

## 项目结构

```
ace-step-ui/
│
├── electron/
│   ├── main.js              # Electron 主进程
│   │   ├── 应用生命周期管理
│   │   ├── 窗口管理
│   │   ├── IPC 处理
│   │   └── 菜单系统
│   └── preload.js           # 安全通信桥梁
│       ├── contextBridge
│       ├── ipcRenderer
│       └── 系统信息暴露
│
├── public/
│   └── favicon.png          # 应用图标
│
├── server/                  # Express 后端
│   ├── src/
│   ├── package.json
│   └── ...
│
├── src/                     # React 前端
│   ├── components/
│   ├── pages/
│   └── ...
│
├── dist/                    # 编译输出
│   ├── index.html
│   ├── assets/
│   └── ...
│
├── dist/electron/           # Electron 构建输出
│   ├── ACE-Step UI Setup.exe
│   ├── ACE-Step UI.dmg
│   ├── ACE-Step UI.AppImage
│   └── ...
│
├── electron-builder.json    # 构建配置
├── package.json             # 项目配置
├── vite.config.ts           # Vite 配置
├── tsconfig.json            # TypeScript 配置
└── run-electron-dev.*       # 开发脚本
```

---

## 核心配置

### 1. package.json 关键字段

```json
{
  "name": "ace-step-ui",
  "version": "1.0.0",
  "main": "electron/main.js",           // Electron 入口
  "homepage": "./",                     // 前端主页
  "scripts": {
    "dev": "vite",                      // 前端开发
    "build": "vite build",              // 前端构建
    "start": "electron .",              // 启动 Electron
    "build:electron": "npm run build && electron-builder"
  },
  "devDependencies": {
    "electron": "^28.0.0",
    "electron-builder": "^24.9.1",
    "electron-is-dev": "^2.0.0"
  }
}
```

### 2. electron-builder.json 详解

```json
{
  "appId": "com.acestepi.ui",           // 应用唯一标识
  "productName": "ACE-Step UI",        // 应用显示名称
  
  "directories": {
    "buildResources": "public",        // 资源目录
    "output": "dist/electron"          // 输出目录
  },
  
  "files": [
    "dist/**/*",                        // 前端编译文件
    "electron/**/*",                    // Electron 代码
    "node_modules/**/*",                // 依赖
    "public/**/*"                       // 资源
  ],
  
  "win": {
    "target": [
      { "target": "nsis", "arch": ["x64"] },    // 安装程序
      { "target": "portable", "arch": ["x64"] } // 便携版
    ]
  },
  
  "nsis": {
    "oneClick": false,                  // 非一键安装
    "allowToChangeInstallationDirectory": true,
    "createDesktopShortcut": true,      // 创建桌面快捷方式
    "createStartMenuShortcut": true     // 创建开始菜单
  },
  
  "mac": {
    "target": ["dmg", "zip"],
    "category": "public.app-category.music"
  },
  
  "linux": {
    "target": ["AppImage", "deb"],
    "category": "Audio"
  }
}
```

### 3. electron/main.js 详解

**主要功能：**

```javascript
// 应用启动流程
app.on('ready', async () => {
  await startBackendServices();  // 1. 启动后端
  createWindow();                // 2. 创建窗口
  createMenu();                  // 3. 创建菜单
});

// 窗口配置
function createWindow() {
  mainWindow = new BrowserWindow({
    width: 1400,               // 默认宽度
    height: 900,               // 默认高度
    minWidth: 800,             // 最小宽度
    webPreferences: {
      preload: path.join(__dirname, 'preload.js'),
      nodeIntegration: false,   // 安全性
      contextIsolation: true,   // 隔离上下文
      sandbox: true             // 沙箱模式
    }
  });
  
  // 开发模式加载 localhost:3000
  // 生产模式加载本地 HTML 文件
  const url = isDev
    ? 'http://localhost:3000'
    : `file://${path.join(__dirname, '../dist/index.html')}`;
  
  mainWindow.loadURL(url);
}
```

### 4. electron/preload.js 详解

**安全通信桥梁：**

```javascript
// 只暴露必要的 API 到渲染进程
contextBridge.exposeInMainWorld('electron', {
  getVersion: () => ipcRenderer.invoke('get-version'),
  getPlatform: () => ipcRenderer.invoke('get-platform'),
  getDataPath: () => ipcRenderer.invoke('get-data-path')
});

// 在 React 组件中使用：
// const version = await window.electron.getVersion();
```

---

## 高级功能

### 1. 自动启动后端

```javascript
// 在生产环境中自动启动 Express 服务器
function startBackendServices() {
  if (!isDev) {
    const backendProcess = spawn(cmd, ['start'], {
      cwd: serverDir,
      stdio: 'inherit'
    });
  }
}
```

### 2. 应用菜单

```javascript
// 自定义菜单
const template = [
  {
    label: 'File',
    submenu: [
      { label: 'Exit', accelerator: 'CmdOrCtrl+Q', click: () => app.quit() }
    ]
  },
  {
    label: 'Help',
    submenu: [
      {
        label: 'Documentation',
        click: () => require('electron').shell.openExternal('https://...')
      }
    ]
  }
];
```

### 3. IPC 通信

**主进程处理：**
```javascript
ipcMain.handle('get-version', () => app.getVersion());
```

**渲染进程调用：**
```javascript
const version = await window.electron.getVersion();
```

---

## 发布和分发

### 1. 版本管理

更新 `package.json` 的 `version` 字段：

```json
{
  "version": "1.0.0",  // major.minor.patch
  "version": "1.1.0",  // 新功能发布
  "version": "1.0.1"   // 修复发布
}
```

### 2. 构建命令

```bash
# 全平台构建
npm run build:all

# 单平台构建
npm run build:electron:win
npm run build:electron:mac
npm run build:electron:linux

# 仅编译前端（不打包 Electron）
npm run build
```

### 3. 输出文件

**Windows：**
- `ACE-Step UI Setup 1.0.0.exe` - 安装程序（推荐）
- `ACE-Step UI 1.0.0.exe` - 便携版（无需安装）

**macOS：**
- `ACE-Step UI-1.0.0.dmg` - 磁盘镜像
- `ACE-Step UI-1.0.0.zip` - 压缩包

**Linux：**
- `ACE-Step UI-1.0.0.AppImage` - AppImage 格式（推荐）
- `ace-step-ui_1.0.0_amd64.deb` - Debian 包

### 4. GitHub Release 发布

```bash
# 创建 tag
git tag v1.0.0
git push origin v1.0.0

# 在 GitHub 上创建 Release
# 上传 dist/electron/ 中的文件
```

### 5. 自动更新（可选）

配置 electron-updater：

```javascript
import { autoUpdater } from 'electron-updater';

autoUpdater.checkForUpdatesAndNotify();
```

---

## 故障排查

### 常见问题

**Q: Electron 打不开**
- A: 检查后端是否运行（端口 3001）
- A: 检查前端是否编译完成
- A: 查看 DevTools 错误信息

**Q: 后端进程启动失败**
- A: 检查 Node.js 版本
- A: 运行 `cd server && npm install`
- A: 检查端口 3001 是否被占用

**Q: 构建失败**
- A: 清理 `dist` 目录：`rm -rf dist`
- A: 重新安装依赖：`rm -rf node_modules && npm install`
- A: 检查磁盘空间

**Q: 应用体积太大**
- A: 这是正常的（包含了 Chromium）
- A: 可以通过代码分割优化前端体积

---

## 📚 参考资源

- [Electron 官方文档](https://www.electronjs.org/docs)
- [Electron Builder 文档](https://www.electron.build/)
- [IPC 通信指南](https://www.electronjs.org/docs/tutorial/ipc)
