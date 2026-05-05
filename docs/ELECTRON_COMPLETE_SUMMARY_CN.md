# Electron 完整功能总结 - 命令速查表 ⚡

## 🎯 快速命令参考

### 初始化和开发

| 命令 | 功能 | 平台 |
|------|------|------|
| `./setup-electron.sh` | 初始化项目（自动安装依赖） | macOS/Linux |
| `setup-electron.bat` | 初始化项目（自动安装依赖） | Windows |
| `./run-electron-dev.sh` | 启动开发环境 | macOS/Linux |
| `run-electron-dev.bat` | 启动开发环境 | Windows |
| `npm run dev` | 仅启动前端（localhost:3000） | 全平台 |
| `npm start` | 启动 Electron 应用 | 全平台 |

### 构建和打包

| 命令 | 功能 | 输出 |
|------|------|------|
| `npm run build` | 构建前端 | `dist/` |
| `npm run build:all` | 打包所有平台 | `dist/electron/` |
| `npm run build:electron:win` | 打包 Windows | `.exe` + 便携版 |
| `npm run build:electron:mac` | 打包 macOS | `.dmg` |
| `npm run build:electron:linux` | 打包 Linux | `.AppImage` + `.deb` |
| `npm run build:electron` | 打包当前平台 | 平台相关文件 |

---

## 📂 项目文件概览

### 核心文件

```
✅ electron/main.js
   ├─ app.on('ready')        应用启动
   ├─ createWindow()         创建窗口
   ├─ startBackendServices() 启动后端
   └─ ipcMain handlers       IPC 处理

✅ electron/preload.js
   ├─ contextBridge          安全桥梁
   ├─ ipcRenderer            通信接口
   └─ exposeInMainWorld      暴露 API

✅ electron-builder.json
   ├─ appId                  应用标识
   ├─ win/mac/linux          平台配置
   └─ files                  打包文件

✅ package.json (已更新)
   ├─ "main": "electron/main.js"
   ├─ electron 依赖
   └─ 构建脚本
```

### 脚本文件

```
✅ setup-electron.sh/bat
   ├─ 环境检查
   ├─ 目录创建
   └─ 依赖安装

✅ run-electron-dev.sh/bat
   ├─ 启动后端 (3001)
   ├─ 启动前端 (3000)
   └─ 启动 Electron
```

---

## 🎬 三种启动方式

### 方式 1：完整开发环境（推荐）

```bash
./run-electron-dev.sh  # 或 run-electron-dev.bat
```

**启动内容：**
- 🟢 Express 后端（http://localhost:3001）
- 🟢 Vite 前端（http://localhost:3000）
- 🟢 Electron 应用窗口
- 🟢 DevTools 自动打开

**适用场景：** 开发新功能、调试问题

---

### 方式 2：Electron + 现有服务

```bash
# 终端 1: 启动后端（可选，如果已在运行则跳过）
cd server && npm run dev

# 终端 2: 启动前端
npm run dev

# 终端 3: 启动 Electron
npm start
```

**适用场景：** 分别开发各层、多人协作

---

### 方式 3：生产模式（已打包）

```bash
# 构建生产版本
npm run build:all

# 运行生成的安装程序
# Windows: dist/electron/ACE-Step UI Setup 1.0.0.exe
# macOS:   dist/electron/ACE-Step UI-1.0.0.dmg
# Linux:   dist/electron/ACE-Step UI-1.0.0.AppImage
```

**适用场景：** 发布给用户、测试最终产品

---

## 📊 开发流程图

```
┌─────────────────────────────────────────┐
│   $ ./run-electron-dev.sh (或 .bat)    │
└──────────────┬──────────────────────────┘
               │
     ┌─────────┼─────────┐
     ▼         ▼         ▼
  后端      前端     Electron
  (3001)   (3000)   窗口
     │         │         │
     └─────────┼─────────┘
               │
               ▼
        访问 localhost:3000
        或 Electron 窗口
               │
     ┌─────────┴─────────┐
     ▼                   ▼
  修改代码           Ctrl+C 停止
  自动重载
     │
     └──► 反复开发
```

---

## 🔧 常用操作

### 检查环境

