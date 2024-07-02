# Verilog

## IP Catalog

### 锁相环(PLL)

- `locked`信号：观察输入时钟是否锁定，如果输入时钟信号锁定，就会输出一个locked高电平信号。

`locked`信号是在输入信号稳定之后再输出一个`locked`信号，可以把`locked`信号当做一个**复位信号**，刚开始locked信号是低电平，等到时钟信号稳定之后他就会拉高。

## [Adder100i](https://hdlbits.01xz.net/wiki/Adder100i)

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

## 四种休眠模式

在数字信号控制器（DSC）或微控制器中，常见的休眠模式包括`IDLE`、`SLEEP`、`DOZE`和`STOP`。这些模式具有不同的功耗级别和功能状态。下面是它们之间的区别：

- IDLE模式：IDLE模式是最低功耗的休眠模式之一。在IDLE模式下，处理器核心停止执行指令，但其他外设和时钟继续运行。处理器核心可以立即恢复到正常工作状态，并从IDLE指令的下一条指令继续执行。

- SLEEP模式：SLEEP模式比IDLE模式功耗稍高。在SLEEP模式下，处理器核心停止执行指令，并关闭大部分外设和时钟，以降低功耗。使用唤醒源（如定时器中断或外部触发）可以将处理器核心从SLEEP模式中唤醒。

- DOZE模式：DOZE模式提供了更低的功耗水平。在DOZE模式下，处理器核心和系统时钟会进一步降低频率以减少功耗，同时一些外设可能保持活动状态。通过唤醒源触发，处理器核心可以从DOZE模式中迅速唤醒。

- STOP模式：STOP模式提供了最低功耗的休眠模式。在STOP模式下，处理器核心和大部分外设都被关闭，时钟也停止运行。只有特定的唤醒源能够使处理器核心从STOP模式中恢复。

IDLE模式是最低功耗的简单休眠模式，只停止处理器核心的指令执行；SLEEP模式进一步降低功耗，关闭大部分外设和时钟，但可以通过唤醒源快速恢复；DOZE模式提供了更低的功耗水平，降低处理器核心和系统时钟的频率，同时保持某些外设的活动性；STOP模式是最低功耗的休眠模式，几乎关闭所有外设和时钟，只有特定的唤醒源才能唤醒处理器核心。
