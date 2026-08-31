# 固件与嵌入式

本章覆盖路由器、IoT 设备和嵌入式 Linux/RTOS 镜像的分析，对象应为你拥有或获准测试的设备与镜像。刷写和修改固件可能使设备变砖或失去安全更新，实验请使用可恢复的测试设备。

## 获取镜像

按优先级：

1. 厂商官网发布的升级包（合法、可复现，通常是先看的入口）；
2. 设备文档或开源项目指明的固件下载渠道；
3. 从设备的 SPI/NAND/eMMC 闪存芯片读取（需要夹具或编程器，且仅对自有设备操作）。

拿到文件后先做基本识别：

```bash
sha256sum firmware.bin
file firmware.bin
binwalk firmware.bin
```

## 识别与解包

- **binwalk** 定位嵌入的文件系统、内核、压缩流和证书，`-e` 会自动提取，但更推荐先 `binwalk` 查看签名列表、再用 `dd` 手工切出目标区域，避免误提取。
- 常见内容：U-Boot 引导头、压缩内核（zImage/Image + dtb）、rootfs（squashfs、jffs2、cramfs、UBI/UBIFS）、cramfs 配置分区。
- 高熵且无签名的区域可能是加密镜像；先查引导代码或配套工具确认密钥来源，不要假设能直接解开。
- 解包出的 rootfs 可以只读挂载或用 `unsquashfs` 展开：

```bash
unsquashfs -d rootfs root.squashfs
```

注意校验文件系统魔数与字节序；MIPS/ARM 大端镜像在 x86 工作站上用普通 `file`/`strings` 仍可识别，但解析工具需要支持对应端序。

## 静态切入点

- 启动脚本：`/etc/inittab`、`/etc/init.d/`、`rcS`，理清服务启动顺序和边界进程。
- 二进制清单：`busybox` 及其 applet 集合、自有守护进程、Web 服务器（GoAhead、lighttpd、Boa 等）。
- 高价值审计点：硬编码凭据与密钥、`system()`/`popen()` 调用中的格式化字符串拼接、网络服务的输入解析、NVRAM 读写。
- 交叉引用结合[原生程序分析](native.md)的方法：先分架构（ARM/MIPS/RISC-V、端序、软硬浮点），再选 Ghidra/IDA 的对应处理器模块。

## 运行与仿真

- **单二进制仿真**：`qemu-<arch> -L rootfs ./usr/bin/targetd` 用 rootfs 作 sysroot 直接运行用户态程序，适合快速验证解析逻辑。
- **系统级仿真**：QEMU 全系统模式配合内核与 rootfs，可复现启动流程；缺内核或依赖硬件外设时，工作量会显著上升，先评估是否值得。
- 仿真环境默认与真实硬件在外设、NVRAM 和时钟上不同，结论要标注差异。

## 常见误区

- 镜像里同时有内核与 rootfs，偏移切错会得到"能解包但内容乱"的结果；用文件系统魔数交叉确认。
- 厂商升级包常是外层容器（TRX、加密头、差分包），不是 rootfs 本身，需要先剥离外层结构。
- 静态发现的可疑字符串（默认口令、密钥）必须结合引用它的代码确认是否真的在运行路径上。
- 设备的调试接口（UART、JTAG）输出可能包含敏感信息，记录时同样需要按[检查表](checklist.md)处理密钥与个人数据。
