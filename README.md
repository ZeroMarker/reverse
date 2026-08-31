# reverse

中文逆向工程笔记与工具索引。内容面向合法的软件兼容性研究、安全审计、故障排查、数字取证与学习实验。

> 只分析你拥有或明确获准分析的目标。不要把本文档用于绕过授权、窃取数据、植入恶意代码或破坏系统。

## 从哪里开始

1. 阅读[入门基础](docs/getting-started.md)，理解文件格式、汇编、调用约定和静态/动态分析。
2. 按[标准分析流程](docs/workflow.md)建立隔离环境、留存样本哈希并记录证据。
3. 从[工具索引](docs/tools.md)按目标平台挑选最小工具集。
4. 查看对应专题：[原生程序](docs/native.md)、[Android](docs/android.md)、[.NET 与 Unity](docs/dotnet-unity.md)、[协议与网络](docs/protocol.md)、[固件与嵌入式](docs/firmware.md)。
5. 分析时配合[检查表与速查](docs/checklist.md)。

## 推荐的最小工具集

| 任务 | 首选 | 备选 |
| --- | --- | --- |
| 通用静态分析 | Ghidra | IDA Free、Cutter/radare2 |
| Windows 动态调试 | x64dbg | WinDbg |
| Linux 动态调试 | GDB + pwndbg | LLDB |
| 动态插桩 | Frida | DynamoRIO、Intel PIN |
| 文件与十六进制查看 | ImHex | 010 Editor、HxD |
| Android | JADX + Apktool + ADB | Ghidra、Frida |
| .NET / Unity | ILSpy、dnSpyEx | dotPeek、Ghidra |
| 网络协议 | Wireshark | mitmproxy（仅限获准流量） |

工具不是流程的替代品。优先回答“目标是什么、输入从哪里来、输出到哪里去”，再决定是否需要反编译器、调试器或插桩框架。

## 仓库结构

```text
docs/
├── getting-started.md   # 基础概念与学习路线
├── workflow.md          # 可复现的分析流程
├── tools.md             # 按用途分类的工具索引
├── native.md            # Windows/Linux/macOS 原生程序
├── android.md           # APK、DEX、资源与运行时分析
├── dotnet-unity.md      # 托管程序集与 Unity
├── protocol.md          # 流量捕获、TLS 与协议重建
├── firmware.md          # 固件解包、静态分析与仿真
└── checklist.md         # 分析记录模板和命令速查
```

## 贡献约定

- 新增工具必须附官方主页或官方仓库，并说明平台、许可证和适用场景。
- 不提交受版权保护的样本、密钥、账号数据或未经授权的二进制文件。
- 命令示例应能在隔离实验环境复现；危险命令必须解释影响。
- 优先记录分析依据，不把反编译器输出直接当作源码事实。

## License

[MIT](LICENSE)
