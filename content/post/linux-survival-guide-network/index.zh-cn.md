+++
title = 'Linux 生存指南：网络配置与管理'
date = "2025-08-26T20:00:00+08:00"
draft = false
comments = false
ShowToc = true
categories = ['技术折腾', 'Linux']
tags = ['Linux', '网络', 'nmcli', 'Netplan']
+++

## nmcli
### 连接 Wi-Fi 命令备忘录

| 任务              | 命令                                                   |
| --------------- | ---------------------------------------------------- |
| **扫描网络**        | `nmcli device wifi list`                             |
| **连接网络**        | `sudo nmcli dev wifi connect "SSID" password "PASS"` |
| **查看活动连接**      | `nmcli con show --active`                            |
| **查看所有已存连接**    | `nmcli con show`                                     |
| **断开连接**        | `sudo nmcli dev disconnect wlan0`                    |
| **连接已存网络**      | `sudo nmcli con up "连接名"`                            |
| **删除已存网络**      | `sudo nmcli con del "连接名"`                           |
| **关闭/开启 Wi-Fi** | `nmcli radio wifi off/on`                            |

### **方法一：使用 `nmcli` 创建热点 (推荐)**
`nmcli` 是 NetworkManager 的命令行客户端，是管理网络连接的现代标准。用它来创建热点相对简单直观。
#### **步骤 1：确保 NetworkManager 正在管理的无线设备**
Ubuntu Server 24.04 默认使用 `netplan` 来配置网络，而 `netplan` 后端可以是 `systemd-networkd` 或 `NetworkManager`。要使用 `nmcli`，我们需要确保 `NetworkManager` 拥有对无线网卡的控制权。
1. 检查 netplan 配置：查看 /etc/netplan/ 目录下的 .yaml 配置文件。
```bash
#
# 查看 netplan 配置文件内容
#
cat /etc/netplan/*.yaml
```

确保 `renderer` 设置为 `NetworkManager`，或者为的无线设备 (`wifis`) 指定 `renderer: NetworkManager`。

**示例配置 `/etc/netplan/01-netcfg.yaml`**:
```yaml
network:
  version: 2
  # 将 NetworkManager 设置为默认的渲染器
  renderer: NetworkManager
```

2. 修改并应用配置：如果的配置不是 NetworkManager，请修改文件，然后运行：
```bash
#
# 应用新的 netplan 配置
#
sudo netplan apply
```

#### **步骤 2：创建热点连接**
现在，我们可以使用 `nmcli` 创建一个热点。这个过程只需要一条命令。
- `con add`：添加一个新连接。
- `type wifi`：连接类型是 Wi-Fi。
- `ifname wlan0`：指定使用哪个无线接口（请替换成的接口名）。
- `con-name MyHotspot`：给这个热点连接起个名字，方便管理。
- `autoconnect yes`：设置为开机自动启动热点（如果需要）。
- `ssid "MyServerHotspot"`：这是的热点名称（Wi-Fi 名称）。
- `wifi-sec.key-mgmt wpa-psk`：设置安全模式为 WPA2-Personal。
- `wifi-sec.psk "YourStrongPassword"`：设置的 Wi-Fi 密码（**请务必使用一个强度高、至少8位的密码！**）。


**将以上参数组合成一条命令：**

```bash
# 创建一个新的 Wi-Fi 热点连接
# 请将 wlan0, MyHotspot, MyServerHotspot 和 YourStrongPassword 替换为自己的设置
sudo nmcli con add type wifi ifname wlp33s0 con-name MyHotspot autoconnect yes ssid "hpz8g4workstation" -- wifi-sec.key-mgmt wpa-psk wifi-sec.psk "cqnu123..."
```

#### **步骤 3：启动热点**

通常，创建后热点会自动启动。如果没启动，可以用以下命令手动启动：
```bash
# 启动名为 MyHotspot 的连接
sudo nmcli con up MyHotspot
```
现在，可以用手机或其他设备搜索 Wi-Fi，应该能找到名为 "MyServerHotspot" 的网络，使用设置的密码即可连接。

#### **管理的热点**

- **查看连接状态**：
```bash
# 查看所有网络连接及其状态
nmcli con show
```

- **关闭热点**：
```bash
# 关闭（断开）热点连接
sudo nmcli con down MyHotspot
```

- **删除热点**：
```bash
# 删除热点配置
sudo nmcli con delete MyHotspot
```


## Ubuntu Server Wi-Fi
### 使用 Netplan 首次连接到 Wi-Fi
在这一步，我们将在没有 `nmcli` 且无网络的情况下，使用系统自带的 `netplan` 工具完成首次网络连接。此时，`netplan` 默认的后端是 `systemd-networkd`。
1.  **查找无线网卡接口名**
首先，我们需要知道系统的无线网卡叫什么名字。
```bash
# 列出所有网络接口
ip a

```
在输出中找到的无线网卡，其名称通常是 `wlan0` 或 `wlpXsY` 的形式。请记下这个名字。

再使用 `iw` 命令扫描可用 Wi-Fi，`iw` 是现代 Linux 系统中用于配置无线设备的标准命令行工具，是旧的 `iwconfig` 的替代品。它能够直接与无线网卡硬件通信，进行扫描。假设前面得到的无线接口名是 `wlan0`。
**执行扫描**
```bash
# 格式: sudo iw dev [接口名] scan
sudo iw dev wlan0 scan
```
这条命令的输出会**非常详细**，它会列出范围内每个接入点（AP）的全部信息，包括 BSSID (MAC地址), 频率, 信号强度, 支持的速率, 加密方式等等。

