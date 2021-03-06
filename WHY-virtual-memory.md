---
title: WHY virtual memory
date: 2018-09-08 11:06:42
tags: OS
---

一个系统中的进程和其他进程共享CPU和主存资源。为了高效和避免出错，OS提供了VM机制，它给每个进程提供一个大的一致的私有的地址空间。
VM提供三个重要能力:

1. 将主存看作存储在磁盘上的地址空间的高速缓存，在主存只保留活动区域，并根据需要在磁盘和主存间来回传送数据，这样高效利用主存，而不是全部加载到主存
2. 给每个进程提供一致的地址空间，简化存储器
3. 保护进程不被其他进程破坏

#### 物理和虚拟寻址
主存被组织成M个连续的字节大小的单元组成的数组，每个字节都有一个唯一的物理地址(按字节编址)，称为PA。

使用虚拟寻址时，CPU通过生成一个虚拟地址VA来访问主存，由MMU进行地址翻译，从虚拟地址转化为物理地址，MMU利用的是主存中的页表来工作的

地址空间可以区分数据对象(字节)和它们的属性(地址)，允许数据对象有多个独立的地址，其中每个地址都取自不同的地址空间。主存中的每个字节都有一个选自虚拟地址空间的虚拟地址和一个选自物理空间的物理地址

分别描述三个重要能力
### VM as cache
VM被组织成存放在磁盘上的N个连续的字节大小的单元组成的数组，每个字节都有一个唯一的虚拟地址，也就是索引。磁盘上的内容被缓存在主存中，磁盘和主存以块为大小交互，也就是虚拟页VP
每个页为固定的P=2^p字节，类似的，物理存储器被分割为物理页，大小相同

在任意时刻，虚拟页被分为三个不相交集:

1. 未分配 0 + null
VM系统还未分配、创建的页，未被分配的块没有任何数据，因此不占用磁盘空间
2. 缓存的 1 + not null
当前缓存在物理存储器的已分配页
3. 未缓存的 0 + not null
没有缓存在物理存储器的已分配页

访问未分配的页会段错误

SRAM来表示片内cache,DRAM来表示主存。DRAM害怕大的miss，因此采用全相联映射，也就是说任何VP可以放在任何PP。DRAM总是write back而不是write through

#### Page table
和任何缓存一样，VM系统必须判断某个VP是否在DRAM中，如果是，还需要确定VP存放在哪个PP中，如果不明智，系统必须判断该VP存放在磁盘的哪个位置，在DRAM中选择一个牺牲页，并将VP从磁盘拷贝到DRAM中，替换牺牲页

page table将VP映射到PP，每次MMU将一个VP转换为PP时都会读取页表，OS负责维护page table，以及在磁盘和DRAM间传送页

页表是一个pte数组，每个pte有一个有效位和一个n位地址字段。有效位表明了该VP是否被缓存在DRAM，如果有效位为1，则地址字段表示DRAM中对应的PP的起始位置,如果为0，则空地址表示还未被分配，否则指向该VP在磁盘上的起始位置.

#### page hit
有效位1，则构造出这个字的物理地址

#### page fault
DRAM不命中称为page fault。发现有效位为0，触发page fault，调用内核中的page fault handler， 
1. 选出一个牺牲页，如果牺牲页被修改，则内核将其拷贝回磁盘，内核会修改牺牲页的pte，表示牺牲页已经牺牲了。
2. 从磁盘拷贝VP 3到存储器中的位置，更新pte，设置有效位为1，然后返回。

原来的程序会重新执行导致page fault的指令，这次会把导致缺页的VA发到MMU，现在该页已经缓存在DRAM中，所以page hit。

#### Alloc page
OS会分配虚拟页，例如调用malloc，会在磁盘上创建空间并更新pte为0+磁盘上位置。

利用局部性来解释VM的性能不差。

### VM as memory manager
利用DRAM来缓存通常更大的虚拟地址空间的页面。OS为每个进程维护一个单独的页表，也就是一个独立的虚拟地址空间。多个虚拟页面(不同进程的)可以映射到相同的物理页面，实现共享。

按需页面调度和独立的虚拟地址空间结合起来，对系统中存储器的使用造成了深远的影响，VM简化了链接和加载、代码和数据共享以及应用程序(ring 3)的存储器分配

1. 简化链接
独立的地址空间允许每个进程对应的elf使用相同的格式。文本从0x08048000开始。数据和bss节紧跟其后，栈占据地址空间最高的部分，向下生长，这样简化了链接器的实现，允许链接器生成全链接的可执行文件，这些可执行文件和物理存储器中代码、数据的最终位置相互独立

2. 简化加载
VM使得容易向存储器中加载 可执行文件 和共享 对象文件。elf中，text和data节是连续的，要把这些节加载到一个新创建的进程中，linux加载器分配虚拟页的一个连续chunk，从地址0x08048000开始，把这些虚拟页标记为无效，讲地址指向目标文件的适当位置。加载器从不实际拷贝数据，而是在每个页被初次引用时(要么CPU取指令，要么一条指令引用一个存储器位置)，VM系统会按需要调入数据页

3. 简化共享
进程共享相同的操作系统内核代码，共享标准库，安排多个进程共享这部分代码的一个拷贝。

4. 简化存储器分配
VM为用户进程提供malloc接口，OS分配适当个连续的VP，并映射到物理页面中，物理页面并不要求连续，因为有page table

### VM as memory guard
OS不允许用户进程修改它的只读文本，不允许读写内核中的代码和数据结构(上述为拷贝),不允许读写其他进程的私有存储器，不允许修改和其他进程共享的虚拟页面，除非所有共享者都显式允许它这么做。

在pte上增加许可位来控制访问，内核模式|读|写
运行在内核态的进程可以访问任何页面，运行在用户态的进程只能访问sup=0的页面
如果违反许可，则shell报告为segmentation fault

### 地址翻译
地址翻译建立了VAS到PAS的映射关系
CPU中的控制寄存器PTBR指向页表基址，n位虚拟地址包括虚拟页号+偏移。MMU利用VPN来选择适当的PTE，将PTE中的PPN和VPO组织起来，就是PA

当页命中时，完全由硬件处理，CPU执行的步骤:
1. 处理器生成VA，传给MMU
2. MMU生成PTE地址，从高速缓存或主存中取得PTE
3. 高速缓存或主存返回PTE
4. MMU构造物理地址，传给高速缓存或主存
5. 高速缓存或主存返回数据字

Page fault时，要求硬件和OS一起完成:
1-3同上
4. PTE中的有效位是0，MMU触发异常，控制转到pagefault handler
5. page fault handler确定牺牲页，如果这个页面是dirty，则换出到磁盘
6. page fault handler 调入新页面，更新PTE
7. 返回到原来进程，再次执行之前的指令

#### TLB
CPU每次产生一个虚拟地址，MMU就必须查阅一个PTE，以便将VA翻译为PA。系统在MMU中包括了一个关于PTE的小的缓存，叫TLB(translation lookaside buffer)

TLB每行都保存着一个由单个PTE组成的块。虚拟地址会分为TLBtag+TLB idx。

1. CPU产生VA
2-3 MMU从TLB取出PTE
4. MMU翻译为PA，发送到高速缓存或主存
5. 返回数据字给CPU

TLB不命中时，MMU必须从L1缓存中取出相应PTE，可能覆盖原TLB条目

#### 多级页表
用于压缩页表
如果一级页表为空，则二级页表根本不会存在
只有一级页表总是在主存中


















