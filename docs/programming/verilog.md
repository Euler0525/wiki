# Verilog

- 通用模板

```verilog
`timescale 1ns/1ps
`default_nettype none
// ============================================================================
// Project  : <your_project>
// File     : example.v
// Author   : <you>
// Created  : 2025-09-12
// Description :
//   Canonical, synthesis-friendly Verilog-2001 skeleton with:
//     - Paired `default_nettype none` / `wire` (no implicit nets)
//     - Clean port grouping/comment style
//     - External async active-low reset -> synced, active-high internal reset
//     - Parameterization patterns (widths, counts)
//     - Utilities: reset_sync, sync_2ff, pulse_sync
//
// Reset Convention
//   rst_n (in) : asynchronous, active-low (from board/top)
//   rst   (int): synchronous, active-high (generated locally per clock domain)
//
// Notes
//   - Separate sequential (non-blocking <=) and combinational (blocking =) logic.
//   - Use this file as a starter for each new block; keep the tail `default_nettype wire`.
// ============================================================================

module example #(
  // --------------------------------------------------------------------------
  // Parameters (public API)
  // --------------------------------------------------------------------------
  parameter integer DATA_W       = 32,        // data width
  parameter integer CNT_MAX      = 1000000,   // example terminal count (>=1)
  parameter integer REG_SLICE_EN = 1          // 1: add 1-deep output reg slice
)(
  // --------------------------------------------------------------------------
  // Clock & Reset (domain: clk)
  // --------------------------------------------------------------------------
  input  wire                    clk,       // primary clock
  input  wire                    rst_n,     // async, active-low external reset

  // --------------------------------------------------------------------------
  // Configuration / Control (clk domain)
  // --------------------------------------------------------------------------
  input  wire                    cfg_enable,          // enables datapath
  input  wire [DATA_W-1:0]       cfg_init_value,      // example per-word tweak

  // --------------------------------------------------------------------------
  // Stream In (clk domain, ready/valid)
  // --------------------------------------------------------------------------
  input  wire [DATA_W-1:0]       s_tdata,
  input  wire                    s_tvalid,
  output wire                    s_tready,

  // --------------------------------------------------------------------------
  // Stream Out (clk domain, ready/valid)
  // --------------------------------------------------------------------------
  output wire [DATA_W-1:0]       m_tdata,
  output wire                    m_tvalid,
  input  wire                    m_tready,

  // --------------------------------------------------------------------------
  // Status (clk domain)
  // --------------------------------------------------------------------------
  output wire                    status_active, // high when running
  output wire                    status_pulse,  // 1-cycle pulse on CNT wrap
  output wire [31:0]             status_count   // exposes internal count (LSBs)
);

  // ==========================================================================
  // 0) Reset Synchronization (external async low -> internal sync high)
  // ==========================================================================
  wire rst; // synchronous, active-high reset
  reset_sync u_rst_sync (
    .clk   (clk),
    .rst_n (rst_n),
    .rst   (rst)
  );

  // ==========================================================================
  // 1) Constant function + localparams / helper constants
  // ==========================================================================
  // Verilog constant-log2 (synthesizable in mainstream tools)
  function integer CLOG2;
    input integer value;
    integer i;
    begin
      value = value - 1;
      for (i = 0; value > 0; i = i + 1)
        value = value >> 1;
      CLOG2 = (i == 0) ? 1 : i;
    end
  endfunction

  localparam integer CW = (CNT_MAX <= 1) ? 1 : CLOG2(CNT_MAX);

  // ==========================================================================
  // 2) Registers / Wires
  // ==========================================================================
  reg  [CW-1:0]      cnt_q,  cnt_d;
  reg  [DATA_W-1:0]  data_q, data_d;
  reg                vld_q,  vld_d;
  reg                wrap_pulse_q;

  wire [DATA_W-1:0]  s_tdata_tweaked;
  assign s_tdata_tweaked = s_tdata ^ cfg_init_value;

  // Status
  assign status_active = cfg_enable;
  assign status_pulse  = wrap_pulse_q;
  // If cnt wider than 32, this truncates to LSB 32 bits; if narrower, zero-extends.
  assign status_count  = cnt_q;

  // ==========================================================================
  // 3) Ready generation and datapath policy
  //    - If REG_SLICE_EN==1: 1-deep elastic buffer behavior
  //    - Else: transparent path (backpressure passes upstream)
  // ==========================================================================
  wire out_holds_data;
  assign out_holds_data = vld_q & ~m_tready;

  assign s_tready = (REG_SLICE_EN != 0) ? ~out_holds_data : m_tready;

  // Combinational next-state
  always @* begin
    // defaults
    cnt_d   = cnt_q;
    data_d  = data_q;
    vld_d   = vld_q;

    // Count on successful input handshake
    if (cfg_enable && s_tvalid && s_tready) begin
      if (cnt_q == (CNT_MAX-1)) cnt_d = {CW{1'b0}};
      else                      cnt_d = cnt_q + {{(CW-1){1'b0}}, 1'b1};
    end

    // Datapath move
    if (REG_SLICE_EN != 0) begin
      // 1-deep elastic buffer
      if (s_tvalid && s_tready) begin
        data_d = s_tdata_tweaked; // example per-word tweak
        vld_d  = 1'b1;
      end
      if (m_tready && vld_q) begin
        if (!(s_tvalid && s_tready)) vld_d = 1'b0; // became empty
      end
    end
    // else: transparent; outputs driven combinationally below
  end

  // Sequential (sync reset)
  always @(posedge clk) begin
    if (rst) begin
      cnt_q        <= {CW{1'b0}};
      data_q       <= {DATA_W{1'b0}};
      vld_q        <= 1'b0;
      wrap_pulse_q <= 1'b0;
    end else begin
      cnt_q        <= cnt_d;
      data_q       <= data_d;
      vld_q        <= vld_d;
      // 1-cycle pulse on wrap
      wrap_pulse_q <= (cfg_enable && s_tvalid && s_tready && (cnt_q == (CNT_MAX-1)));
    end
  end

  // Outputs (conditional on register slice option)
  assign m_tdata  = (REG_SLICE_EN != 0) ? data_q         : s_tdata_tweaked;
  assign m_tvalid = (REG_SLICE_EN != 0) ? vld_q          : s_tvalid;

  // ==========================================================================
  // 4) Lightweight simulation-time checks (ignored by synthesis)
  // ==========================================================================
`ifndef SYNTHESIS
  initial begin
    if (CNT_MAX < 1) begin
      $display("[%0t] ERROR: CNT_MAX must be >= 1", $time);
      $finish;
    end
    if (DATA_W < 1) begin
      $display("[%0t] ERROR: DATA_W must be >= 1", $time);
      $finish;
    end
  end
