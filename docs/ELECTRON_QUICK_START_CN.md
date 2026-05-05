# ACE-Step UI Electron - 快速上手指南 ⚡

## 5分钟快速开始

### 前置要求
- Node.js 18 或更高
- npm 或 yarn
- Git

### 第1步：初始化项目（3分钟）

#### macOS / Linux：
```bash
chmod +x setup-electron.sh
./setup-electron.sh
```

#### Windows：
```bash
setup-electron.bat
```

这会自动：
- ✅ 检查 Node.js 版本
- ✅ 创建必要的目录
- ✅ 安装所有依赖（包括 Electron）
- ✅ 配置构建工具

### 第2步：启动开发环境（2分钟）

#### macOS / Linux：
```bash
chmod +x run-electron-dev.sh
./run-electron-dev.sh
```

#### Windows：
```bash
run-electron-dev.bat
```

**您会看到：**
- 🟢 后端启动在 http://localhost:3001
- 🟢 前端启动在 http://localhost:3000
- 🟢 Electron 窗口自动打开
- 🟢 开发工具已启用（DevTools）

### 第3步：开始开发

修改代码，Electron 会自动重新加载。按 `Ctrl+C` 停止所有服务。

---

## 常见问题

### Q: 如何生成安装程序？
```bash
npm run build:electron
```
输出文件在 `dist/electron/` 目录：
- Windows: `.exe` 和便携版
- macOS: `.dmg`
- Linux: `.AppImage` 和 `.deb`

### Q: 如何针对特定平台构建？
```bash
npm run build:electron:win   # 仅 Windows
npm run build:electron:mac   # 仅 macOS
npm run build:electron:linux # 仅 Linux
npm run build:all            # 所有平台
```

### Q: Electron 窗口打不开？
1. 检查后端是否正在运行（端口 3001）
2. 检查前端是否正在运行（端口 3000）
3. 查看 Electron DevTools 控制台的错误信息

### Q: 如何关闭 DevTools？
编辑 `electron/main.js`，注释掉这一行：
```javascript
// mainWindow.webContents.openDevTools();
```

---

## 项目结构

```
ace-step-ui/
├── electron/
│   ├── main.js          # Electron 主进程
│   └── preload.js       # 安全通信桥梁
├── public/
│   └── favicon.png      # 应用图标
├── server/              # Express 后端
├── src/                 # React 前端源代码
├── electron-builder.json # 构建配置
├── package.json         # 项目配置
└── run-electron-dev.*   # 开发脚本
```

---

## 下一步

- 📖 详细配置 → 查看 `ELECTRON_SETUP_CN.md`
- 🔧 从零开始 → 查看 `EXECUTE_FROM_ZERO_CN.md`
- ⚙️ 命令参考 → 查看 `ELECTRON_COMPLETE_SUMMARY_CN.md`
