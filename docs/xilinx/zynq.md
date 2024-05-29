# ZYNQ

![zynq_block_design](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/soc/zynq_block_design.webp)

> ```
> Project family: Zynq-7000
> Project part: xc7z020clg400-2
> ```
>
> Xilinx Commercial, 7 Series, Zynq, Value Index = 020, cl: Wire-bond Molded, Package Pin Count = 400(0.8mm), Speed Grade-2

## 简介

ZYNQ 系列包含了完整的 ARM 处理子系统（PS），每颗 ZYNQ 系列处理器都包含 Cortex-A9 处理器。

- PS：ARM 的 SoC 部分，AMBA 互联，内部存储器，外部存储器接口和外设
- PL：FPGA 部分

### PS 与 PL 互联

AXI(Advanced Extensible Interface)协议，主要描述了主从设备之间的数据传输，ZYNQ 中的版本是 AXI4.0

> AXI 是 ARM 公司提出的 AMBA(Advanced Microcontrooler Bus Architecture)的一部分，是一种高带宽低延迟的片内总线。

在 ZYNQ 中，支持 AXI-Lite，AXI4 和 AXI-Stream 三种总线，

|    接口协议    |          特性          |        应用        |
| :------------: | :--------------------: | :----------------: |
|  **AXI-Lite**  | 地址/单数据(32bit)传输 |   低速外设/控制    |
|    **AXI4**    |   地址/突发数据传输    |    地址批量传输    |
| **AXI-Stream** |      数据突发传输      | 数据流和媒体流传输 |

- **AXI-Lite**：轻量级，适合小批量数据传输、简单控制场合。读写时一次 32bit，不支持批量传输。用于访问低速外设和控制外设；
- **AXI4**：支持批量传输（具有数据读写的 burst 功能，对一片地址进行一次性读写）；

> 上述两种采用内存映射控制方式，ARM 将用户自定义 IP 编入某一地址进行访问，读写时就像在读写自己片内的 RAM。代价时资源占用过多，需要额外的读写地址线、控制线，写应答线等。
>
> <img src="https://cdn.jsdelivr.net/gh/Euler0525/tube@master/soc/axi-read_write.webp" alt="axi-read_write" width="90%" height="90%" />

- **AXI-Stream**：连续流接口（像 FIFO 一样一直读一直写），不需要地址线，因此不能通过内存地址映射的方式控制，需要有个转换装置；AXI-Stream 适合用于实时信号处理；

---

ZYNQ 中 **硬件实现** 了 AXI 总线协议，包括 9 个物理接口。其中有 2 个 AXI-GP 是主机接口（Master），可以主动访问 PL 逻辑，把 PL 映射到某个地址，读 PL 寄存器就像在读自己的存储器；其余 7 个为从机接口（Slave），被动接受来自 PL 的读写。

- **AXI-GP**：两个 32 位主设备接口和两个 32 位从设备接口；
- **AXI-HP**：高性能高带宽的 AXI3.0 接口，用于 PL 访问 PS 的存储器（DDR 和 On-chip RAM），总共由四个；
- **AXI-ACP**：用于管理 DMA 之类不带缓存的 AXI 外设，PS 端时 Slave 接口；

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube@master/soc/axi-acp_hp_gp.webp" alt="axi-acp_hp_gp" width="75%" height="75%" />

其中 GP 是 32 位的低性能接口，理论带宽约为 600MB/s，HP 和 ACP 接口为 64 位高性能接口，理论带宽为 1200MB/s。

位于 PS 的 ARM 由直接硬件支持的 AXI 接口，PL 需要使用逻辑实现相应的 AXI 协议

- **AXI-DMA**：从 PS 内存到 PL 高速传输通道的转换，AXI-HP↔AXI-Stream；
- **AXI-FIFO-MM25**：从 PS 内存到 PL 通用传输通道的转换，AXI-GP↔AXI-Stream；
- **AXI-Datamover**：从 PS 内存到 PL 高速传输高速通道的转换，AXI-HP↔AXI-Stream，完全由 PL 控制；
- **AXI-CDMA**：PL 将数据从一个位置搬移到另一个位置；

### 中断

ZYNQ 中断分为三部分，

