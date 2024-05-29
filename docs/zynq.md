# ZYNQ

![zynq_block_design](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/soc/zynq_block_design.webp)

> ```
> Project family: Zynq-7000
> Project part: xc7z020clg400-2
> ```
>
> Xilinx Commercial, 7 Series, Zynq, Value Index=020, cl: Wire-bond Molded, Package Pin Count=400(0.8mm), Speed Grade-2

## 简介

ZYNQ系列包含了完整的ARM处理子系统（PS），每颗ZYNQ系列处理器都包含Cortex-A9处理器。

- PS：ARM的SoC部分，AMBA互联，内部存储器，外部存储器接口和外设
- PL：FPGA部分

### PS与PL互联

AXI(Advanced Extensible Interface)协议，主要描述了主从设备之间的数据传输，ZYNQ中的版本是AXI4.0

> AXI是ARM公司提出的AMBA(Advanced Microcontrooler Bus Architecture)的一部分，是一种高带宽低延迟的片内总线。

在ZYNQ中，支持AXI-Lite，AXI4和AXI-Stream三种总线，

|    接口协议    |          特性          |        应用        |
| :------------: | :--------------------: | :----------------: |
|  **AXI-Lite**  | 地址/单数据(32bit)传输 |   低速外设/控制    |
|    **AXI4**    |   地址/突发数据传输    |    地址批量传输    |
| **AXI-Stream** |      数据突发传输      | 数据流和媒体流传输 |

- **AXI-Lite**：轻量级，适合小批量数据传输、简单控制场合。读写时一次32bit，不支持批量传输。用于访问低速外设和控制外设；
- **AXI4**：支持批量传输（具有数据读写的burst功能，对一片地址进行一次性读写）；

> 上述两种采用内存映射控制方式，ARM将用户自定义IP编入某一地址进行访问，读写时就像在读写自己片内的RAM。代价时资源占用过多，需要额外的读写地址线、控制线，写应答线等。
>
> <img src="https://cdn.jsdelivr.net/gh/Euler0525/tube@master/soc/axi-read_write.webp" alt="axi-read_write" width="90%" height="90%" />

- **AXI-Stream**：连续流接口（像FIFO一样一直读一直写），不需要地址线，因此不能通过内存地址映射的方式控制，需要有个转换装置；AXI-Stream适合用于实时信号处理；

---

ZYNQ中**硬件实现**了AXI总线协议，包括9个物理接口。其中有2个AXI-GP是主机接口（Master），可以主动访问PL逻辑，把PL映射到某个地址，读PL寄存器就像在读自己的存储器；其余7个为从机接口（Slave），被动接受来自PL的读写。

- **AXI-GP**：两个32位主设备接口和两个32位从设备接口；
- **AXI-HP**：高性能高带宽的AXI3.0接口，用于PL访问PS的存储器（DDR和On-chip RAM），总共由四个；
- **AXI-ACP**：用于管理DMA之类不带缓存的AXI外设，PS端时Slave接口；

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube@master/soc/axi-acp_hp_gp.webp" alt="axi-acp_hp_gp" width="75%" height="75%" />

其中GP是32位的低性能接口，理论带宽约为600MB/s，HP和ACP接口为64位高性能接口，理论带宽为1200MB/s。

位于PS的ARM由直接硬件支持的AXI接口，PL需要使用逻辑实现相应的AXI协议

- **AXI-DMA**：从PS内存到PL高速传输通道的转换，AXI-HP↔AXI-Stream；
- **AXI-FIFO-MM25**：从PS内存到PL通用传输通道的转换，AXI-GP↔AXI-Stream；
- **AXI-Datamover**：从PS内存到PL高速传输高速通道的转换，AXI-HP↔AXI-Stream，完全由PL控制；
- **AXI-CDMA**：PL将数据从一个位置搬移到另一个位置；

### 中断

ZYNQ中断分为三部分，

- SGI，软件生成的中断，16个端口；
- PPI，CPU私有外设中断，5个；
- SPI，共享外设中断，来自44个PS端IO外设以及16个PL端中断

