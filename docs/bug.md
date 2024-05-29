# Bug

## Windows

安装系统时报错

```powershell
The computer restarted unexpectedly or encountered an unexpected error.
```

[Shift+F10] 打开 CMD，输入 `regedit` 打开注册表，修改 `HKEY_LOCAL_MACHINE\SYSTEM\Setup\Status\ChildCompletion` 中的 `setup.exe` 的 `Value` 为 `3`，然后重启即可。

---

Windows11 屏幕切换残留：

- 关闭 Multi Plane Overlay(MPO)

```powershell
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Dwm
```

新建一个 `DWORD32`，`Value name` 为 `OverlayTestMode`，`Value data` 为 `5`.

- 关闭浏览器系统中的图像加速选项。

## IDE

### Kiro

PowerShell无法启动，注释掉下面这行配置

```powershell
# C:\Users\Euler0525\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
if ($env:TERM_PROGRAM -eq "kiro") { . "$(kiro --locate-shell-integration-path pwsh)" }
```

### Vitis

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

### Vivado

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
