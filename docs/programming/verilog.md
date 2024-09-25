# Verilog

- PS端计时器

```verilog
XTime tEnd, tCur;
u32 tUsed;
XTime_GetTime(&tCur);
/******  Start ******/
// Coding...
/******   End  ******/
XTime_GetTime(&tEnd);
tUsed = ((tEnd - tCur) * 1000000) / (COUNTS_PER_SECOND);
xil_printf("(Time elapsed is %d us)\r\n", tUsed);
```

---

- [信号边沿检测](https://ax7020-20231-v101.readthedocs.io/zh-cn/latest/7020_S1_RSTdocument_CN/14_RS232%E5%AE%9E%E9%AA%8C_CN.html)

```verilog
`timescale 1ns / 1ps


module edge_detection(
    input clk,
    input rst_n,
    input edge_pin,

    output edge_neg
);

reg  edge_d0;
reg  edge_d1;

reg edge_neg_pin0;
reg edge_neg_01;

// wire edge_neg_pin0;
// wire edge_neg_01;
// assign edge_neg_pin0 = edge_pin && ~edge_d0;
// assign edge_neg_01 = edge_d1 && ~edge_d0;

always@(posedge clk) begin
    if(rst_n == 1'b0) begin
        edge_d0 <= 1'b1;
        edge_d1 <= 1'b1;
    end else begin
        edge_d0 <= edge_pin;
        edge_d1 <= edge_d0;
    end
end

always @(posedge clk ) begin
    if(rst_n == 1'b0) begin
        edge_neg_pin0 <= 1'b0;
        edge_neg_01 <= 1'b0;
    end else begin
        if (edge_pin == 1'b0 && edge_d0 == 1'b1)
            edge_neg_pin0 <= 1'b1;
        else
            edge_neg_pin0 <= 1'b0;
        if (edge_d0 == 1'b0 && edge_d1 == 1'b1)
            edge_neg_01 <= 1'b1;
        else
            edge_neg_01 <= 1'b0;
    end
end

// assign edge_neg  = edge_neg_01;

endmodule
```

---

- 保存至`.txt`文件

```verilog
// .tb
integer f;
initial begin
    f = $fopen("f.txt", "w");
end

always@(posedge clk) begin
    f = $fopen("f.txt", "a+");
    $fwrite(f, "%d\n", $signed(  ));
    $fclose(f);
end
```

---

- [Adder100i](https://hdlbits.01xz.net/wiki/Adder100i)

```verilog
module top_module(
    input [99:0] a,
    input [99:0] b,
    input cin,

    output [99:0] cout,
    output [99:0] sum
);
    adder u_adder0(
        .a(a[0]),
        .b(b[0]),
        .cin(cin),
        .sum(sum[0]),
        .cout(cout[0])
    );
    generate
        genvar i;
        for (i = 1; i < 100; i = i + 1) begin: full_adder
            adder u_adder(
                .a(a[i]),
                .b(b[i]),
                .cin(cout[i-1]),
                .sum(sum[i]),
                .cout(cout[i])
            );
        end
    endgenerate

endmodule

module adder (
    input a,
    input b,
    input cin,

    output cout,
    output sum
);
    assign sum = a ^ b ^ cin;
    assign cout = (a & b) | (a & cin) | (b & cin);

endmodule //adder

```

---

- m序列

```verilog
`timescale 1ns / 1ps


module test(
    input   clk   ,
    input   rst_n ,
    output  m
);

    reg  [3:0] ser = 4'b1000;
    reg ser_4 = 1'b0;
    always@(posedge clk)
    begin
        if(!rst_n) begin
            ser <= 4'b1000;
            ser_4 <= 1'b0;
        end
        else begin
            ser_4 <= (ser[3] ^ ser[1]);
            ser <= {ser_4, ser[3:1]};
        end
    end
    assign m = ser[0];

endmodule
```

---

- **四种休眠模式**

在数字信号控制器（DSC）或微控制器中，常见的休眠模式包括`IDLE`、`SLEEP`、`DOZE`和`STOP`。这些模式具有不同的功耗级别和功能状态。下面是它们之间的区别：

- IDLE模式：IDLE模式是最低功耗的休眠模式之一。在IDLE模式下，处理器核心停止执行指令，但其他外设和时钟继续运行。处理器核心可以立即恢复到正常工作状态，并从IDLE指令的下一条指令继续执行。

- SLEEP模式：SLEEP模式比IDLE模式功耗稍高。在SLEEP模式下，处理器核心停止执行指令，并关闭大部分外设和时钟，以降低功耗。使用唤醒源（如定时器中断或外部触发）可以将处理器核心从SLEEP模式中唤醒。

- DOZE模式：DOZE模式提供了更低的功耗水平。在DOZE模式下，处理器核心和系统时钟会进一步降低频率以减少功耗，同时一些外设可能保持活动状态。通过唤醒源触发，处理器核心可以从DOZE模式中迅速唤醒。

- STOP模式：STOP模式提供了最低功耗的休眠模式。在STOP模式下，处理器核心和大部分外设都被关闭，时钟也停止运行。只有特定的唤醒源能够使处理器核心从STOP模式中恢复。

IDLE模式是最低功耗的简单休眠模式，只停止处理器核心的指令执行；SLEEP模式进一步降低功耗，关闭大部分外设和时钟，但可以通过唤醒源快速恢复；DOZE模式提供了更低的功耗水平，降低处理器核心和系统时钟的频率，同时保持某些外设的活动性；STOP模式是最低功耗的休眠模式，几乎关闭所有外设和时钟，只有特定的唤醒源才能唤醒处理器核心。

---

- AXI valid-ready握手协议

```verilog
// 简单理解为
s_axis_tready  // output 准备好接收数据
s_axis_tvalid  // input  写入使能
               // ↓
m_axis_tvalid  // output 可以发送数据
m_axis_tready  // input  可以发送
```

---

- 复位：异步复位、同步释放

```verilog
module async_reset_sync_release (
    input wire clk,      // 时钟信号
    input wire rst_n,    // 异步复位信号，低电平有效
    input wire data_in,  // 输入数据
    output reg data_out  // 输出数据
);

    reg rst_sync_1;      // 第一级同步寄存器
    reg rst_sync_2;      // 第二级同步寄存器

    always @(posedge clk or negedge rst_n) begin
        if (!rst_n) begin
            rst_sync_1 <= 1'b0;  // 异步复位
            rst_sync_2 <= 1'b0;  // 异步复位
        end else begin
            rst_sync_1 <= 1'b1;  // 同步释放
            rst_sync_2 <= rst_sync_1;  // 同步释放
        end
    end

    always @(posedge clk or negedge rst_sync_2) begin
        if (!rst_sync_2) begin
            data_out <= 1'b0;  // 同步复位
        end else begin
            data_out <= data_in;  // 正常数据传输
        end
    end

endmodule
```

