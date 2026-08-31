# Android 逆向

APK 本质上是 ZIP 容器，常见内容包括 Manifest、资源表、DEX 字节码、签名信息、assets 和按 ABI 分类的原生库。

## 静态检查

```bash
sha256sum app.apk
unzip -l app.apk
apkanalyzer manifest print app.apk
apkanalyzer dex list app.apk
```

工具分工：

- **JADX**：将 DEX 反编译为便于阅读的 Java 代码；结果是近似表示，不保证可重新编译。
- **Apktool**：解码 Manifest、资源和 smali，并能重建修改后的实验 APK。
- **Ghidra/IDA**：分析 `lib/<abi>/*.so` 原生库。

基础命令：

```bash
jadx-gui app.apk
apktool d app.apk -o decoded
```

## 阅读顺序

1. Manifest：包名、组件、权限、导出状态、深链、备份与网络安全配置。
2. 资源：字符串、域名、布局、XML 配置与 assets。
3. 入口组件：Application、Activity、Service、Receiver、Provider。
4. 数据边界：Intent、Binder、文件、数据库、WebView、网络和 JNI。
5. 原生代码：`System.loadLibrary`、`native` 方法及 JNI 注册位置。

## 动态观察

只在自有设备、模拟器或明确授权的测试设备上操作。

```bash
adb devices
adb shell pm path com.example.app
adb shell dumpsys package com.example.app
adb logcat
adb shell pidof com.example.app
```

Frida 适合验证运行时参数、返回值和调用路径。应先从静态分析得到明确假设，再写最小化脚本观测；不要把网上来源不明的绕过脚本直接用于真实设备。

## 重建与签名

修改资源或 smali 后，重建 APK 并不保留原作者签名。测试包必须使用你控制的测试密钥，且不能作为原版应用的可信替代品：

```bash
apktool b decoded -o rebuilt.apk
zipalign -p 4 rebuilt.apk aligned.apk
apksigner sign --ks test-keystore.jks aligned.apk  # 未指定 --ks-pass 时会交互式提示输入密钥库口令
apksigner verify --verbose aligned.apk
```

不同签名方案、split APK/App Bundle、动态特性模块和 Play Integrity 会影响安装与运行，不能把“能重建”误解为“与原应用等价”。

## 常见误区

- JADX 显示的变量名和控制流不等同于源码。
- 只看 Java/Kotlin 会漏掉 JNI、WebView JavaScript 和动态加载代码。
- Manifest 中声明权限不代表实际使用，未声明也不等于无相关行为。
- 抓到域名不代表某条路径一定执行，必须结合调用关系或运行证据。
