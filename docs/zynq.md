# ZYNQ

> ```
> Project family: Zynq-7000
> Project part: xc7z020clg400-2
> ```

## Block Design

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

#### 调试界面

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