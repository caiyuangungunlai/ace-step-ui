# EXE 一键打包指南 - ACE-Step UI

> 📦 **一键将 ACE-Step UI 打包成 Windows EXE 文件，开箱即用！**

## ✨ 快速开始

### Windows 用户 (最简单!)

```batch
cd ace-step-ui
build-exe.bat
```

**就这样!** 脚本会自动:
- ✅ 安装所有依赖
- ✅ 构建前端资源
- ✅ 编译后端程序
- ✅ 生成 EXE 安装包

打包完成后，你会在 `dist/` 文件夹找到:
- `ACE-Step-UI-x.x.x.exe` - 标准安装版
- `ACE-Step-UI-x.x.x.exe` - 便携版 (无需安装，直接运行)

### Linux / macOS 用户

```bash
chmod +x build-exe.sh
./build-exe.sh
```

## 📋 详细流程说明

### 1️⃣ 环境准备

**Windows:**
```batch
# 检查 Node.js 是否安装
node --version
npm --version

# 如果没有，从 https://nodejs.org 下载安装
```

**Linux/macOS:**
```bash
# 检查 Node.js
node --version
npm --version

# Ubuntu/Debian
sudo apt-get install nodejs npm

# macOS (使用 Homebrew)
brew install node
```

### 2️⃣ 手动打包 (详细步骤)

如果你想手动控制每一步:

```bash
# 1. 安装依赖
npm install
cd server && npm install && cd ..

# 2. 构建前端 (生成 dist 文件夹)
npm run build

# 3. 编译后端
cd server
npm run build
cd ..

# 4. 生成 EXE (Windows)
npm run electron-build-win

# 或生成跨平台应用
npm run electron-build
```

## 📦 生成的文件说明

打包后你会得到:

```
dist/
├── ACE-Step-UI-1.0.0.exe          # Windows 标准安装版
├── ACE-Step-UI-1.0.0.exe          # Windows 便携版 (推荐)
└── ACE-Step-UI-1.0.0-Setup.exe   # 安装向导版
```

### 版本对比

| 类型 | 安装方式 | 文件大小 | 推荐场景 |
|------|--------|--------|--------|
| **便携版** | 解压即用，无需安装 | 较小 | 👑 **推荐**，U盘便携 |
| **安装版** | 运行安装向导，安装到 Program Files | 较大 | 正式安装，多用户共享 |
| **Setup 版** | 自动检测，智能安装 | 中等 | 一般用户 |

## 🚀 分发你的应用

打包完成后:

1. **本地测试**
   ```bash
   # 直接运行 EXE 测试
   .\dist\ACE-Step-UI-1.0.0.exe
   ```

2. **上传分享**
   - 上传到 GitHub Release
   - 分享到网盘
   - 发送给朋友

3. **用户使用**
   - 下载 EXE
   - 双击运行
   - 完成！🎉

## ⚙️ 高级配置

### 修改应用信息

编辑 `package.json` 中的 `build` 部分:

```json
{
  "build": {
    "appId": "com.ace-step-ui.app",
    "productName": "ACE-Step UI",
    "nsis": {
      "oneClick": false,
      "createDesktopShortcut": true
    }
  }
}
```

### 添加应用图标

1. 准备 PNG 图片 (512x512)
2. 使用工具转换为 ICO 格式
3. 放入 `assets/icon.ico`
4. 重新运行 `build-exe.bat`

## 🐛 常见问题

### Q: 打包失败，提示缺少依赖？
A: 运行:
```bash
npm install
cd server && npm install && cd ..
```

### Q: 生成的 EXE 很大？
A: 正常现象 (~200-300MB)，包含了:
- Electron 框架
- Node.js 运行时
- 所有依赖包
- 前后端代码

### Q: 如何减小 EXE 体积？
A: 在 `package.json` 中配置:
```json
{
  "build": {
    "win": {
      "artifactName": "${productName}.exe"
    }
  }
}
```

### Q: 打包后需要 Python 和 ACE-Step 吗？
A: **需要！** 用户需要:
- 安装 ACE-Step-1.5 AI 模型
- 运行 Gradio API
- 然后启动你打包的 EXE

你可以配合 Pinokio 一键安装来简化这个过程。

## 📚 相关文档

- [Electron 文档](https://www.electronjs.org/docs)
- [electron-builder](https://www.electron.build/)
- [ACE-Step 安装指南](./README.md#-installation)

## 💡 建议

1. **测试充分** - 在多个 Windows 版本上测试 EXE
2. **文档清晰** - 提供安装和使用说明
3. **用户反馈** - 收集用户意见不断改进
4. **版本管理** - 使用 semver 版本控制

---

**祝你打包顺利！🚀**

有任何问题，欢迎提交 Issue 或 PR！
