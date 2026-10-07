# 舒尔特方格 · 手机本地 App

基于 Capacitor 的安卓本地应用，零依赖单文件网页实现（无框架、无构建步骤）。

## 功能

- **规格**：3×3 / 4×4 / 5×5 / 6×6
- **模式**：正序、降序、多色（干扰项）
- **计时**：首次点击才起表；结束展示总用时、平均反应、单次最快、准确率
- **折线图**：结果页展示上一局每次点击耗时（canvas 手绘，含均线，最慢一点标红）
- **排行榜**：历史成绩按用时排序，保留最近 50 局，可一键清空
- **反馈**：按键音（WebAudio 合成，无音频文件）、震动（Navigator.vibrate）、点击闪色
- **持久化**：localStorage 保存最好成绩（按 规格_模式 分开）与历史记录

## 目录结构

```
schulte-app/
├── www/index.html          # 全部应用代码（单文件）
├── capacitor.config.json   # Capacitor 配置
├── android/                # Capacitor 生成的安卓原生工程
└── package.json
```

## 开发流程

改完 `www/index.html` 后同步进安卓工程再构建：

```bash
npx cap sync android
cd android && ./gradlew assembleDebug
```

## 本机构建环境（便携版，装在 C 盘根目录）

| 组件 | 位置 |
|---|---|
| JDK 21 (Temurin，构建用) | `C:\android-build-tools\jdk-21` |
| JDK 17 (Temurin，备用) | `C:\android-build-tools\jdk-17` |
| Android cmdline-tools | `C:\android-build-tools\cmdline-tools\latest` |
| Android SDK | `C:\android-sdk`（platform 35 + build-tools 34.0.0） |
| Gradle 8.11.1 | 已装入 `~/.gradle/wrapper/dists`（腾讯云镜像下载） |

> 注意：JDK 目录名不要带 `+` 等特殊字符（Windows 路径解析问题），`local.properties` 的 `sdk.dir` 必须用正斜杠。
> Maven 依赖走阿里云镜像（`android/init.gradle`），Gradle 发行包走腾讯云镜像——本机直连官方源超时。

构建前设置环境变量：

```bash
export JAVA_HOME="C:\android-build-tools\jdk-21"
export ANDROID_HOME="C:\android-sdk"
```

Capacitor 8 要求 Java 21+，用 JDK 17 会报“无效的源发行版：21”。

## 产物

- Debug APK（已签 debug 证书，可直接安装）：`舒尔特方格-v1.0-debug.apk`（项目根目录）
- 原始输出：`android/app/build/outputs/apk/debug/app-debug.apk`

## 打正式包（可选）

1. 生成签名密钥：`keytool -genkey -v -keystore my-release.keystore -alias schulte -keyalg RSA -keysize 2048 -validity 10000`
2. 配置 `android/app/build.gradle` 的 signingConfigs
3. `./gradlew assembleRelease`
