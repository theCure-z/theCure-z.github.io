# Control Flow

从开机到关机,CPU 不断读取和执行一系列指令,每次执行一个,如果 CPU 是多核的,那么每个核心会交替执行,指令的序列就叫做 **Control Flow 控制流**

# Exceptional Control Flow

在汇编中有改变控制流的方式`jmp&branch``call&ret`,能让当前$rip 从一个指令转换至另一个指令,但在系统中进程会遇见其他情况,比如某个变量除以 0,比如用户按下`ctrl+C`,这时候应当改变当前控制流,称作 **Exceptional Control Flow 异常控制流处理(ECF)**

# Exception

kernel 内核是一个操作系统驻留在内存中的一部分代码,提供相应程序.
当一个事件导致异常发生时,控制流会跳转至内核代码中的异常处理程序,程序结果有三种

+ 返回原位置
+ 返回原位置处的下一条代码
+ 退出

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779005822036-581e5f45-a258-45e7-a4a4-03b5468305e4.png)

这个过程是一个由硬件和软件共同决定的过程,当异常 k 抛出时,系统查找 k 在异常表中记录的程序地址,硬件会修改$rip为对应的异常处理程序

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779006004468-0cd4b22f-48d8-4817-acda-a7cedb745380.png)

## 异步异常(中断)

何为异步,指异常和处理器执行的指令没有关系,而是某些外部事件发生了,这时异常的结果为返回至原程序的下一条指令,程序正常运行,
比如键盘输入时,程序中断并且跳转至处理键盘中断的程序,

**CPU 定时器**,类似 stm32 一样,每个一定时间就会拉高电平停止当前程序,并进入对应的异常处理程序,其意义在于,如果一个程序是循环运行且没有定时器,那么系统只能不断锁死在这个程序中,而有了定时器异常,能够将控制权定期转移给操作系统,系统能够决定切换运行程序或者中断死循环

`ctrl+C`,用户的外部输入引起异常从而终止进程

## 同步异常

同步异常和异步异常相反,异常来自于程序中的指令本身

+ Trap 陷阱,陷阱是故意的异常,
最经典的例子是`system call`,内核中存在操作系统函数,以及一些变量,但他们是受保护且无法使用`call`直接跳转,为了使外部程序能够调用他们,内核提供了一个接口(类似与通过 public 函数当作接口调用 private 函数或变量一样),当接口被调用时,异常将控制流转移至所需要的那个内核函数中,最终返回至原指令的下一条指令
![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779013114976-e72c07ae-53d8-434b-a2d3-e4ad73281d27.png)
open 函数的具体实现,调用 open 后会进入 __open 函数中,__open 是打包了 syscall 的一个函数,__open 通过将 2 写入\$rax,即 open 异常的编号,然后调用 syscall 触发同步异常,并将 open 得到的 FILE*返回给\$rax,如果\$rax是负数,则打开失败

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779013314496-c69eb70d-c519-4046-b238-d43779701e04.png)

+ Fault 故障,故障是无意的异常,但可能恢复,
例子是页缺失 page fault ,当某些数据不在内存中时(比如内存太小,而同时运行的程序太多,会将部分指令放回硬盘,需要时动态调用),触发页缺失故障,系统会将需要的数据转移至内存,可能重新执行原指令或发送 SIGSEGV 代表 segmentation fault 直接终止

+ Abort 终止,终止是无意但无法恢复的异常
例子,非法指令

# Process

程序可以是.c 中的代码,可以是二进制文件中的代码段,而进程特指正在运行的程序的一个实例,进程拥有两个抽象:

+ 进程看起来能够使你独享当下的寄存器与 CPU,即使同时可能存在着多个进程,由上下文切换提供
+ 进程看起来能够拥有独属于自己的地址空间与内存空间,即使内存可能被多个进程共享,由虚拟内存提供

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779014616127-592bff6d-c153-448f-bf93-410b8ceeb2b1.png)

即使在单核系统上也能并发运行多个进程

PID:进程唯一的 ID

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779173148026-b00569b9-cd27-4443-b77a-f649a9d15d10.png)

子进程通过 pgid 和父进程共享 pid 组别

# MultiProcessing

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779016650874-64981a8e-5fc3-41c5-af52-b361e86763f6.png)

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779016670343-f852e17e-657c-4429-819b-af3ec525b5ee.png)

多进程运行的真实情况(**单核下**),当定时器或某些异常使得进程发生切换时,CPU 将当前进程寄存器存入该进程对应的空间中,并取出切换后的进程上一次存入的寄存器,以及其对应的地址空间,**这样切换地址空间和寄存器被称为上下文 context(全局变量虚拟地址一致但只是数值相同)**

# Concurrent Processing

并发进程值的是在两个程序从开始到结束的运行过程中,其运行时间上有重叠,注意并发 ≠ 并行,而是交替执行

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779017291185-3b0a19cc-1fcb-4f94-8dd8-1c66de7b3a54.png)

