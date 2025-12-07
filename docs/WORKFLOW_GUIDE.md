# GitHub Actions 构建工作流使用指南 / Workflow Usage Guide

[English](#english) | [中文](#中文)

---

## 中文

### 概述

本项目已配置完整的 GitHub Actions 工作流，支持自动构建以下平台：

- 📱 **Android** - 生成多架构 APK（ARM64, ARMv7, x86_64, Universal）
- 🪟 **Windows** - 生成 ZIP 压缩包
- 🍎 **macOS** - 生成 DMG 安装包
- 📱 **iOS** - 生成 IPA 安装包
- 🐧 **Linux** - 生成 TAR.GZ 压缩包

### 如何触发构建

工作流配置为手动触发（`workflow_dispatch`），这意味着你需要手动启动构建过程。

#### 步骤：

1. **访问 GitHub Actions 页面**
   - 进入仓库主页
   - 点击顶部的 `Actions` 标签

2. **选择工作流**
   - 在左侧列表中找到 `Flutter Build` 工作流
   - 点击它

3. **运行工作流**
   - 点击右侧的 `Run workflow` 按钮
   - 选择要构建的平台（可以同时选择多个）：
     - ☑️ Build Android
     - ☑️ Build Windows
     - ☑️ Build macOS
     - ☑️ Build iOS
     - ☑️ Build Linux
   - 点击绿色的 `Run workflow` 按钮开始构建

4. **等待构建完成**
   - 构建过程可能需要几分钟到十几分钟
   - 你可以点击运行中的工作流查看实时日志

5. **下载构建产物**
   - 构建完成后，滚动到页面底部的 `Artifacts` 部分
   - 点击相应的压缩包下载：
     - `android-arm64-v8a` - ARM64 架构 APK
     - `android-armeabi-v7a` - ARMv7 架构 APK
     - `android-x86_64` - x86_64 架构 APK
     - `android-universal` - 通用 APK（包含所有架构）
     - `windows-release` - Windows 版本
     - `macos-release` - macOS 版本
     - `ios-release` - iOS 版本
     - `linux-release` - Linux 版本

### Android 签名配置（重要）

要构建已签名的 Android 版本，你需要配置以下 GitHub Secrets：

1. **生成 keystore 文件**
   ```bash
   cd android/app
   keytool -genkeypair -v -keystore keystore.jks -alias <your_key_alias> -keyalg RSA -keysize 2048 -validity 10000
   ```

2. **转换 keystore 为 Base64**
   ```bash
   base64 -i keystore.jks -o keystore_base64.txt
   ```

3. **在 GitHub 设置 Secrets**
   - 进入仓库的 `Settings` -> `Secrets and variables` -> `Actions`
   - 添加以下 Secrets：
     - `KEYSTORE_BASE64` - keystore.jks 的 Base64 编码内容
     - `KEYSTORE_PASSWORD` - keystore 密码
     - `KEY_ALIAS` - 密钥别名
     - `KEY_PASSWORD` - 密钥密码

### 工作流配置说明

工作流文件位于：`.github/workflows/build.yml`

#### 主要特性：

- **Flutter 版本**：3.35.3（可在文件顶部的 `FLUTTER_VERSION` 环境变量中修改）
- **Java 版本**：21（用于 Android 构建）
- **缓存优化**：启用了 Flutter 缓存以加快构建速度
- **并行构建**：不同平台可以并行构建

#### 自定义构建

如果你需要修改构建配置：

1. 编辑 `.github/workflows/build.yml` 文件
2. 修改相应平台的构建步骤
3. 提交更改，新配置将在下次运行时生效

### 常见问题

#### Q: 为什么 Android 构建失败？
A: 最常见的原因是缺少签名配置。请确保已正确配置所有必需的 Secrets。

#### Q: 可以自动触发构建吗？
A: 当前配置为手动触发。如果需要自动构建（例如在推送代码或创建 tag 时），可以修改工作流的 `on:` 部分。

#### Q: 构建产物会保留多久？
A: GitHub Actions 的构建产物默认保留 90 天。

#### Q: 可以只构建某个平台吗？
A: 可以！在运行工作流时，只勾选你需要构建的平台即可。

---

## English

### Overview

This project has a complete GitHub Actions workflow configured that supports automatic building for the following platforms:

- 📱 **Android** - Generates multi-architecture APKs (ARM64, ARMv7, x86_64, Universal)
- 🪟 **Windows** - Generates ZIP archive
- 🍎 **macOS** - Generates DMG installer
- 📱 **iOS** - Generates IPA installer
- 🐧 **Linux** - Generates TAR.GZ archive

### How to Trigger a Build

The workflow is configured for manual triggering (`workflow_dispatch`), which means you need to manually start the build process.

#### Steps:

1. **Visit GitHub Actions Page**
   - Go to the repository homepage
   - Click the `Actions` tab at the top

2. **Select Workflow**
   - Find the `Flutter Build` workflow in the left sidebar
   - Click on it

3. **Run Workflow**
   - Click the `Run workflow` button on the right
   - Select platforms to build (you can select multiple):
     - ☑️ Build Android
     - ☑️ Build Windows
     - ☑️ Build macOS
     - ☑️ Build iOS
     - ☑️ Build Linux
   - Click the green `Run workflow` button to start the build

4. **Wait for Build Completion**
   - The build process may take several minutes to over ten minutes
   - You can click on the running workflow to view real-time logs

5. **Download Build Artifacts**
   - After the build completes, scroll to the `Artifacts` section at the bottom
   - Click on the appropriate archive to download:
     - `android-arm64-v8a` - ARM64 architecture APK
     - `android-armeabi-v7a` - ARMv7 architecture APK
     - `android-x86_64` - x86_64 architecture APK
     - `android-universal` - Universal APK (includes all architectures)
     - `windows-release` - Windows version
     - `macos-release` - macOS version
     - `ios-release` - iOS version
     - `linux-release` - Linux version

### Android Signing Configuration (Important)

To build a signed Android version, you need to configure the following GitHub Secrets:

1. **Generate keystore file**
   ```bash
   cd android/app
   keytool -genkeypair -v -keystore keystore.jks -alias <your_key_alias> -keyalg RSA -keysize 2048 -validity 10000
   ```

2. **Convert keystore to Base64**
   ```bash
   base64 -i keystore.jks -o keystore_base64.txt
   ```

3. **Set up Secrets in GitHub**
   - Go to repository `Settings` -> `Secrets and variables` -> `Actions`
   - Add the following Secrets:
     - `KEYSTORE_BASE64` - Base64 encoded content of keystore.jks
     - `KEYSTORE_PASSWORD` - keystore password
     - `KEY_ALIAS` - key alias
     - `KEY_PASSWORD` - key password

### Workflow Configuration Details

The workflow file is located at: `.github/workflows/build.yml`

#### Key Features:

- **Flutter Version**: 3.35.3 (can be modified in the `FLUTTER_VERSION` environment variable at the top of the file)
- **Java Version**: 21 (for Android builds)
- **Cache Optimization**: Flutter cache is enabled to speed up builds
- **Parallel Builds**: Different platforms can build in parallel

#### Customizing Builds

If you need to modify the build configuration:

1. Edit the `.github/workflows/build.yml` file
2. Modify the build steps for the respective platform
3. Commit the changes, and the new configuration will take effect on the next run

### FAQ

#### Q: Why is the Android build failing?
A: The most common reason is missing signing configuration. Please ensure all required Secrets are properly configured.

#### Q: Can I automatically trigger builds?
A: Currently configured for manual triggering. If you need automatic builds (e.g., on code push or tag creation), you can modify the `on:` section of the workflow.

#### Q: How long are build artifacts kept?
A: GitHub Actions build artifacts are kept for 90 days by default.

#### Q: Can I build only a specific platform?
A: Yes! When running the workflow, just check the platforms you need to build.
