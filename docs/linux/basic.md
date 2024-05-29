# Linux Basic

- `tar` 指令速查

| 参数 | 含义                                      |
| :--: | :---------------------------------------- |
| `-c` | 创建归档文件                              |
| `-x` | 解包/解压归档文件                         |
| `-v` | 显示详细过程                              |
| `-f` | 指定归档文件名，通常必须放在文件名前      |
| `-z` | 使用 gzip 压缩/解压，常见后缀 `.tar.gz`   |
| `-j` | 使用 bzip2 压缩/解压，常见后缀 `.tar.bz2` |
| `-J` | 使用 xz 压缩/解压，常见后缀 `.tar.xz`     |
| `-t` | 查看归档文件内容                          |
| `-C` | 指定解压目录                              |


```shell
# 打包
tar -cvf archive.tar dir/
# 打包并 gzip 压缩
tar -czvf archive.tar.gz dir/
# 解包 tar
tar -xvf archive.tar
# 解压 tar.gz
tar -xzvf archive.tar.gz
# 解压到指定目录
tar -xzvf archive.tar.gz -C /tmp
# 查看 tar.gz 内容
tar -tvf archive.tar

# 打包并 xz 压缩
tar -cJvf archive.tar.xz dir/
# 解压 tar.xz
tar -xJvf archive.tar.xz
# 解压到指定目录
tar -xJvf archive.tar.xz -C /tmp
# 查看 tar.xz 内容
tar -tJvf archive.tar.xz
```