`endif

endmodule

// ============================================================================
// reset_sync : External async-low to internal sync-high reset
//   - Asynchronous assertion (via negedge rst_n), synchronous deassertion.
//   - Use 'rst' only synchronously inside always @(posedge clk).
// ============================================================================
module reset_sync (
  input  wire clk,
  input  wire rst_n,  // async, active-low
  output wire rst     // sync, active-high
);
  reg ff1, ff2;
  // Asynchronous assertion, synchronous deassertion
  always @(posedge clk or negedge rst_n) begin
    if (!rst_n) begin
      ff1 <= 1'b1;
      ff2 <= 1'b1;
    end else begin
      ff1 <= 1'b0;
      ff2 <= ff1;
    end
  end
  assign rst = ff2;
endmodule

// ============================================================================
// sync_2ff : Standard 2-flop synchronizer for single-bit level signals
//   - Use only for CDC of stable level signals (not pulses).
// ============================================================================
module sync_2ff (
  input  wire clk_dst,
  input  wire rst_dst,     // sync, active-high
  input  wire in_async,    // from another domain
  output wire out_sync
);
  reg s1, s2;
  always @(posedge clk_dst) begin
    if (rst_dst) begin
      s1 <= 1'b0;
      s2 <= 1'b0;
    end else begin
      s1 <= in_async;
      s2 <= s1;
    end
  end
  assign out_sync = s2;
endmodule

// ============================================================================
// pulse_sync : One-shot pulse transfer across clock domains
//   - src pulses may be sparse; converts to toggle, syncs, and edge-detects.
//   - Assumes pulse_src is 1-cycle wide in src domain.
// ============================================================================
module pulse_sync (
  // source domain
  input  wire clk_src,
  input  wire rst_src,     // sync high
  input  wire pulse_src,   // 1-cycle pulse in src

  // destination domain
  input  wire clk_dst,
  input  wire rst_dst,     // sync high
  output wire pulse_dst    // 1-cycle pulse in dst
);
  // Toggle in src
  reg tog_src_q;
  always @(posedge clk_src) begin
    if (rst_src)        tog_src_q <= 1'b0;
    else if (pulse_src) tog_src_q <= ~tog_src_q;
  end

  // Sync toggle into dst
  reg tog_s1, tog_s2;
  always @(posedge clk_dst) begin
    if (rst_dst) begin
      tog_s1 <= 1'b0;
      tog_s2 <= 1'b0;
    end else begin
      tog_s1 <= tog_src_q;
      tog_s2 <= tog_s1;
    end
  end

  // Edge detect in dst: XOR of two synced stages gives a 1-cycle pulse
  assign pulse_dst = tog_s2 ^ tog_s1;
endmodule

`default_nettype wire
```

- `.coe` 文件头

```verilog
MEMORY_INITIALIZATION_RADIX = 10;
MEMORY_INITIALIZATION_VECTOR =
```

- 绘制时序图：[WaveDrom](https://wavedrom.com/)

- 三段式状态机

```verilog
// 1. state 去哪里（时序逻辑）
always @(posedge clk) begin
    if(!rst_n) begin
        state <= IDLE;        // 复位后状态机处于空闲态
    end
    else begin
        state <= next_state;  // 更新状态
    end
