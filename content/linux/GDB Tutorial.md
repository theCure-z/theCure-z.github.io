# 启动

```bash
gdb [可执行文件]
gdb -p [进程id]
gdb [可执行文件] core
```

# 常用参数

| run [input] | r | 开始执行程序[ 带输入运行 ] |
| :---: | :---: | :---: |
| kill | k | 终止当前程序 |
| break | b | 设置断点 |
| delete | del | 删除断点 |
| continue | c | 继续执行至下一个断点 |
| next | n | 执行下一行（不进入） |
| step | s | 执行下一行（进入函数） |
| step instruction | si | 按汇编行执行 |
| next instruction | ni | 按汇编行执行（跳过 call 指令） |
| print | p | 打印变量或表达式的值 |
| backtrace | bt | 显示调用栈 |
| frame | f | 选择栈帧 |
| list | l | 显示源代码 |
| info | i | 显示断点、变量等信息 |
| quit | q | 退出 |
| disassemble | disas | 当前函数汇编 |
| finish |  | 退出当前函数 |
| focus [win] |  | 切换窗口焦点 |
| info win |  | 显示当前窗口焦点 |
| x |  | 查看内存 |
| refresh |  | 刷新 tui 下的全局缓冲区 |
| layout asm/regs |  | 进入汇编 /寄存器 界面 |
| set disassembly-flavor intel/att |  | 切换为 intel、at<br/>语法  |
| shell |  | 执行 bash 命令 |

在 s 进入函数时，如果编译时没有带 gdb -g 添加函数内部行信息，是无法在函数内按行进行的，step 会退化成 next

# 断点设置

```bash
break main.c:10       # 在main.c的第10行设置断点
break function_name   # 在函数入口设置断点
break *0x8048000      # 在内存地址设置断点
 
info breakpoints      # 查看所有断点
delete 2              # 删除编号为2的断点
disable 1             # 禁用编号为1的断点
enable 1              # 启用编号为1的断点
 
# 条件断点
break 15 if x == 5    # 当x等于5时在第15行中断
```

# 查看变量

```bash
print x               # 打印变量x的值
print/x x             # 以十六进制打印x
print array[10]@5     # 打印数组从第10个元素开始的5个元素
print *(int*)0x1234   # 打印内存地址0x1234处的整数值
 
set var x = 10        # 修改变量x的值为10
set {int}0x1234 = 10  # 修改内存地址0x1234处的值为10
```

# 查看汇编

`x/10xw 0x7ffffffffde00`

x:查看内存
10:向后查看 10 个单位内存（地址增大方向）
x:按十六进制显示  其他格式按照 printf 中一致
w:以一个词（4 个字节作为单位）b:1 字节 h:2 字节 g:8 字节