- SGI，软件生成的中断，16 个端口；
- PPI，CPU 私有外设中断，5 个；
- SPI，共享外设中断，来自 44 个 PS 端 IO 外设以及 16 个 PL 端中断

中断控制器（GIC, Generic Interrupt Controller）用于对中断进行使能、关闭、掩码、设置优先级等。

## Block Design

![zynq_block_design_plus](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/soc/zynq_block_design_plus.png)

### ZYNQ Block Design

ZYNQ 硬核架构图，绿色部分是可配置模块。

### PS-PL Configuration

PS 与 PL 之间接口的配置主要聚焦于 AXI 接口，这些接口能够扩展 PL 端的 AXI 接口外设。因此，当 PL 需要与 PS 进行数据交互时，必须遵循 AXI 总线协议。Xilinx 为我们提供了丰富的 AXI 接口 IP 核。

### <a id="MIO"> Peripheral I/O Pins </a>

图形化配置界面（ZYNQ 的 PS 端外设是复用的，相同的引脚标号可以配置成不一样的功能，但只能同时配制成一种外设）

- PS 端分为两个 `Bank`，分别为原理图中的 `Bank500,501`，分别配置为 `LVCMOS3.3V` 和 `LVCMOS1.8V`；
- 根据原理图，串口连接在 PS 的 `MIO48,49` 上，因此在选项中使能 UART1(MIO48 MIO49)；
- `Quad SPI Flash, QSPI`：作为 ZYNQ 的启动存储设备，ZYNQ 通过读取 QSPI 中存储的启动文件加载 ARM 和 FPGA，选择 `Single SS 4bit IO`
- `Ethernet`：以太网接口，根据原理图，配置 `MDIO` 为 `MIO52,53`

*（同理，可根据原理图第 4 页配置）*

**[Run Block Automation]** 后将 `M_AXI_GP0_ACLK` 与 `FCLK_CLK0` 相连。

## GPIO

> **UG585-ZYNQ_7000_SoC**

Xilinx ZYNQ 提供了 **MIO**、**EMIO**、**AXI_GPIO** 三种类型的 GPIO 接口。

- MIO 是属于 PS 端的固定 IO 口，使用时不需要消耗 PL 端的资源；
- EMIO 是通过 PL 进行扩展的 IO 口，使用时需要分配 PL 端的引脚，消耗 PL 端资源；
- AXI_GPIO 是 Xilinx 封装好的 IP 核，是 PS 端通过 AXI GP 总线控制 PL 端的 IO 口技术，使用时需要消耗 PL 端资源。

### MIO

> **UG585-ZYNQ_7000_SoC**：Figure 14-1 **GPIO Block Diagram**(P410)

ZYNQ7000 系列芯片有 54 个 MIO，它们分配在 GPIO 的 Bank0 和 Bank1，隶属于 PS 部分，这些 IO 与 PS 直接相连使用时不需要添加引脚约束，**对 PL 部分不可见**。所以对 MIO 的操作可以看作是纯 PS 的操作。用 SDK 软件操作底层都是对于内存地址空间的操作。

---

MIO 或 EMIO 使用时需要调用 API 函数：

1. 根据设备 ID 返回结构体 XGpioPs_Config 的配置

```c
XGpioPs_Config *XGpioPs_LookupConfig(u16 DeviceId);
```

2. 初始化 GPIO，主要完成对 XGpioPs 结构体的配置

```c
s32 XGpioPs_CfgInitialize(XGpioPs *InstancePtr, XGpioPs_Config *ConfigPtr, u32 EffectiveAddr);
```

3. 为指定的引脚配置输入输出方向（对方向寄存器 `DirModeReg` 使能）。`0` 输入，`1` 输出

```c
void XGpioPs_SetDirectionPin(XGpioPs *InstancePtr, u32 Pin, u32 Direction);
```

4. 设置指定引脚输出使能（对 `OpEnableReg` 寄存器配置）。OpEnableReg 与 DirModeReg 属于逻辑与的关系，只有两个都配置成功才能实现 MIO 的输入或者输出的配置。

```c
void XGpioPs_SetOutputEnablePin(XGpioPs *InstancePtr, u32 Pin, u32 OpEnable);
```

5. 从指定引脚读出数据。返回值是从 DATA_RO 寄存器内取出读到的数据