end

// 2. next_state 怎么去（组合逻辑）
always @(*) begin
      if("进入状态的条件") begin
           next_state = "想要进入的状态";
      end
      else if("进入状态的条件") begin
          next_state = "想要进入的状态";
      end
      // ...
end

// 3. 去做什么（时序逻辑）
always @(posedge clk) begin
    if(!rst_n) begin
        "复位状态机中使到的变量(寄存器)";
    end else begin
        case(state)
            "状态1": begin
                "要干的事";
                // ...
            end
            "状态2": begin
                "要干的事";
                // ...
            end
            // ...
            default: begin
                "默认情况下要干的事";
            end
        endcase
    end
end
```

---

- PS 端计时器

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

- 保存至 `.txt` 文件

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

- m 序列

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

在数字信号控制器（DSC）或微控制器中，常见的休眠模式包括 `IDLE`、`SLEEP`、`DOZE` 和 `STOP`。这些模式具有不同的功耗级别和功能状态。下面是它们之间的区别：

- IDLE 模式：IDLE 模式是最低功耗的休眠模式之一。在 IDLE 模式下，处理器核心停止执行指令，但其他外设和时钟继续运行。处理器核心可以立即恢复到正常工作状态，并从 IDLE 指令的下一条指令继续执行。

- SLEEP 模式：SLEEP 模式比 IDLE 模式功耗稍高。在 SLEEP 模式下，处理器核心停止执行指令，并关闭大部分外设和时钟，以降低功耗。使用唤醒源（如定时器中断或外部触发）可以将处理器核心从 SLEEP 模式中唤醒。

- DOZE 模式：DOZE 模式提供了更低的功耗水平。在 DOZE 模式下，处理器核心和系统时钟会进一步降低频率以减少功耗，同时一些外设可能保持活动状态。通过唤醒源触发，处理器核心可以从 DOZE 模式中迅速唤醒。

- STOP 模式：STOP 模式提供了最低功耗的休眠模式。在 STOP 模式下，处理器核心和大部分外设都被关闭，时钟也停止运行。只有特定的唤醒源能够使处理器核心从 STOP 模式中恢复。

IDLE 模式是最低功耗的简单休眠模式，只停止处理器核心的指令执行；SLEEP 模式进一步降低功耗，关闭大部分外设和时钟，但可以通过唤醒源快速恢复；DOZE 模式提供了更低的功耗水平，降低处理器核心和系统时钟的频率，同时保持某些外设的活动性；STOP 模式是最低功耗的休眠模式，几乎关闭所有外设和时钟，只有特定的唤醒源才能唤醒处理器核心。

---

- AXI valid-ready 握手协议

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

---

- PRBS(Pseudo-Random Binary Sequence)

使用 PRBS 这种伪随机码进行高速串行通道的测试，主要测试误码率等情况。PRBS 的码流在很大程度上具有“随机数据”的特性，“0”和“1”随机出现，这种码流的频谱特征和白噪声非常接近，所谓“白噪声”就是在一个比较宽的频域里功率密度谱均匀分布，也就是所有的频率都具有相同的能量，因此该码型能够模拟各种不同频率数据组成的情况，使测试更符合真实的情况。

> 常用的有阶数 7, 9, 11, 15, 20, 23, 31

```verilog
`timescale 1ns/1ps


//////////////////////////////////////////////////////////////////////////////////
// Company : 
// Engineer: Euler0525(Modified)
// Author  : Daniele Riccardi
//
// Create Date: 2024/09/12 23:32:00
// Design Name:
// Module Name: prbs_gen_or_check
// Project Name:
// Target Devices:
// Tool Versions: Vivado 2019.1
// Description:
//--------------------------------------------------------------------------
// DESCRIPTION
//--------------------------------------------------------------------------
//  This module generates or check a PRBS pattern. The following table shows how
//  to set the PARAMETERS for compliance to ITU-T Recommendation O.150 Section 5.
//
//  When the CHK_MODE is "false", it uses a  LFSR strucure to generate the
//  PRBS pattern.
//  When the CHK_MODE is "true", the incoming data are loaded into prbs registers
//  and compared with the locally generated PRBS
//
// Dependencies:
//
// Revision:
// Revision 0.01 - File Created
// Additional Comments:
//--------------------------------------------------------------------------
// NOTES
//--------------------------------------------------------------------------
//
//
//   Set paramaters to the following values for a ITU-T compliant PRBS
//------------------------------------------------------------------------------
// POLY_LENGHT POLY_TAP INV_PATTERN  || nbr of   bit seq.   max 0      feedback
//                                   || stages    length  sequence      stages
//------------------------------------------------------------------------------
//     7          6       false      ||    7         127      6 ni        6, 7   (*)
//     9          5       false      ||    9         511      8 ni        5, 9
//    11          9       false      ||   11        2047     10 ni        9,11
//    15         14       true       ||   15       32767     15 i        14,15
//    20          3       false      ||   20     1048575     19 ni        3,20
//    23         18       true       ||   23     8388607     23 i        18,23
//    29         27       true       ||   29   536870911     29 i        27,29
//    31         28       true       ||   31  2147483647     31 i        28,31
//
// i=inverted, ni= non-inverted
// (*) non standard
//----------------------------------------------------------------------------
//
// In the generated parallel PRBS, LSB is the first_n generated bit, for example
//      if the PRBS serial stream is : 000001111011... then
//      the generated PRBS with a parallelism of 3 bit becomes:
//        prbs_dout(2) = 0  1  1  1 ...
//        prbs_dout(1) = 0  0  1  1 ...
//        prbs_dout(0) = 0  0  1  0 ...
// In the received parallel PRBS, LSB is oldest bit received
//
// RESET pin is not needed for power-on reset : all registers are properly inizialized
// in the source code.
//
//////////////////////////////////////////////////////////////////////////////////


//------------------------------------------------------------------------------
// PARAMETERS
//------------------------------------------------------------------------------
// CHK_MODE    : true  => check mode
//               false => generate mode
// INV_PATTERN : true : invert prbs pattern
//               in "generate mode", the generated prbs is inverted bit-wise at outputs
//               in "check mode", the input data are inverted before processing
// NBITS       : bus size of prbs_din and prbs_dout
// POLY_LENGTH : length of the polynomial (= number of shift register stages)
// POLY_TAP    : intermediate stage that is xor-ed with the last stage to generate to next prbs bit
//
//------------------------------------------------------------------------------
// PINS
//------------------------------------------------------------------------------
//
// prbs_en     : input wire  enable/pause pattern generation/check
// prbs_din    : input wire  inject error           ( in generate mode )
//                           data to be checked     ( in check mode    )
// prbs_dout   : output reg  generated prbs pattern ( in generate mode )
//                           error found            ( in check mode    )
//

module prbs_gen_or_check #(
    parameter                 CHK_MODE    = 0                                  ,
    parameter                 INV_PATTERN = 0                                  ,
    parameter                 NBITS       = 32                                 ,
    parameter                 POLY_LENGTH = 31                                 ,
    parameter                 POLY_TAP    = 28
)(
    input  wire               clk                                              ,
    input  wire               rst_n                                            ,
    input  wire               prbs_en                                          ,
    input  wire [NBITS - 1:0] prbs_din                                         ,
    output reg  [NBITS - 1:0] prbs_dout
);
    //--------------------------------------------------------------------------
    // Internal variables
    //--------------------------------------------------------------------------

    wire [1:POLY_LENGTH] prbs       [NBITS:0]                                  ;
    wire [NBITS - 1:0]   prbs_din_i                                            ;
    wire [NBITS - 1:0]   prbs_xor_a                                            ;
    wire [NBITS - 1:0]   prbs_xor_b                                            ;
    wire [NBITS:1]       prbs_msb                                              ;
    reg  [1:POLY_LENGTH] prbs_reg   = {(POLY_LENGTH){1'b1}}                    ;

    assign prbs_din_i = INV_PATTERN == 0 ? prbs_din : ( ~prbs_din);
    assign prbs[0] = prbs_reg;

    genvar i;
    generate for (i = 0; i < NBITS; i = i + 1) begin : g1
        assign prbs_xor_a[i] = prbs[i][POLY_TAP] ^ prbs[i][POLY_LENGTH];
        assign prbs_xor_b[i] = prbs_xor_a[i] ^ prbs_din_i[i];
        assign prbs_msb[i+1] = CHK_MODE == 0 ? prbs_xor_a[i]  :  prbs_din_i[i];
        assign prbs[i+1]     = {prbs_msb[i+1] , prbs[i][1:POLY_LENGTH-1]};
    end
    endgenerate

    always @(posedge clk) begin
        if(rst_n == 1'b0) begin
            prbs_reg  <= {POLY_LENGTH{1'b1}};
            prbs_dout <= {NBITS{1'b1}};
        end else if(prbs_en == 1'b1) begin
            prbs_dout <= prbs_xor_b;
            prbs_reg  <= prbs[NBITS];
        end
    end

endmodule
```

