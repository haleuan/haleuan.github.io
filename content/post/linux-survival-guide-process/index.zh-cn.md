+++
title = 'Linux 生存指南：进程与定时任务'
date = 2026-06-03T20:00:00+08:00
draft = false
comments = false
ShowToc = true
categories = ['技术折腾', 'Linux']
tags = ['Linux', 'Crontab', 'systemd', '后台服务']
+++

## Crontab 常用速查指南（OpenWRT / Linux）

### 1) 基本概念
- **cron**：定时任务守护进程
- **crontab**：用户级任务表（每个用户一份）
- **任务格式**：`分 时 日 月 周  命令`

### 2) 常用命令
- 查看当前用户任务：`crontab -l`
- 编辑当前用户任务：`crontab -e`
- 删除当前用户任务：`crontab -r`
- 查看 cron 服务状态（OpenWRT）：`/etc/init.d/cron status`
- 启动 cron（OpenWRT）：`/etc/init.d/cron start`
- 开机自启（OpenWRT）：`/etc/init.d/cron enable`
- 重启 cron（OpenWRT）：`/etc/init.d/cron restart`

### 3) 时间字段速记（分时日月周）
- 分：0-59
- 时：0-23
- 日：1-31
- 月：1-12
- 周：0-7（0/7 都表示周日；1=周一 … 6=周六）

### 4) 通配与步进写法
- `*`：任意值
- `,`：列举多个值（如 `1,15`）
- `-`：范围（如 `1-5`）
- `/`：步进（如 `*/5` 表示每 5 分钟）

示例：
- 每 5 分钟：`*/5 * * * * cmd`
- 每天 3:00：`0 3 * * * cmd`
- 每周一到周五 9:30：`30 9 * * 1-5 cmd`
- 每月 1 号 0:10：`10 0 1 * * cmd`

### 5) 常用时间模板（直接套用）
- 每分钟：`* * * * * cmd`
- 每 10 分钟：`*/10 * * * * cmd`
- 每小时整点：`0 * * * * cmd`
- 每天 02:00：`0 2 * * * cmd`
- 每天 23:59：`59 23 * * * cmd`
- 每周日 04:00：`0 4 * * 0 cmd`
- 每工作日 08:00：`0 8 * * 1-5 cmd`
- 每月 1 号 06:30：`30 6 1 * * cmd`
- 每年 1 月 1 日 00:00：`0 0 1 1 * cmd`

### 6) 输出与日志（强烈推荐）
- 丢弃输出（不产生日志）：`cmd >/dev/null 2>&1`
- 追加输出到日志：`cmd >> /root/task.log 2>&1`
- 分开记录标准输出/错误：
  - `cmd >> /root/out.log 2>> /root/err.log`

### 7) 环境与坑位（务必注意）
- cron 环境变量很少：尽量使用**绝对路径**（如 `/usr/bin/curl`）
- 脚本要可执行：`chmod +x /root/myscript.sh`
- 脚本首行解释器：`#!/bin/sh`
- PATH 可在 crontab 顶部自定义：
  - `PATH=/usr/sbin:/usr/bin:/sbin:/bin`
- 需要等待网络就绪：脚本内加检测与重试（如 ping 网关/公网 DNS）

### 8) 示例：OpenWRT 每天定时跑登录脚本
- 编辑：`crontab -e`
- 添加（每天 07:05 执行，记录日志）：
  - `5 7 * * * /root/netlogin.sh >> /root/netlogin.log 2>&1`

### 9) 排错清单（任务不执行时）
- 是否保存了 crontab：`crontab -l`
- cron 服务是否在跑：`/etc/init.d/cron status`
- 命令/脚本是否用绝对路径
- 权限是否可执行：`ls -l /root/netlogin.sh`
- 手动执行脚本是否正常：`/root/netlogin.sh`
- 日志是否有输出：`tail -n 50 /root/netlogin.log`


## systemd
### `systemd ` 的基本概念
要理解 systemd 的用法，你需要先了解几个核心概念：
- **单元（Units）：** systemd 将所有需要管理的对象都抽象为单元。每个单元都有一个配置文件，以 `.unit_type` 结尾，比如 `.service`、`.socket`、`.device` 等。
- **目标（Targets）：** 目标是一种特殊的单元，用于将其他单元分组。例如，`multi-user.target` 表示一个多用户的、非图形化的运行级别，而 `graphical.target` 则表示一个带有图形界面的运行级别。这类似于 SysVinit 中的运行级别（runlevels）。

