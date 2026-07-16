# Driver

## Embedded

- 在嵌入式系统和小型网络中自动获取和配置网络连接

```shell
udhcpc -i eth0
```

- Windows 和 WSL2 共享串口

```powershell
usbipd list
usbipd bind --busid <BUSID>          # 允许设备共享
usbipd attach --wsl --busid <BUSID>  # 将设备连接到 WSL2
usbipd detach --busid <BUSID>        # 将设备从 WSL2 断开
usbipd unbind --busid <BUSID>        # 取消共享配置
```

## Vitis

- 读写 DDR

```shell
xsct% mrd -bin -file [filename.bin] [startaddr] [length]  # 读
xsct% dow -data [file_name.] [address]                    # 写
```