如上图,进程 A 运行被进程 B 中断,B 自然结束后运行进程 C,进程 C 被 A 中断,A 结束后运行进程 C,过程中 A 被 B 中断,C 被 A 中断,他们是两组并发进程,但是 B 是结束后运行的 C,他们的生命周期并无重叠,B 与 C 的关系称为 sequential 连续的进程'

# Context Switching

系统运行 A 进程,系统收到中断,控制流转移至 kernel,kernel 决定是否切换进程,kernel 交换上下文

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779017974106-223eeab4-9b00-4134-b7ff-f70ca107f555.png)

# System Call Error Handling

linux 提供了多种系统函数调用接口,其内部能实现异常并调用 system call,当失败时,他们通常返回-1,并在全局变量 errno 中给出原因

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779022524186-607f4589-0863-405a-9543-cc78a2a3bcc1.png)

`pit_d getpid(void)`获取当前进程 pid
`pid_t getppid(void)`获取父进程 pid

# Terminating Process

进程终止的原因:

+ 受到了终止信号
+ 从 main 函数中返回
+ 调用 exit 函数

`void exit(int status)`以 status 状态退出,正常下是 0,exit 函数调用后没有返回直接终止程序,比较特殊

# Creating Process

`int fork(void)`子进程就像父进程的副本:

+ 子进程拥有父进程地址空间的拷贝,意味着其内部栈空间代码段全局变量一致
+ 子进程拥有父进程文件描述符 FILE*的拷贝,意味着子进程拥有任何已打开的文件包括输入输出流
+ 父进程与子进程 PID 不一致

fork 只被调用一次但是返回两次,在子进程中返回 0,在父进程中返回子进程 PID,无法保证先返回的是子进程还是父进程,但他们都会从 fork 的下一条指令开始执行
判断 fork 返回是不是 0 来区分父子进程

```cpp
if(fork()==0){
    this is a child process;
}else{
    this is a parent process;
}
```

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779072414378-e3870ca9-a01d-41bb-b603-6a24bc2d5566.png)

使用进程图完成进程创建可视化

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779072634396-49efe028-07bf-42ec-bce6-65539d638d4c.png)

多重 fork

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779072861743-ff6f637f-322e-4894-adcb-ee800d2a72f0.png)

没用的循环 fork

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779090705303-c12125ff-cd2d-4b56-b2a8-aa5cf765a327.png)

# Reap Process;

当一个进程被终止后,不会完全消失,内核会保留其

+ PID
+ 退出码
+ 资源统计信息

内核实际上一直保存着他直到他被 Reap(回收),这样做父进程就能知道子进程的相关信息,这时的子进程被称为 zombie 进程,直到被父进程所回收(通过`wait`或`waitpid`,父进程能够知道子进程的退出状态),然后 kernel 再删除子进程
如果父进程没有回收 zombie 状态的子进程呢?假如父进程在回收子进程前终止了,这时候的子进程称为 orphan 孤儿进程,系统将安排第一个进程`init`PID=1 来回收子进程,比如通过 bash 调用 ls,本质上是通过 bash 进程 fork 一个子进程,在子进程中完成 ls,假如关闭 bash,那么 ls 没有了父节点,此时将交由 init 完成回收过程,**本质上,对 orphan 进程无需担心,因为存在长期运行的进程来代回收,只需要关心 zombie 进程,对于成百上千的 zombie 进程,不及时销毁他们和不清理指针指向的堆上空间一样,会造成严重的内存泄露**

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779091956820-287ff6d6-e2d1-4a8b-b6b9-add562928d3a.png)

不回收子进程的例子:使用 fork 后,让子进程退出,让父进程堵死在无限循环不回收,此时 zombie 进程只有在父进程被终止后才能由 init 回收
使用 `wait`完成进程回收，`wait`能停止父进程，直到某个子进程终止（没有指定哪一个），使用`waitpid`能等待指定子进程的终止

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779092335145-107f32c6-3221-48b3-b2f9-08c298e118c6.png)

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1779092785887-97784456-bf65-45c0-b8ab-f7a90488fb50.png)

# execve
前文提到，使用`fork`创建子进程实际上是父进程的 copy，如何在子进程中运行不同的程序?使用`int execve(char* filename, char* argv[], char* envp[])`能做到,`argv[0]==filename`,`envp`是环境变量列表,其能加载**二进制文件/脚本文件(以 shebang 开头的)**,但保留原有 PID.
所以,在 bash 中启动一个二进制文件流程为:

1. fork出一个bash子进程
2. 子进程最初是bash的copy
3. 子进程调用execve
4. 将 filename,argv,argc 压入栈中,并计算 argv 数量传入 argc
5. execve把bash映像替换成目标程序
6. 使用压入参数调用二进制文件`int main(argc, argv, envp)`
7. 父bash继续等待并管理子进程

为什么不直接在一个 bash 中启动呢？如果 bash 使用 execve 运行二进制文件，那么该 bash 自己也就消失了，也就不会回到 bash 中了