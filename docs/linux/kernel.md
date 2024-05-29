# Linux  Kernel

```mermaid
graph LR
A(进入内核前) ---> B(初始化) --内核态→用户态--> C(新进程) ---> D(Shell程序)
```

## 进入内核前

开机之后，

- bootsect

主板上的固件程序 BIOS 将硬盘启动区的代码移到内存 `0x7c00` 处，并跳转到这个位置去执行。

从 `0x7c00` 移动到 `0x90000`，数据段寄存器 `DS` 和代码段寄存器 `CS` 设置为 `0x90000`，栈顶地址设置为 `0x9FF00`，栈指针寄存器 `SP` 设置为 `0xFF00`。

![](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/linux/1-bootsect.webp)

将操作系统的代码完全从内存搬到硬盘中。

![](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/linux/2-bootsect.webp)

- setup

系统将 system 模块的代码（除了 `bootsect` 和 `setup` 之外的程序，包括 `head.s` 和 `main.c` 等其它文件链接在一起）移动到内存的零地址处，同时再 `0x90000` 处放置一些临时信息（内存、硬盘、显卡等设备信息）。

![](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/linux/4-setup.webp)

## 初始化

在进入 `main` 函数之前，已经完成了下面操作，

![](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/linux/6-head.webp)

### 内存初始化

计算出边界值，将内存区域分为 **内核区**、**缓冲区** 和 **主内存区**

```plaintext
物理地址空间：
0x000000┌─────────────────┐
        │    内核代码      │
        │   和数据区段     │
        ├─────────────────┤
        │   缓冲区内存     │            
        │  (磁盘缓存等)    │            ← buffer_memory_end
        ├─────────────────┤ ← main_memory_start
        │    RAMDISK     │ (可选)
        │  (如果有定义)    │
        ├─────────────────┤
        │    主内存区      │
        │ (用户进程等)     │
        └─────────────────┘ ← memory_end (最大16MB)
```

维护一个数组 `mem_map` 标记每一个 `4K` 的页是否空闲，标记其使用次数。1M 以下时内核区，无权管理；1M~2M 的区间时缓冲区，2M 是缓冲区末端，2M 以上是主内存区域，初始化时没有内存申请，标记为 0.

### 中断初始化

设置中断描述符表中的一些中断，分为内核态(3)和用户态(0)。

![](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/linux/7-trap_init.webp)

### 块设备初始化

对硬盘的读写操作封装在一个`request`结构中。

### 控制台初始化

打开串口中断。

获取显示模式，往显存写内容，显示在屏幕上。

### 时间初始化

### 进程调度初始化

在全局描述符表中写入TSS和LDT，作为之后进程0的任务状态段和局部描述符信息；初始化task_struct数组，存放所有进程的信息；设置时钟中断和系统调用中断。

### 缓冲区初始化

双向链表+哈希表，实现缓冲区管理。

### 硬盘初始化

总的来说，对硬件设备的初始化，首先在某些IOP端口读写数据，开启它，然后向中断向量表添加一个中断，使CPU能够响应对这个硬件设备的动作，最后初始化一些数据结构来管理。

## 参考资料

[Euler0525/Linux-0.11](https://github.com/Euler0525/Linux-0.11)

[Linux 源码趣读](https://book.douban.com/subject/36573361/)