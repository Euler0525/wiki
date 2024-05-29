# Bug

## Vivado

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

这个问题在综合MMCM时出现，右键[Hierachy Update]→[Automatic Update and Compile Order]

---

```tcl
[filemgmt 20-1731] Too many checkpoints files associated with the sub-design ''
```

右键IP核，[Reset Output Product]，[Generate Output Product]