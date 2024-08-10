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

- [检测异步信号的起始位](https://ax7020-20231-v101.readthedocs.io/zh-cn/latest/7020_S1_RSTdocument_CN/14_RS232%E5%AE%9E%E9%AA%8C_CN.html)

```verilog
wire rx_negedge;
reg  rx_d0;  // rx_pin本周期值
reg  rx_d1;  // rx_pin上一周期值
assign rx_negedge = rx_d1 && ~rx_d0;  // 用于判断起始位

always@(posedge clk or negedge rst_n) begin
	if(rst_n == 1'b0) begin
		rx_d0 <= 1'b0;
		rx_d1 <= 1'b0;
	end else begin
		rx_d0 <= rx_pin;
        rx_d1 <= rx_d0;  // 延迟一个时钟周期赋值(rx_pin上一周期值)
	end
end
```

- `negedge rx_pin`：`rx_negedge`无法复原为`1'b0`；

**边沿的检测不应该受到时钟边沿变化的影响：**上面代码中`rx_d0`和`rx_d1`的赋值逻辑确保了在时钟周期的任何时刻，`rx_d1`和`rx_d0`都持有稳定的值，因此它们的异或结果`rx_negedge`也是稳定的，不会因为时钟边沿的变化而改变。

- `rx_pin && ~rx_d0`：`rx_pin`变化时刻未知：时钟上升沿变化，两者时钟相同，永远检测不到下降沿；其它时刻变化，暂时没在仿真中发现问题；
- `rx_pin && ~rx_d1`：得到`rx_d1`前提是有`rx_d0`延迟一个时钟周期，而且`rx_d0`比`rx_pin`更稳定；

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
