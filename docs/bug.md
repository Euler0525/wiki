# Bug

## Vitis

- Ubuntu 安装 Vitis 的过程可能缺少的库

```shell
sudo apt update
sudo apt upgrade

sudo apt install libtinfo5
sudo apt install libncurses5
sudo apt install ncurses-compat-libs
sudo apt install libncurses5-dev libncursesw5-dev
sudo apt install libc6-i386 libstdc++6 lib32stdc++6 libc6-dev
```

## Vivado

PS 端烧写程序完成后无报错，但是 Vivado 显示 `(DONE Status = 0)`，有可能是电源供电不足，限流过低。

---

```tcl
[Synth 8-6895] The reference checkpoint 
.srcs/utils_1/imports/synth_1/system_wrapper.dcp is not suitable for use with incremental synthesis for this design. Please regenerate the checkpoint for this design with -incremental_synth switch in the same Vivado session that synth_design has been run. Synthesis will continue with the default flow
```

Tools→Settings→Synthesis→Incremental synthesis→Disable（轻易不要使用）

---

```tcl
[filemgmt 56-176] Module references are not supported in manual compile order mode and will be ignored.
```

这个问题在综合 MMCM 时出现，右键 [Hierachy Update] → [Automatic Update and Compile Order]

---

```tcl
[filemgmt 20-1731] Too many checkpoints files associated with the sub-design ''
```

右键 IP 核，[Reset Output Product]，[Generate Output Product]
