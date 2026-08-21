# 原生程序分析

本章覆盖 PE、ELF 和 Mach-O 中的机器码程序。

## 初步识别

Linux/macOS 常用命令：

```bash
file target
sha256sum target
strings -a -n 6 target | less
readelf -hW target
readelf -lW target
readelf -dW target
readelf -sW target
objdump -d -M intel target | less
```

其中 `strings` 只能提供线索：字符串可能被编码、压缩、运行时生成，也可能根本不会被执行。

Windows PE 可先查看：

- 架构、子系统、入口点与节区权限；
- 导入/延迟导入、导出和资源；
- 数字签名与版本信息；
- TLS 回调、异常目录、重定位与调试目录。

## 静态切入点

1. 从入口点分离运行库初始化与程序自身逻辑。
2. 搜索独特错误信息、路径、协议字段和格式字符串。
3. 查看字符串交叉引用，再向上追踪调用者。
4. 根据导入 API 识别文件、网络、加密、进程和 UI 行为。
5. 给结构体字段、枚举、函数和全局变量逐步恢复类型。

## 动态切入点

### Linux

```bash
strace -f -o strace.log ./target
gdb --args ./target arg1
```

在 GDB 中常用：

```gdb
start
info files
info proc mappings
break main
run
info registers
x/16gx $rsp
bt
```

### Windows

用 Process Monitor 建立文件/注册表/进程活动时间线，再在 x64dbg 中围绕入口、模块加载、关键 API 或已知函数地址设置断点。开启系统断点前先理解调用频率，否则容易得到大量噪声。

## 地址换算

分析时必须区分：

- 文件偏移（file offset）；
- RVA（相对映像基址）；
- VA（运行时虚拟地址）；
- ASLR 后模块的实际加载地址。

不要把反汇编器显示地址直接当成文件偏移。PE 节的 `VirtualAddress` 与 `PointerToRawData`、ELF 的段映射决定二者关系。

## 编译优化造成的错觉

- 函数可能被内联或拆分；
- 多个源码函数可能合并为相同实现；
- 循环可能展开或向量化；
- 尾调用会让常规调用/返回结构消失；
- 常量传播会删除原本存在的分支；
- 异常处理和协程会产生非直观控制流。

判断伪代码是否可信时，应检查汇编、调用约定、数据宽度以及实际运行结果。

## 加壳与混淆

异常高熵、很少的导入、可写可执行节、入口附近的解压循环或运行时新映射代码可能表明存在加壳，但任何单一特征都不能定论。优先确认授权和分析目的；不要为了“脱壳”而运行来源未知的样本于非隔离环境。