```bash
node --version      # 需要 v18+
npm --version       # 需要 8+
which npm           # macOS/Linux
where npm           # Windows
```

### 清理和重新安装

```bash
# 清理所有编译产物
rm -rf dist node_modules/.vite  # macOS/Linux
rmdir /S dist node_modules/.vite # Windows

# 重新安装依赖
npm install
cd server && npm install && cd ..
```

### 查看日志

```bash
# 前端日志：Vite 控制台
# 后端日志：Express 控制台
# Electron 日志：DevTools 控制台

# 或查看系统日志
# macOS: ~/Library/Logs/ACE-Step UI/
# Windows: %APPDATA%/ACE-Step UI/logs
# Linux: ~/.config/ACE-Step UI/
```

### 打开 DevTools

```bash
# 在 Electron 中
Ctrl+Shift+I  (Windows/Linux)
Cmd+Option+I  (macOS)

# 或编辑 electron/main.js
mainWindow.webContents.openDevTools();
```

---

## 🎨 UI 更改对应的操作

| 修改类型 | 自动重载 | 需要重启 |
|---------|--------|--------|
| React 组件 | ✅ | ❌ |
| CSS/样式 | ✅ | ❌ |
| API 调用 | ✅ | ❌ |
| 后端代码 | ✅ | ❌ |
| package.json | ❌ | ✅ |
| Electron 配置 | ❌ | ✅ |
| 环境变量 | ❌ | ✅ |

---

## 📦 构建输出文件说明

### Windows
```
ACE-Step UI Setup 1.0.0.exe    [推荐] 标准安装程序
ACE-Step UI 1.0.0.exe          便携版，无需安装
```

### macOS
```
ACE-Step UI-1.0.0.dmg          标准安装程序
ACE-Step UI-1.0.0.zip          压缩包
```

### Linux
```
ACE-Step UI-1.0.0.AppImage     [推荐] 通用格式
ace-step-ui_1.0.0_amd64.deb    Debian/Ubuntu 包
```

---

## 🚀 发布流程

```
1. 更新 package.json 版本
   "version": "1.0.0" → "1.1.0"

2. 构建生产版本
   npm run build:all

3. 测试所有安装程序
   - Windows: 双击 .exe
   - macOS: 双击 .dmg
   - Linux: ./AppImage

4. 创建 Git tag
   git tag v1.1.0
   git push origin v1.1.0

5. 在 GitHub 创建 Release
   - 上传 dist/electron/ 中的文件
   - 添加更新说明
   - 发布

6. 用户下载并安装
```

---

## 🎓 学习资源

### 官方文档
- [Electron 官网](https://www.electronjs.org/)
- [Electron Builder](https://www.electron.build/)
- [Vite 官网](https://vitejs.dev/)

### 文档文件
- `ELECTRON_QUICK_START_CN.md` - 5分钟快速开始
- `EXECUTE_FROM_ZERO_CN.md` - 5阶段详细指南
- `ELECTRON_SETUP_CN.md` - 完整部署参考

---

## ❓ 常见问题速查

**Q: 如何更改应用名称？**
```json
// electron-builder.json
"productName": "新名称"
```

**Q: 如何更改应用图标？**
```
将新图标放在 public/ 目录
 electron-builder 会自动检测
```

**Q: 如何针对特定操作系统开发？**
```javascript
if (process.platform === 'win32') { /* Windows */ }
if (process.platform === 'darwin') { /* macOS */ }
if (process.platform === 'linux') { /* Linux */ }
```

**Q: 应用体积为什么这么大？**
```
这是正常的，包含了 Chromium (~200MB)
可通过以下方式优化：
- 代码分割
- Tree shaking
- 删除不用的依赖
```

---

## 📝 核心改进清单

- ✅ Electron 主进程完整实现
- ✅ 安全的 IPC 通信桥梁
- ✅ 自动启动后端服务
- ✅ 跨平台构建配置
- ✅ 一键初始化脚本
- ✅ 开发环境启动脚本
- ✅ 详细中文文档
- ✅ 多种启动方式
- ✅ 生产构建配置
- ✅ 应用菜单系统

**现在您的 ACE-Step UI 已经是一个完整的跨平台桌面应用！** 🎉
