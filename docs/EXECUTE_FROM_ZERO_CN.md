# 从零开始执行 - Electron 桌面应用完整指南 🚀

## 📋 5 个阶段完整流程

本指南将逐步带您从零开始，完成 Electron 桌面应用的部署。

---

## 📍 阶段 1：环境检查（5分钟）

### 检查项目是否已克隆

```bash
# 进入项目目录
cd ace-step-ui

# 验证目录结构
ls -la  # macOS/Linux
dir     # Windows
```

预期看到：
- ✅ `package.json`
- ✅ `server/` 目录
- ✅ `src/` 或类似的源代码目录
- ✅ `vite.config.ts`

### 检查 Node.js 版本

```bash
node --version    # 应显示 v18.x 或更高
npm --version     # 应显示 8.x 或更高
```

**如果版本不符：**
- 访问 https://nodejs.org/
- 下载 LTS 版本（18 或更高）
- 重新安装

### 检查文件权限（macOS/Linux）

```bash
ls -l setup-electron.sh run-electron-dev.sh
```

预期：所有者可执行

---

## 📍 阶段 2：初始化项目（10分钟）

### 运行初始化脚本

**macOS/Linux：**
```bash
chmod +x setup-electron.sh    # 给予执行权限
./setup-electron.sh           # 运行脚本
```

**Windows：**
```bash
setup-electron.bat
```

### 脚本执行内容

脚本会自动完成：

1. **验证环境**
   - ✅ Node.js 版本检查
   - ✅ npm 可用性检查

2. **创建目录**
   ```
   electron/      # Electron 主进程
   public/        # 资源文件
   dist/          # 编译输出
   ```

3. **安装依赖**
   ```bash
   # 主项目依赖
   npm install
   
   # 后端依赖
   cd server && npm install && cd ..
   
   # Electron 工具
   npm install --save-dev electron electron-builder
   ```

### 预期输出

```
═══════════════════════════════════════════════════════════
    初始化完成！ ✨
═══════════════════════════════════════════════════════════

下一步，运行以下命令开始开发：

  📱 开发模式:
    ./run-electron-dev.sh

  🏗️  构建生产版本:
    npm run build:electron
```

---

## 📍 阶段 3：启动开发环境（5分钟）

### 运行开发脚本

**macOS/Linux：**
```bash
chmod +x run-electron-dev.sh
./run-electron-dev.sh
```

**Windows：**
```bash
run-electron-dev.bat
```

### 脚本做的事情

脚本会依次启动：

1. **Express 后端** (端口 3001)
   ```bash
   cd server && npm run dev
   ```
   
2. **Vite 前端服务器** (端口 3000)
   ```bash
   npm run dev
   ```
   
3. **Electron 应用**
   ```bash
   npm start
   ```

### 启动时的预期行为

```
1. 终端会显示后端启动日志
2. 终端会显示 Vite 编译日志
3. Electron 窗口会自动打开
4. 看到 ACE-Step UI 界面加载
5. DevTools 会自动打开（仅开发模式）
```

### 验证一切正常

检查清单：
- ✅ 后端日志显示 "listening on port 3001"
- ✅ Vite 显示 "Local: http://localhost:3000"
- ✅ Electron 窗口打开无错误
- ✅ 能看到音乐生成界面
- ✅ DevTools 显示无红色错误

---

## 📍 阶段 4：测试应用功能（10分钟）

### 基础功能测试

#### 1. 页面加载
- [ ] 主界面完全加载
- [ ] 没有白屏或崩溃
- [ ] UI 组件正常显示

#### 2. 后端连接
- [ ] DevTools 网络标签页中有 API 请求
- [ ] 没有 CORS 错误
- [ ] 响应状态码为 200

#### 3. 热重载（可选）
- [ ] 修改 React 组件
- [ ] 保存文件
- [ ] Vite 显示 HMR 更新
- [ ] 页面自动刷新

### 常见问题排查

**问题：Electron 窗口打不开**
```
解决方案：
1. 检查控制台错误信息
2. 查看后端是否运行（端口 3001）
3. 查看前端是否运行（端口 3000）
4. 尝试手动打开 http://localhost:3000 在浏览器中
```

**问题："Cannot GET /" 错误**
```
解决方案：
1. 前端编译未完成，等待 Vite 输出 "ready in xxx ms"
2. 重新启动开发脚本
```

**问题：后端连接失败**
```
解决方案：
1. 检查后端进程是否运行
2. 查看 server/package.json 的 "dev" 脚本
3. 手动运行 cd server && npm run dev
```

---

## 📍 阶段 5：构建生产版本（20分钟）

### 构建前准备

1. **停止开发服务**
   - 按 `Ctrl+C` 停止所有进程

2. **清理旧构建**
   ```bash
   rm -rf dist node_modules/.vite  # macOS/Linux
   rmdir /S dist node_modules\.vite # Windows
   ```

### 构建所有平台

```bash
npm run build:all
```

或针对特定平台：

```bash
npm run build:electron:win    # 仅 Windows (.exe)
npm run build:electron:mac    # 仅 macOS (.dmg)
npm run build:electron:linux  # 仅 Linux (.AppImage)
```

### 构建过程

```
1. Vite 编译前端 (30-60秒)
   → 输出到 dist/

2. Electron-builder 打包 (1-3分钟)
   → 输出到 dist/electron/

3. 生成安装程序
   → Windows: .exe 和便携版
   → macOS: .dmg
   → Linux: .AppImage 和 .deb
```

### 验证构建成功

```bash
ls -la dist/electron/  # macOS/Linux
dir dist\electron\     # Windows
```

预期文件：
- ✅ Windows: `ACE-Step UI Setup 1.0.0.exe`
- ✅ macOS: `ACE-Step UI-1.0.0.dmg`
- ✅ Linux: `ACE-Step UI-1.0.0.AppImage`

### 测试安装程序

1. **Windows：**
   ```bash
   # 双击 .exe 文件或运行
   .\"dist/electron/ACE-Step UI Setup 1.0.0.exe"
   ```

2. **macOS：**
   ```bash
   # 双击 .dmg 文件
   open dist/electron/"ACE-Step UI-1.0.0.dmg"
   ```

3. **Linux：**
   ```bash
   chmod +x dist/electron/"ACE-Step UI-1.0.0.AppImage"
   ./dist/electron/"ACE-Step UI-1.0.0.AppImage"
   ```

---

## ✅ 完成清单

- [ ] 阶段 1：环境检查完成
- [ ] 阶段 2：初始化脚本成功运行
- [ ] 阶段 3：开发环境启动成功
- [ ] 阶段 4：应用功能测试通过
- [ ] 阶段 5：生产版本构建成功
- [ ] 安装程序已生成并可运行

---

## 🎉 下一步

- 📤 分发应用：将 `.exe`/`.dmg`/`.AppImage` 分享给用户
- 🚀 自动更新：配置 GitHub Release 用于更新检查
- 🔧 高级配置：查看 `ELECTRON_SETUP_CN.md`
- 📚 其他参考：查看 `ELECTRON_COMPLETE_SUMMARY_CN.md`