---

- Verilator 语法检查脚本

```shell
#!/bin/bash

# Verilog/VHDL Syntax Check Script
# Usage: ./vericheck.sh <directory_path>

# Check if directory parameter is provided
if [ -z "$1" ]; then
    echo "Error: Please provide a directory path to check"
    exit 1
fi

# Check if directory exists
if [ ! -d "$1" ]; then
    echo "Error: Directory '$1' does not exist"
    exit 1
fi

# Check if verilator is installed
if ! command -v verilator &> /dev/null; then
    echo "Error: verilator not found. Please install verilator first."
    exit 1
fi

# Create result file
RESULT_FILE="verilator_check_results_$(date +%Y%m%d_%H%M%S).txt"
echo "Verilator Syntax Check Results - $(date)" > "$RESULT_FILE"
echo "=====================================" >> "$RESULT_FILE"

# Statistics
total_files=0
success_files=0
failed_files=0

# Traverse all Verilog files in the directory
echo "Starting directory check: $1"
while IFS= read -r -d '' file; do
    ((total_files++))
    echo -n "Checking file $total_files: $file ... "
    
    # Execute verilator syntax check
    result=$(verilator --lint-only -Wall "$file" 2>&1)
    exit_code=$?
    
    # Record results
    if [ $exit_code -eq 0 ]; then
        ((success_files++))
        echo "Passed"
        echo "[$total_files] Passed: $file" >> "$RESULT_FILE"
    else
        ((failed_files++))
        echo "Failed"
        echo "[$total_files] Failed: $file" >> "$RESULT_FILE"
        echo "Error details:" >> "$RESULT_FILE"
        echo "$result" >> "$RESULT_FILE"
        echo "-------------------------------------" >> "$RESULT_FILE"
    fi
done < <(find "$1" -type f \( -name "*.v" -o -name "*.sv" \) -print0)

# Output statistics
echo "====================================="
echo "Check completed!"
echo "Total files checked: $total_files"
echo "Passed: $success_files"
echo "Failed: $failed_files"
echo "Detailed results saved to: $RESULT_FILE"
echo "====================================="

# Save statistics
echo "=====================================" >> "$RESULT_FILE"
echo "Check completed!" >> "$RESULT_FILE"
echo "Total files checked: $total_files" >> "$RESULT_FILE"
echo "Passed: $success_files" >> "$RESULT_FILE"
echo "Failed: $failed_files" >> "$RESULT_FILE"
echo "Check time: $(date)" >> "$RESULT_FILE"

# Return non-zero exit code if there are failed files
if [ $failed_files -gt 0 ]; then
    exit 1
else
    exit 0
fi
```

