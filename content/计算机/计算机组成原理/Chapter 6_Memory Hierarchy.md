# Random-Access Memory(RAM)

通常为芯片封装,基本储存为一 bit 一个单元

![两种RAM类型](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776859017968-25af9077-dbcb-4252-b6d1-faa0fa4621c5.png)

+ DRAM 只需要 1 个晶体管而 SRAM 需要 4~6 个 
+ SRAM 成本更高
+ SRAM 读写速度更快
+ DRAM 使用电容储存电荷,会随时间漏电,需要定期刷新,SRAM 只要维持电源即可稳定维持
+ SRAM 比 DRAM 更可靠
+ SRAM 用于内存中的高速缓存,DRAM 则用于主内存和显存中
+ DRAM 和 SRAM 断电后均会丢失,均为易失性存储器

# Nonvolatile Memory

通用名称为 Read-Only Memory(ROM)

+ ROM:出厂时固定程序
+ PROM:能改写一次
+ EPROM:能被紫外线擦除
+ EEPROM:能被电擦除
+ FLASH:EEPROM 的延申,拥有按块擦除内容的能力,十万次后会磨损

尽管 EPROM\EEPROM\FLASH 已经不再是 Read-Only 了,但行业仍将其归类于 ROM 类中

# Hard Disk Drive(HDD)

传统机械磁盘,具有读写功能,与 FLASH 等读写的 ROM 不同的是使用探头探测盘片上的磁性来读写数据

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776861191614-64746e77-88b7-4215-ba3c-2d541d5bdb14.png)

每一个盘片上有两面,每一面嵌套着许多同心圆成为磁道,每一个磁道包含多个扇区存储数据,磁道之间分布间隙不存储数据

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776861755661-e49cf7bf-adf7-4bec-bb4a-0f87d024204f.png)

盘片绕轴旋转,同时针头绕臂旋转

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776861861409-2c10705c-73f0-4253-9163-30b1952a40b2.png)读取数据流程

1. 先移动至所需磁道的半径处(3-9ms,占据读取数据的主要时间)
2. 等待磁盘旋转
3. 读取所需区块

# Solid State Disk(SSD)

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776911140386-64bf80c5-66a8-441e-ab28-2a1742ca6a8a.png)

SSD 和磁盘的区别是没有机械部件,完全由 Flash 构成,以页为单位读取或写入数据,多个页组合成一块,一页只能在他所属的块被完全擦除后才能重新覆写

# Bus Structure

![内存使用BUS管线和CPU通信](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776860293241-c730b012-e217-46c7-b434-1838a89f3dfc.png)

例:使用`movq A, %rax` 将内存中的数据转移至寄存器中

1. 先将 A 的地址放至存储器管线上

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776860423601-3ce8ca8f-f57f-424b-8a2e-e72b51eb1f99.png)

2. 内存读取 A 地址存储的 8 字节数据,然后放置在管线上

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776860808859-ef1e9f7f-b8d8-403b-b42e-f78e5a3197a0.png)

3. 数据返回管线接口,CPU 读取数据,存储至%rax 中
磁盘等外设与 IO 链接的方式

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776862146802-75b98aa2-0061-4b02-bc00-90d3ab54aefa.png)

DMA 通过直接将磁盘和内存连接完成数据运输,因为数据读取速度太慢了,期间可以利用 CPU 同时工作

![](https://cdn.nlark.com/yuque/0/2026/png/58377233/1776910996097-f8002175-b3af-4416-8869-b3729751a4ca.png)

# 存储器层次

![](https://cdn.nlark.com/yuque/0/2026/jpeg/58377233/1776922022905-7b57961d-c2c0-4e72-b601-d8dc938efcd0.jpeg)

越往上速度越快,成本越高,每一层都有能力调用下层的数据

# Cache

对于金字塔的第 k 层设备而言,都作为更慢容量更大的第 k+1 层存储设备的缓存,由于局部性原理,cpu 更愿意访问更快速的第 k 层的缓存数据而不是 k+1 中更慢的数据

![](https://cdn.nlark.com/yuque/0/2026/jpeg/58377233/1776923030567-e4141163-c521-4306-8ac6-69dc3f8d2283.jpeg)