```c
u32 XGpioPs_ReadPin(XGpioPs *InstancePtr, u32 Pin);
```

5. 对指定引脚写入数据。对 DATA 寄存器、DATA_MSW 寄存器、DATA_LSW 寄存器进行配置，共同完成输出数据的写入。

```c
void XGpioPs_WritePin(XGpioPs *InstancePtr, u32 Pin, u32 Data);
```

例如，利用 MIO 使 PS 端 LED 灯闪烁：

```c
#include "xparameters.h"  // 宏定义查找
#include <stdio.h>
#include "platform.h"
#include "xil_printf.h"
#include "xgpiops.h"
#include "sleep.h"

#define GPIO_DEVICE_ID		XPAR_XGPIOPS_0_DEVICE_ID  // "xparameters.h"
XGpioPs Gpio;

int main() {
	init_platform();

	int Status;
	XGpioPs_Config *ConfigPtr;

    // 初始化 GPIO
	ConfigPtr = XGpioPs_LookupConfig(GPIO_DEVICE_ID);  // (1) 配置
	Status = XGpioPs_CfgInitialize(&Gpio, ConfigPtr, ConfigPtr->BaseAddr);  // (2)
	if (Status != XST_SUCCESS) {
		return XST_FAILURE;
	}
    // 设置方向为输出方向，并使能输出
    XGpioPs_SetDirectionPin(&Gpio, 0, 1);     // (3) 设置IO的输入输出方向
    XGpioPs_SetOutputEnablePin(&Gpio, 0, 1);  // (4) 使能对应的IO
    XGpioPs_SetDirectionPin(&Gpio, 13, 1);
    XGpioPs_SetOutputEnablePin(&Gpio, 13, 1);
	while(1){
		// 0 → 1 → 0 → ...
		XGpioPs_WritePin(&Gpio, 0, 0x0);      // (5) 对IO读写操作
		XGpioPs_WritePin(&Gpio, 13, 0x0);
		sleep(1);
		XGpioPs_WritePin(&Gpio, 0, 0x1);
		XGpioPs_WritePin(&Gpio, 13, 0x1);
		sleep(1);
	}
	cleanup_platform();
	return 0;
}
```

> ```c
> // 设置按键为输入方向、上升沿触发并使能
> XGpioPs_SetDirectionPin(&GPIO_PTR, PS_KEY_MIO, GPIO_INPUT);
> XGpioPs_SetIntrTypePin(&GPIO_PTR, PS_KEY_MIO, XGPIOPS_IRQ_TYPE_EDGE_RISING);
> XGpioPs_IntrEnablePin(&GPIO_PTR, PS_KEY_MIO);
> ```

### EMIO

> **[Customize Block] → [MIO Configuation]** 选择 **EMIO**

扩展 MIO，依然属于 ZYNQ 的 PS 部分，只是连接到了 PL 上，再从 PL 的引脚连到芯片外面实现数据输入输出。 **无论是 EMIO 还是 MIO 都属于 PS 上的 IO，直接由 PS 操作，PS 操作 MIO 和 EMIO 的方法类似**。

MIO 分布在 BANK0，BANK1，而 EMIO 则分布在 BANK2、BANK3，参考 **[*Figure 14‐1*: GPIO Block Diagram]**

1. MIO 在 ZYNQ 上的管脚是固定的，而 EMIO 是通过 PL 部分扩展的，所以使用 EMIO 时候需要在约束文件中分配管脚，并且设计 EMIO 的程序时，需要生成 PL 部分的 bit 文件烧写到 FPGA 中；