- FIFO IP 核使用示例

```verilog
`timescale 1ns / 1ps


module fifo_test (
    input clk,
    input rst_n
);

    // Write & Read Clock Generator
    wire clk_100;
    wire clk_75;
    wire locked;

    clk_gen_pll clk_gen_pll_inst (
        .clk_in1  ( clk                                                       ),  // input clk_in1
        .reset    ( ~rst_n                                                    ),  // input reset
        .clk_out1 ( clk_100                                                   ),  // output clk_out1
        .clk_out2 ( clk_75                                                    ),  // output clk_out2
        .locked   ( locked                                                    )   // output locked
    );

    wire wr_clk;
    wire rd_clk;
    wire fifo_rst_n;

    assign wr_clk = clk_100;
    assign rd_clk = clk_75;
    assign fifo_rst_n = locked;

    wire         wr_en;
    wire         rd_en;
    wire         full;
    wire         empty;
    reg  [15:0]  wr_data;
    wire [15:0]  rd_data;
    wire [ 8:0]  rd_data_cnt;
    wire [ 8:0]  wr_data_cnt;
    wire         wr_rst_busy;
    wire         rd_rst_busy;

    fifo fifo_inst     (
        .rst           ( ~fifo_rst_n                                          ),  // input  wire          rst
        .wr_clk        ( wr_clk                                               ),  // input  wire          wr_clk
        .rd_clk        ( rd_clk                                               ),  // input  wire          rd_clk
        .wr_en         ( wr_en                                                ),  // input  wire          wr_en
        .din           ( wr_data                                              ),  // input  wire [15 : 0] din
        .rd_en         ( rd_en                                                ),  // input  wire          rd_en
        .dout          ( rd_data                                              ),  // output wire [15 : 0] dout
        .full          ( full                                                 ),  // output wire          full
        .empty         ( empty                                                ),  // output wire          empty
        .rd_data_count ( rd_data_cnt                                          ),  // output wire [ 8 : 0] rd_data_count
        .wr_data_count ( wr_data_cnt                                          ),  // output wire [ 8 : 0] wr_data_count
        .wr_rst_busy   ( wr_rst_busy                                          ),  // output wire          wr_rst_busy
        .rd_rst_busy   ( rd_rst_busy                                          )   // output wire          rd_rst_busy
    );

    /* Write State Machine ---------------------------------------------------*/
    localparam WR_IDLE       = 0;
    localparam WR_FIFO       = 1;
    reg        wr_state      = 1'b0;
    reg        wr_next_state = 1'b1;

    reg  [6:0] wr_rst_cnt;
    always @(posedge wr_clk) begin
        if (!fifo_rst_n) begin
            wr_rst_cnt <= 7'd0;
        end else begin
            if (wr_state == WR_IDLE) begin
                wr_rst_cnt <= wr_rst_cnt + 7'd1;
            end else begin
                wr_rst_cnt <= 7'd0;
            end
        end
    end

    always @(posedge wr_clk) begin
        if (!fifo_rst_n) begin
            wr_state <= WR_IDLE;
        end else begin
            wr_state <= wr_next_state;
        end
    end

    always @(*) begin
        case (wr_state)
            WR_IDLE:
                if (wr_rst_cnt == 7'd79) begin
                    wr_next_state = WR_FIFO;
                end else begin
                    wr_next_state = WR_IDLE;
                end
            WR_FIFO:
                wr_next_state = WR_FIFO;
            default:
                wr_next_state = WR_IDLE;
        endcase
    end

    assign wr_en = (wr_state == WR_FIFO) ? (~full) : 1'b0;

    always @(posedge wr_clk) begin
        if (!fifo_rst_n) begin
            wr_data <= 16'd0;
        end else begin
            if (wr_en) begin
                wr_data <= wr_data + 16'd1;
            end else begin
                wr_data <= wr_data;
            end
        end
    end

    /* Read State Machine ----------------------------------------------------*/
    localparam RD_IDLE       = 0;
    localparam RD_FIFO       = 1;
    reg        rd_state      = 1'b0;
    reg        rd_next_state = 1'b1;

    reg  [6:0] rd_rst_cnt;
    always @(posedge rd_clk) begin
        if (!fifo_rst_n) begin
            rd_rst_cnt <= 7'd0;
        end else begin
            if (rd_state == RD_IDLE) begin
                rd_rst_cnt <= rd_rst_cnt + 7'd1;
            end else begin
                rd_rst_cnt <= 7'd0;
            end
        end
    end

    always @(posedge rd_clk) begin
        if (!fifo_rst_n) begin
            rd_state <= RD_IDLE;
        end else begin
            rd_state <= rd_next_state;
        end
    end

    always @(*) begin
        case (rd_state)
            RD_IDLE:
                if (rd_rst_cnt == 7'd59) begin
                    rd_next_state = RD_FIFO;
                end else begin
                    rd_next_state = RD_IDLE;
                end
            RD_FIFO:
                rd_next_state = RD_FIFO;
            default:
                rd_next_state = RD_IDLE;
        endcase
    end

    assign rd_en = (rd_state == RD_FIFO) ? (~empty) : 1'b0;


endmodule
```

