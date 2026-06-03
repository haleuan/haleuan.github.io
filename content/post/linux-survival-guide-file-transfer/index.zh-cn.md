+++
title = 'Linux 生存指南：文件传输与进度监控'
date = 2026-06-03T20:00:00+08:00
draft = false
comments = false
ShowToc = true
categories = ['技术折腾', 'Linux']
tags = ['Linux', '文件系统', 'rsync']
+++

## `mv`, `cp` 进度显示

### `rsync`
复制单个文件：
```bash
# -a: 归档模式，保留文件所有属性（权限、时间戳等）
# -h: human-readable，以人类可读的格式显示大小 (e.g., GB, MB)
# -P: 显示进度 (progress) 并且支持断点续传 (partial)
rsync -ahP /path/to/source/very_large_dataset.zip /path/to/destination/
```

复制整个目录：
```bash
rsync -ahP /path/to/source_directory/ /path/to/destination_directory/
```

### `pv` (通用管道查看器，非常灵活)
`pv` (Pipe Viewer) 是一个终端工具，专门用来测量数据在管道（pipe）中的流向。可以把它插入到任意两个命令之间，用来监控数据传输的进度。它是一个通用工具，不仅能监控文件复制，还能监控 `tar` 打包、`gzip` 压缩、数据库导出 `dd` 镜像等任何通过管道传输数据的过程。
安装：
```bash
sudo apt install pv
```

使用：
```bash
# 基本格式: pv [源文件] > [目标文件]
pv /path/to/source/large_model.pth > /path/to/destination/large_model.pth
```

### `progress` (监控已在运行的命令)
它可以在**不中断**命令的情况下，去监控系统中正在运行的 `cp`, `mv`, `dd`, `tar` 等命令的进度。
安装：
```bash
sudo apt install progress
```

使用：
- 在第一个终端窗口，正常执行复制命令：
```bash
cp very_large_file.iso /mnt/usb/
```
- 打开第二个终端窗口，运行 `progress` 命令：
```bash
# -m 参数表示持续监控
progress -m
```

**输出示例：** `progress` 会自动找到正在运行的 `cp` 进程，并显示它的 PID、文件名、进度、速度等信息。
```bash
[12345] cp /path/to/very_large_file.iso
        4.1 GiB (55.1%) of 7.5 GiB at 98.7 MiB/s eta 0:35
```
