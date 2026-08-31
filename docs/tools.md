# 工具索引

下面优先收录仍在维护且有官方文档的工具。选择工具时关注目标格式、架构、平台、许可证、自动化能力与团队协作需求。

## 反汇编与反编译

| 工具 | 平台/目标 | 特点 | 官方资源 |
| --- | --- | --- | --- |
| Ghidra | 跨平台，多架构 | 免费开源；反汇编、反编译、脚本和协作 | [项目](https://github.com/NationalSecurityAgency/ghidra) |
| IDA | 跨平台，多架构 | 成熟的交互分析生态；不同版本功能和许可不同 | [IDA](https://hex-rays.com/ida-pro) / [IDA Free](https://hex-rays.com/ida-free) |
| radare2 | 跨平台，多架构 | 命令行优先、可脚本化、适合自动化 | [项目](https://github.com/radareorg/radare2) / [官方书](https://book.rada.re/) |
| Cutter | 跨平台，多架构 | 基于 Rizin 的图形界面 | [项目](https://github.com/rizinorg/cutter) |
| Binary Ninja | 跨平台，多架构 | 多层中间语言和良好的 API | [官网](https://binary.ninja/) |

## 调试器

| 工具 | 主要场景 | 官方资源 |
| --- | --- | --- |
| x64dbg | Windows x86/x64 用户态调试 | [项目](https://github.com/x64dbg/x64dbg) / [文档](https://help.x64dbg.com/) |
| WinDbg | Windows 用户态、内核、转储分析 | [Microsoft 文档](https://learn.microsoft.com/windows-hardware/drivers/debugger/) |
| GDB | Linux/Unix 原生程序 | [GNU 文档](https://sourceware.org/gdb/documentation/) |
| LLDB | LLVM 生态、macOS/Linux | [LLVM 文档](https://lldb.llvm.org/) |
| pwndbg | GDB/LLDB 的逆向与漏洞研究界面增强 | [项目](https://github.com/pwndbg/pwndbg) |

## 动态插桩与系统观察

| 工具 | 适用场景 | 官方资源 |
| --- | --- | --- |
| Frida | 跨平台进程插桩，移动端尤其常用 | [文档](https://frida.re/docs/home/) |
| strace | Linux 系统调用与信号跟踪 | [官网](https://strace.io/) |
| ltrace | Linux 动态库调用跟踪；维护缓慢，结果建议用 GDB 在 PLT 处设断交叉验证 | [项目](https://gitlab.com/cespedes/ltrace) |
| Process Monitor | Windows 文件、注册表、进程/线程活动 | [Sysinternals](https://learn.microsoft.com/sysinternals/downloads/procmon) |
| Process Explorer | Windows 进程、句柄和 DLL 检查 | [Sysinternals](https://learn.microsoft.com/sysinternals/downloads/process-explorer) |

## 二进制与文件格式

| 工具 | 用途 | 官方资源 |
| --- | --- | --- |
| ImHex | 十六进制编辑、模式描述和数据可视化 | [项目](https://github.com/WerWolv/ImHex) |
| Kaitai Struct | 声明式描述并解析二进制格式 | [官网](https://kaitai.io/) |
| binwalk | 固件识别、嵌入数据定位和熵分析 | [项目](https://github.com/ReFirmLabs/binwalk) |
| Detect It Easy | 文件类型、编译器、打包器识别 | [项目](https://github.com/horsicq/Detect-It-Easy) |
| YARA | 基于文本与二进制特征的规则匹配 | [文档](https://yara.readthedocs.io/) |

## 平台专用

| 工具 | 目标 | 官方资源 |
| --- | --- | --- |
| JADX | DEX/APK 到可读 Java 代码，带 GUI/CLI | [项目](https://github.com/skylot/jadx) |
| Apktool | APK 资源和 smali 解码、重建 | [官网](https://apktool.org/) |
| ADB | Android 设备通信、日志与调试 | [Android 文档](https://developer.android.com/tools/adb) |
| ILSpy | 跨平台 .NET 反编译 | [项目](https://github.com/icsharpcode/ILSpy) |
| dnSpyEx | .NET/Unity 程序集查看、调试与编辑 | [项目](https://github.com/dnSpyEx/dnSpy) |

## 网络与协议

| 工具 | 主要用途 | 官方资源 |
| --- | --- | --- |
| Wireshark | 抓包、协议解析、流重组 | [文档](https://www.wireshark.org/docs/) |
| mitmproxy | 获准环境中的交互式 HTTP(S) 代理 | [文档](https://docs.mitmproxy.org/) |
| Burp Suite | 获准 Web/API 测试与流量分析 | [文档](https://portswigger.net/burp/documentation) |

TLS 拦截需要控制客户端信任或测试证书。不要在未获授权的设备、账号或网络上拦截流量。

## 如何选择

- **第一次分析 Windows 原生程序**：Ghidra + x64dbg + Process Monitor。
- **Linux 命令行程序**：Ghidra + GDB/pwndbg + strace。
- **Android APK**：JADX 看代码，Apktool 看资源/smali，ADB 看运行状态，必要时使用 Frida 验证。
- **.NET/Unity**：先用 ILSpy；需要运行时调试或程序集编辑时再考虑 dnSpyEx。
- **协议与网络**：Wireshark/tshark 看流量，mitmproxy 查看 TLS 明文（需控制信任），Kaitai 固化解析器。
- **固件与嵌入式**：binwalk 解包，ImHex 查看结构，Ghidra 按架构分析，QEMU 验证行为。
- **大规模自动化**：优先选择有稳定 CLI/API 的 radare2、Ghidra headless、JADX CLI、YARA 与自编脚本。