中断控制器（GIC, Generic Interrupt Controller）用于对中断进行使能、关闭、掩码、设置优先级等。

## Block Design

![zynq_block_design_plus](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/soc/zynq_block_design_plus.png)

### ZYNQ Block Design

ZYNQ硬核架构图，绿色部分是可配置模块。

### PS-PL Configuration

PS与PL之间接口的配置主要聚焦于AXI接口，这些接口能够扩展PL端的AXI接口外设。因此，当PL需要与PS进行数据交互时，必须遵循AXI总线协议。Xilinx为我们提供了丰富的AXI接口IP核。

### <a id="MIO">Peripheral I/O Pins</a>

图形化配置界面（ZYNQ的PS端外设是复用的，相同的引脚标号可以配置成不一样的功能，但只能同时配制成一种外设）

- PS端分为两个`Bank`，分别为原理图中的`Bank500,501`，分别配置为`LVCMOS3.3V`和`LVCMOS1.8V`；
- 根据原理图，串口连接在PS的`MIO48,49`上，因此在选项中使能UART1(MIO48 MIO49)；
- `Quad SPI Flash, QSPI`：作为ZYNQ的启动存储设备，ZYNQ通过读取QSPI中存储的启动文件加载ARM和FPGA，选择`Single SS 4bit IO`
- `Ethernet`：以太网接口，根据原理图，配置`MDIO`为`MIO52,53`

*（同理，可根据原理图第4页配置）*

**[Run Block Automation]**后将`M_AXI_GP0_ACLK`与`FCLK_CLK0`相连。

## GPIO

> **UG585-ZYNQ_7000_SoC**

Xilinx ZYNQ提供了**MIO**、**EMIO**、**AXI_GPIO**三种类型的GPIO接口。

- MIO是属于PS端的固定IO口，使用时不需要消耗PL端的资源；
- EMIO是通过PL进行扩展的IO口，使用时需要分配PL端的引脚，消耗PL端资源；
- AXI_GPIO是Xilinx封装好的IP核，是PS端通过AXI GP总线控制PL端的IO口技术，使用时需要消耗PL端资源。

### MIO

> **UG585-ZYNQ_7000_SoC**：Figure 14-1 **GPIO Block Diagram**(P410)

ZYNQ7000系列芯片有54个MIO，它们分配在 GPIO 的 Bank0 和Bank1，隶属于PS部分，这些 IO 与 PS 直接相连使用时不需要添加引脚约束，**对PL部分不可见**。所以对 MIO 的操作可以看作是纯 PS 的操作。用SDK软件操作底层都是对于内存地址空间的操作。

---

MIO或EMIO使用时需要调用API函数：

1. 根据设备ID返回结构体XGpioPs_Config的配置

```c
XGpioPs_Config *XGpioPs_LookupConfig(u16 DeviceId);
```

2. 初始化GPIO，主要完成对XGpioPs结构体的配置

```c
s32 XGpioPs_CfgInitialize(XGpioPs *InstancePtr, XGpioPs_Config *ConfigPtr, u32 EffectiveAddr);
```

3. 为指定的引脚配置输入输出方向（对方向寄存器`DirModeReg`使能）。`0`输入，`1`输出

```c
void XGpioPs_SetDirectionPin(XGpioPs *InstancePtr, u32 Pin, u32 Direction);
```

4. 设置指定引脚输出使能（对`OpEnableReg`寄存器配置）。OpEnableReg与DirModeReg属于逻辑与的关系，只有两个都配置成功才能实现MIO的输入或者输出的配置。

```c
void XGpioPs_SetOutputEnablePin(XGpioPs *InstancePtr, u32 Pin, u32 OpEnable);
```

5. 从指定引脚读出数据。返回值是从DATA_RO寄存器内取出读到的数据

```c
u32 XGpioPs_ReadPin(XGpioPs *InstancePtr, u32 Pin);
```