2. MIO 共占 54bit，**占用 IO 号为 0-53**，配置方法见 [Block Design/Peripheral I/O Pins](#MIO) 部分；EMIO 占 64bit，**占用 IO 号为 54-117**；

3. **<u>MIO和EMIO都是直接挂在PS上的GPIO</u>**，由 PS 操作。调用头文件 `xgpiops.h` 即可；**<u>AXI_GPIO是通过AXI总线挂在PS上的GPIO上</u>**，使用 AXI_GPIO 时，需要调用头文件 `xgpio.h`；

### AXI GPIO

AXIGPIO 由 FPGA 的 PL 逻辑核功能实现，相当于 GPIO 的 IP 核，通过 AXI 总线挂在 PS 上的 GPIO 上，耗费 PL 端的逻辑资源。

## IP Catalog

### Clock Wizard

锁相环(PLL)，用于产生 $50Hz$ 的分频或倍频。

- `locked` 信号：观察输入时钟是否锁定，如果输入时钟信号锁定，就会输出一个 locked 高电平信号。

`locked` 信号是在输入信号稳定之后再输出一个 `locked` 信号，刚开始 locked 信号是低电平，等到时钟信号稳定之后他就会拉高。

### Block Memory Generator

用于缓存数据。

- `Memory Type` 常用的是 `Simple Dual Port RAM`，两个端口（读写+只读）输入和输出信号独立；

- `Primitives Output Register`：（在输出数据加上寄存器，可以有效改善时序，但读出的数据会落后地址两个周期）多数情况下不使能它，保持数据落后地址一个周期；
- `Width` 表示数据位宽，`Depth` 表示存储数据的个数；

### [FIFO Generator](https://euler0525.github.io/wiki/programming/verilog/)

- FIFO 是为了缓存数据，因此两边的时钟通常不同，所以选择 `Independent Clocks Block RAM`；

- `FWFT` 读模式：`rd_en` 信号有效时，有效数据 `D0` 已经在数据线上准备好有效了，不会再延后一个周期（与 `Standard FIFO` 的区别）

### DDS Compiler

#### Configuration

**Configuration Options**

- **Phase Generator and SIN/COS LUT**：一个相位生成器和用于生成正余弦波形的查找表
- **Phase Generator Only**：只包含相位生成器（自定义波形生成）
- **SIN/COS LUT Only**：仅包含正余弦查找表（用于固定频率）

**System Requirements**

- **System Clock(MHz)**：系统时钟频率 $f_{clk}$
- **Number of Channels**：通道数。单个 IP 核实现多个独立的频率和相位输出
- **Mode of Operation**
  - **Standard**：见 [设计原理](https://euler0525.github.io/blogs/posts/c8c59f13/)
    - **Rasterized**：生成固定的频率点（输出频率不是连续可变的，而是在一些固定的频率值跳变），间隔由相位累加器的模数 $M$ 决定

> $$
> \Delta f = \dfrac{f_{clk}}{M}
> $$

- **Parameter Selection**：

**System Parameters**：

将 IP 核集成到更广泛系统中所需的参数，如 IP 核的时钟配置、接口类型、以及可能涉及到的系统级优化和功能使能

- **Spurious Free Dynamic Range(SFDR)**： 无杂散动态范围。信号最大幅度与最大杂散或噪声幅度之间的差异。SFDR 越高信号的纯净度越高
- **Frequency Resolution**：频率分辨率，决定了输出频率的精度
- **Noise Shaping**：在频率域内重新分配量化噪声，将其分配到不敏感的频率区域。

**Hardware Parameters**：

涉及 IP 核内部硬件实现的细节直接影响 IP 核的性能和资源消耗

- **Phase Width**：相位累加器的位宽，越宽频率分辨率越高
- **Output Width**：输出波形位宽，决定了波形最大幅度和幅度分辨率

#### Implementation

**Phase Increment Programmability**

- **Fixed**：相位增量固定，频率不可更改
- **Programmable**：相位增量可以在运行时修改，允许动态修改频率
- **Streaming**：相位增量可以实时更新（连续变化频率）

**Phase Offset Programmability**

- **None**：没有初始相位偏移
- **Fixed**：相位偏移固定，不可更改
- **Programmable**：可以在在运行时修改

**Output**

- **Output Selection**：正弦、余弦或两者同时输出（`sin` 在高位，`cos` 在低位）
- **Polarity**：将相位增加 $\pi$

**Implementation Options**

- **Has Phase Out**：输出相位信息
- **Memory Type**：查找表存储方式
- **Optimization Goal**：优化速度或面积；**DSP48 Use**：DSP48 资源使用程度

### Fast Fourier Transform

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube/ic/fft_configuration.webp" alt="fft_configuration" width="90%" height="90%" />

**Configuration**

- **Number of Channels**：多通道，实现多帧数据同时进行 FFT 运算
- **Transform Length**：FFT 长度
- **Architecture Configuration**
  - **Target Clock Frequency(MHz)**
    - **Target Data Throughput(MSPS)**

- **Architecture Choice**
  - **Automatically Select**：自动选择
    - **Piplined, Streaming I/O**：并行流水线结构（处理时间最短，资源消耗最大）
    - **Radix-4, Burst I/O**：基 4
    - **Radix-2, Burst I/O**：基 2
    - **Radix-2 Lite, Burst I/O**：

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube/ic/fft_implementation.webp" alt="fft_implementation" width="90%" height="90%" />

**Implementation**

- **Data Format**：**Fixed Point**（定位全精度）；**Floating Point**（定位缩减位宽）

- **Scaling Options**：

  - **Block Floating Point**：FFT 内部采用浮点，并根据每一级的数据自动缩放（输入输出位宽一致）
    - **Scaled**：`m_axis_data_tuser` 中有 $5bit$ 表示每一级的缩放情况
    - **Unscaled**：不用担心溢出

    - **Rounding Modes**：**Convergent Rounding**；**Truncation**

- **Precision Options**

  - **Input Data Width**：
    - **Phase Factor Width**：

- **Control Signals**
  - **ACLKEN**
    - **ARESETn(active low)**

- **Output Ordering Options**
  - **Output Ordering**：**Bit/Digit Reversed Order**；**Natural Order**
    - **Cyclic Prefix Insertion**

- Optional Output Fields：XK_INDEX；OVFLO
- Throttle Scheme：Non Real Time；Real Time

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube/ic/fft_detailed.webp" alt="fft_detailed" width="90%" height="90%" />

**Detailed Implementation**

- **Memory Options**
  - Data：Block RAM；Distributed RAM
    - Phase Factors：Block RAM；Distributed RAM
    - Number of stages using Block RAM for Data and Phase Factors
    - Reorder Buffer：Block RAM；Distributed RAM；Optimize Block RAM Count Using Hybrid Memories
- Optimize Options
  - Complex Multipliers
    - Butterfly Arithmetic

### FIR

**Hardware Oversampling Specification**

$Clock\space Frequency\space /\space Input\space Sampling\space Frequency = usmp\_rate$

### ILA(Integrated Logic Analyzer)

> 常用的 **在线调试** 方法：
>
> - ILA IP Catalog
> - VIO IP Catalog
> - `(* MARK_DEBUG="true" *)`，**[SYNTHESIS]** 综合后打开 **[Open Synthesized Design]**，右上角选择 **[Debug]** 模式，被标记的信号会有 🕷️ 标识。

**General Options**

- `Native`：常规普通接口模式
- `AXI`：AXI 接口模式，用于调试 AXI 接口信号
- `Number of Probes`：探针数量
- `Sample Data Depth`：采样数据深度，数值越大，采样的数据越多，看到的波形数据越多（最终占用的资源越多）

**Probe_Ports**

- `Probe Width`：根据代码中的定义设置位宽
- `Probe Trigger or Data`：设置为 `DATA AND TRIGGER`

例如：

```verilog
ila your_instance_name (
	.clk(clk), // input wire clk

	.probe0(probe0), // input wire [31:0]  probe0  
	.probe1(probe1) // input wire [3:0]  probe1
);
```

**调试界面**

bit 文件烧写后，会进入下图所示的 ILA 调试界面

![](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/prog/ila_debug.webp)

**Waveform 窗口**

通过 **[+/-]** 选择需要观察的波形（波形能否显示与 **Probe_Ports** 的 `Probe Trigger or Data` 参数设置有关）

**Status 窗口**

四个按钮分别为：**循环采样**、**条件触发采样**、**无条件执行 `ILA` 采样**、**停止采样按钮**
**Core status** 表示 `ILA` 的运行状态

## 参考资料

1. Zynq 7000 SoC Technical Reference Manual UG585 (v1.14) June 30, 2023
2. [ZYNQ 的三种 GPIO ：MIO、EMIO、AXI](https://www.elecfans.com/emb/fpga/20200922481156.html)

2. [zynq 中各种 GPIO 方式的区别：MIO，EMIO，AXI_GPIO 核](https://blog.csdn.net/ningjinghai11/article/details/80440683)