下面是一些常见的单元类型：
- `service`：服务，比如一个 Web 服务器 (Nginx) 或数据库 (MySQL)。
- `socket`：套接字，用于进程间的通信。
- `device`：设备，比如硬盘或网卡。
- `mount`：挂载点，比如 `/home` 目录。
- `target`：目标，用于将多个单元分组。
- `timer`：定时器，类似于 `cron`，用于定时执行任务。

### 常用的 systemd 命令
所有与 systemd 相关的命令都以 `systemctl` 开头，这是你最常用到的工具。
#### 管理服务
这是 systemd 最常见的用法。你可以使用 `systemctl` 命令来启动、停止、重启、查看状态等。
- **启动服务：**
```bash
sudo systemctl start [服务名称].service
# 例如：sudo systemctl start nginx.service
```
通常情况下，`.service` 后缀可以省略。

- **停止服务：**
```bash
sudo systemctl stop [服务名称]
# 例如：sudo systemctl stop nginx
```

- **重启服务：**
```bash
sudo systemctl restart [服务名称]
# 例如：sudo systemctl restart nginx
```

- **查看服务状态：**
```bash
systemctl status [服务名称]
# 例如：systemctl status nginx
```
这个命令会显示服务的运行状态、进程 ID (PID)、内存占用、最近的日志等详细信息。
#### 开机自启
如果你希望某个服务在系统启动时自动运行，需要启用它。
- **启用服务（开机自启）：**
```bash
sudo systemctl enable [服务名称]
# 例如：sudo systemctl enable nginx
```
这个命令会在 `/etc/systemd/system/` 目录下创建一个指向服务配置文件的软链接。
- **禁用服务（取消开机自启）：**

```bash
sudo systemctl disable [服务名称]
# 例如：sudo systemctl disable nginx
```
- **检查服务是否已启用：**
```bash
systemctl is-enabled [服务名称]
```

#### 列出所有单元
你可以使用 `list-units` 命令来查看当前系统中所有单元的状态。
- **列出所有正在运行的单元：**
```bash
systemctl list-units
```

- **列出所有已安装的单元（包括未运行的）：**
```bash
systemctl list-units --all
```

- **按类型筛选单元：**
```bash
systemctl list-units --type=service
# 列出所有服务单元
systemctl list-units --type=target
# 列出所有目标单元
```

### 创建自定义服务
当你需要为自己的应用程序创建一个 systemd 服务时，可以编写一个 `.service` 配置文件。这些文件通常存放在 `/etc/systemd/system/` 目录下。
以下是一个简单的示例，假设你的应用程序是一个名为 `my-app.py` 的 Python 脚本。
1. **创建服务文件**

```bash
sudo nano /etc/systemd/system/my-app.service
```
2. **编辑文件内容**
```toml
[Unit]
Description=My Custom Python Application
After=network.target
[Service]
User=your_username
ExecStart=/usr/bin/python3 /path/to/your/my-app.py
WorkingDirectory=/path/to/your/
Restart=always
[Install]
WantedBy=multi-user.target
```
- `[Unit]` 部分：定义了服务的描述和依赖关系。`After=network.target` 表示这个服务会在网络服务启动之后再启动。
- `[Service]` 部分：定义了如何运行服务。
- `User`：指定运行服务的用户。
- `ExecStart`：指定启动服务的命令。
- `WorkingDirectory`：指定工作目录。
- `Restart`：指定服务异常退出时的重启策略，`always` 表示总是重启。
- `[Install]` 部分：定义了服务如何被“安装”，也就是如何与目标单元关联。`WantedBy=multi-user.target` 表示当系统进入多用户模式时，会自动启用此服务。

3. 重新加载 systemd 配置
创建或修改服务文件后，你需要让 systemd 重新加载配置。
```bash
sudo systemctl daemon-reload
```

4. **启动并启用你的新服务**
```bash
sudo systemctl start my-app.service
sudo systemctl enable my-app.service
```

### 其他有用的命令
- **查看日志：** `systemd` 将所有服务的日志都通过 `journald` 统一管理。
```bash
journalctl -u [服务名称]
# 例如：journalctl -u nginx
```
这个命令会显示特定服务的所有日志。你可以使用 `-f` 选项实时跟踪日志：`journalctl -u nginx -f`。
- **掩盖服务（Masking）：** `mask` 命令可以阻止一个服务被启动，即使有其他服务依赖它。
```bash
sudo systemctl mask [服务名称]
# 例如：sudo systemctl mask nginx
```
这会在 `/etc/systemd/system/` 下创建一个指向 `/dev/null` 的软链接。要取消掩盖，使用 `unmask`。
- **挂载点（Mounts）：** `systemd` 也可以管理文件系统的挂载点，你可以使用 `systemctl --type=mount` 来查看。