5. 对指定引脚写入数据。对DATA寄存器、DATA_MSW寄存器、DATA_LSW寄存器进行配置，共同完成输出数据的写入。

```c
void XGpioPs_WritePin(XGpioPs *InstancePtr, u32 Pin, u32 Data);
```

例如，利用MIO使PS端LED灯闪烁：

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

> **[Customize Block]→[MIO Configuation]**选择**EMIO**

扩展MIO，依然属于ZYNQ的PS部分，只是连接到了PL上，再从PL的引脚连到芯片外面实现数据输入输出。 **无论是EMIO还是MIO都属于PS上的IO，直接由PS操作，PS操作MIO和EMIO的方法类似**。

MIO分布在BANK0，BANK1，而EMIO则分布在BANK2、BANK3，参考**[*Figure 14‐1*: GPIO Block Diagram]**

1. MIO在ZYNQ上的管脚是固定的，而EMIO是通过PL部分扩展的，所以使用EMIO时候需要在约束文件中分配管脚，并且设计EMIO的程序时，需要生成PL部分的bit文件烧写到FPGA中；

2. MIO共占54bit，**占用IO号为0-53**，配置方法见[Block Design/Peripheral I/O Pins](#MIO)部分；EMIO占64bit，**占用IO号为54-117**；

3. **<u>MIO和EMIO都是直接挂在PS上的GPIO</u>**，由PS操作。调用头文件`xgpiops.h`即可；**<u>AXI_GPIO是通过AXI总线挂在PS上的GPIO上</u>**，使用AXI_GPIO时，需要调用头文件`xgpio.h`；

### AXI GPIO

AXIGPIO由FPGA的PL逻辑核功能实现，相当于GPIO的IP核，通过AXI总线挂在PS上的GPIO上，耗费PL端的逻辑资源。

## IP Catalog

### Clock Wizard

锁相环(PLL)，用于产生$50Hz$的分频或倍频。

- `locked`信号：观察输入时钟是否锁定，如果输入时钟信号锁定，就会输出一个locked高电平信号。

`locked`信号是在输入信号稳定之后再输出一个`locked`信号，刚开始locked信号是低电平，等到时钟信号稳定之后他就会拉高。

### Block Memory Generator

用于缓存数据。

- `Memory Type`常用的是`Simple Dual Port RAM`，两个端口（读写+只读）输入和输出信号独立；

- `Primitives Output Register`：（在输出数据加上寄存器，可以有效改善时序，但读出的数据会落后地址两个周期）多数情况下不使能它，保持数据落后地址一个周期；
- `Width`表示数据位宽，`Depth`表示存储数据的个数；

### FIFO Generator

- FIFO是为了缓存数据，因此两边的时钟通常不同，所以选择`Independent Clocks Block RAM`；

- `FWFT`读模式：`rd_en`信号有效时，有效数据`D0`已经在数据线上准备好有效了，不会再延后一个周期（与`Standard FIFO`的区别）

### DDS Compiler

#### Configuration

**Configuration Options**

- **Phase Generator and SIN/COS LUT**：一个相位生成器和用于生成正余弦波形的查找表
- **Phase Generator Only**：只包含相位生成器（自定义波形生成）
- **SIN/COS LUT Only**：仅包含正余弦查找表（用于固定频率）

**System Requirements**

- **System Clock(MHz)**：系统时钟频率$f_{clk}$
- **Number of Channels**：通道数。单个IP核实现多个独立的频率和相位输出
- **Mode of Operation**
    - **Standard**：见[设计原理](#principle)
    - **Rasterized**：生成固定的频率点（输出频率不是连续可变的，而是在一些固定的频率值跳变），间隔由相位累加器的模数$M$决定

> $$
> \Delta f = \dfrac{f_{clk}}{M}
> $$

- **Parameter Selection**：

**System Parameters**：

将IP核集成到更广泛系统中所需的参数，如IP核的时钟配置、接口类型、以及可能涉及到的系统级优化和功能使能

- **Spurious Free Dynamic Range(SFDR)**： 无杂散动态范围。信号最大幅度与最大杂散或噪声幅度之间的差异。SFDR越高信号的纯净度越高
- **Frequency Resolution**：频率分辨率，决定了输出频率的精度
- **Noise Shaping**：在频率域内重新分配量化噪声，将其分配到不敏感的频率区域。

**Hardware Parameters**：

涉及IP核内部硬件实现的细节直接影响IP核的性能和资源消耗

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

- **Output Selection**：正弦、余弦或两者同时输出（`sin`在高位，`cos`在低位）
- **Polarity**：将相位增加$\pi$

**Implementation Options**

- **Has Phase Out**：输出相位信息
- **Memory Type**：查找表存储方式
- **Optimization Goal**：优化速度或面积；**DSP48 Use**：DSP48资源使用程度

### Fast Fourier Transform

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube/ic/fft_configuration.webp" alt="fft_configuration" width="90%" height="90%" />

**Configuration**

- **Number of Channels**：多通道，实现多帧数据同时进行FFT运算
- **Transform Length**：FFT长度
- **Architecture Configuration**
    - **Target Clock Frequency(MHz)**
    - **Target Data Throughput(MSPS)**

- **Architecture Choice**
    - **Automatically Select**：自动选择
    - **Piplined, Streaming I/O**：并行流水线结构（处理时间最短，资源消耗最大）
    - **Radix-4, Burst I/O**：基4
    - **Radix-2, Burst I/O**：基2
    - **Radix-2 Lite, Burst I/O**：

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube/ic/fft_implementation.webp" alt="fft_implementation" width="90%" height="90%" />

**Implementation**

- **Data Format**：**Fixed Point**（定位全精度）；**Floating Point**（定位缩减位宽）

- **Scaling Options**：

    - **Block Floating Point**：FFT内部采用浮点，并根据每一级的数据自动缩放（输入输出位宽一致）
    - **Scaled**：`m_axis_data_tuser`中有$5bit$表示每一级的缩放情况
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

### ILA(Integrated Logic Analyzer)

> 常用的**在线调试**方法：
>
> - ILA IP Catalog
> - VIO IP Catalog
> - `(* MARK_DEBUG="true" *)`，**[SYNTHESIS]**综合后打开**[Open Synthesized Design]**，右上角选择**[Debug]**模式，被标记的信号会有🕷️标识。

**General Options**

- `Native`：常规普通接口模式
- `AXI`：AXI 接口模式，用于调试 AXI 接口信号
- `Number of Probes`：探针数量
- `Sample Data Depth`：采样数据深度，数值越大，采样的数据越多，看到的波形数据越多（最终占用的资源越多）

**Probe_Ports**

- `Probe Width`：根据代码中的定义设置位宽
- `Probe Trigger or Data`：设置为`DATA AND TRIGGER`

例如：

```verilog
ila your_instance_name (
	.clk(clk), // input wire clk

	.probe0(probe0), // input wire [31:0]  probe0  
	.probe1(probe1) // input wire [3:0]  probe1
);
```

**调试界面**

bit文件烧写后，会进入下图所示的ILA调试界面

![](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/prog/ila_debug.webp)

**Waveform窗口**

通过**[+/-]**选择需要观察的波形（波形能否显示与**Probe_Ports**的`Probe Trigger or Data`参数设置有关）

**Status窗口**

四个按钮分别为：**循环采样**、**条件触发采样**、**无条件执行`ILA`采样**、**停止采样按钮**
**Core status**表示`ILA`的运行状态

## 参考资料

1. Zynq 7000 SoC Technical Reference Manual UG585 (v1.14) June 30, 2023
2. [ZYNQ 的三种GPIO ：MIO、EMIO、AXI](https://www.elecfans.com/emb/fpga/20200922481156.html)

2. [zynq中各种GPIO方式的区别：MIO，EMIO，AXI_GPIO 核](https://blog.csdn.net/ningjinghai11/article/details/80440683)