由于原始输出太长，我们通常使用 `grep` 命令来筛选出我们最关心的信息——**网络名称 (SSID)**。
```bash
# | (管道符) 会将前一个命令的输出作为后一个命令的输入
# grep SSID: 会筛选出所有包含 "SSID: " 的行
sudo iw dev wlan0 scan | grep SSID:
```
输出示例：
```bash
SSID: MyHomeWiFi 
SSID: Guest-Network 
SSID: Another-WiFi-Here 
SSID:
```
可能会看到一个空的 `SSID:`，这通常是隐藏网络。

同时查看信号强度，可以这样做：
```bash
# -i 参数让 grep 不区分大小写
# -E 参数表示使用扩展正则表达式，可以用 | (或) 来匹配多个模式
sudo iw dev wlan0 scan | grep -iE 'SSID:|signal:'
```
**信号强度解读**：这个值是 `dBm`，它是一个负数。**数字越大（越接近0），表示信号越强**。例如 `-45 dBm` 是一个非常强的信号，而 `-80 dBm` 则是一个很弱的信号。

2.  **创建 Netplan 配置文件**
我们将为 Wi-Fi 连接创建一个新的 `netplan` 配置文件。

```bash
# 使用 nano 编辑器创建一个新的配置文件
sudo nano /etc/netplan/02-wifi.yaml
```
3.  **写入 Wi-Fi 配置信息**
将以下内容复制并粘贴到编辑器中。**注意：YAML 格式对缩进极其敏感，请务必使用空格并保持层级正确。**

```yaml
# Netplan Wi-Fi 配置文件
network:
  version: 2
  wifis:
	#
	# 将 wlan0 替换为的无线网卡接口名
	#
	wlan0:
	  dhcp4: true
	  access-points:
		#
		# 替换为要连接的 Wi-Fi 名称 (SSID)
		#
		"Your_WiFi_SSID":
		  #
		  # 替换为的 Wi-Fi 密码
		  #
		  password: "Your_WiFi_Password"
```
修改完成后，保存并退出 (`Ctrl + X`, `Y`, `Enter`)。

4.  **应用配置并验证连接**
让配置生效并检查网络状态。

```bash
#
# 测试配置，如果120秒内没有确认，配置会自动回滚
#
sudo netplan try
#
# 应用 netplan 配置
#
sudo netplan apply
#
# 稍等片刻，检查是否获取了 IP 地址
#
ip a
#
# 测试外网连通性
#
ping -c 4 baidu.com
```
当在 `wlan0` 接口下看到 `inet` 开头的 IP 地址，并且 `ping` 命令成功时，恭喜，服务器已经成功连接到互联网！
### 安装 NetworkManager
现在我们有了网络连接，可以安装更强大的网络管理工具 `network-manager`，它会提供 `nmcli` 命令行程序。
1.  **更新软件包列表**
```bash
#
# 确保的软件包列表是最新
#
sudo apt update
```
2.  **安装 NetworkManager**

```bash
#
# 安装 network-manager 软件包
#
sudo apt install network-manager
```
### 将网络控制权移交给 NetworkManager
安装完成后，我们需要告诉 `netplan`，以后请让 NetworkManager 来当“网络管家”。
1.  **修改 Netplan 配置文件**
再次打开我们之前创建的 `netplan` 配置文件。
```bash
#
# 编辑配置文件
#
sudo nano /etc/netplan/02-wifi.yaml
```
2.  **简化配置，指定渲染器**
将文件中的**所有内容删除**，替换为以下更简洁的配置。

```yaml
#
# Netplan 配置文件 - 指定 NetworkManager 为全局渲染器
#
network:
  version: 2
  #
  # 这一行是核心，它命令 Netplan 使用 NetworkManager
  # 来管理所有的网络设备
  #
  renderer: NetworkManager
```
这个配置意味着 `netplan` 不再负责具体的连接细节，而是把所有工作都交给了 NetworkManager。保存并退出。

3.  **应用新配置并验证**
```bash
#
# 应用新的配置，网络服务会重启
# 注意：的 SSH 连接可能会短暂中断
#
sudo netplan apply
#
# 验证 NetworkManager 是否已成功接管
#
nmcli device status
```
如果输出中，的无线网卡（`wlan0`）和有线网卡的 `STATE` 不再是 `unmanaged`，则说明切换成功。

### 使用 nmcli 轻松管理 Wi-Fi 连接
现在，可以开始享受 `nmcli` 带来的便利了。
1.  **扫描周边的 Wi-Fi 网络**
```bash
#
# 列出所有可用的 Wi-Fi 热点
#
nmcli device wifi list
```
2.  **使用 `nmcli` 连接到网络**
之前的 `netplan` Wi-Fi 配置在切换后已失效，我们需要用 `nmcli` 重新连接一次。这次连接后，配置将被 NetworkManager 自动永久保存。
```bash
#
# 使用 nmcli 连接到的 Wi-Fi
#
sudo nmcli device wifi connect "Your_WiFi_SSID" password "Your_WiFi_Password"
```
3.  **查看当前连接状态**

```bash
#
# 查看当前活动的连接
#
nmcli connection show --active
```
4.  **断开连接**
```bash
#
# 断开无线网卡的连接
#
sudo nmcli device disconnect wlan0
```

### 系统优化 - 处理 `systemd-networkd-wait-online ` 服务失败
在切换到 NetworkManager 后，可能会在系统日志或 Cockpit 界面中看到 `systemd-networkd-wait-online` 服务启动失败。这是一个无害的副作用，因为 `systemd-networkd` 已经“失业”了。我们可以禁用这个多余的服务来清理系统并略微加快开机速度。
1.  **禁用多余的服务**
```bash
#
# 禁用该服务，使其不再开机启动
#
sudo systemctl disable systemd-networkd-wait-online.service
```
2.  **重启验证（可选）**
可以重启服务器，之后该启动失败的提示就会消失。
```bash
sudo reboot
```
