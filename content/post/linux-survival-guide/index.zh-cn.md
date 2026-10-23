+++
title = 'Linux 生存指南：终端、文件、网络与服务管理'
date = "2025-08-26T20:00:00+08:00"
draft = false
comments = false
ShowToc = true
categories = ['技术折腾', 'Linux']
tags = ['Linux', '命令行', 'Vim', 'Nano', 'Tmux', '文件系统', 'rsync', '网络', 'nmcli', 'Netplan', 'Crontab', 'systemd', '后台服务']
+++


## 终端工具：Vim、Nano 与 Tmux

### Vim
#### 功能
- **命令行文本编辑器**：Vim 是一个强大的命令行文本编辑器，适用于多种操作系统。
- **自动识别编程语言**：根据文件扩展名自动识别编程语言，支持代码高亮、自动缩进等功能。
- **使用方式**：
- 命令：`vim filename`
- 如果文件存在，则打开该文件；如果文件不存在，则创建一个新文件并命名为 `filename`。
#### 模式
Vim 包含三种主要模式：
1. **一般命令模式**（默认模式）：
- 用于执行复制、粘贴、删除等操作。
- 通过键盘输入特定命令实现不同功能。
2. **编辑模式**：
- 按 `i` 从一般命令模式进入编辑模式，可输入文本。
- 按 `ESC` 退出编辑模式，返回一般命令模式。
3. **命令行模式**：
- 在一般命令模式下按 `:`, `/`, 或 `?` 进入。
- 显示在屏幕底部，用于查找、替换、保存、退出或配置编辑器。
#### 操作
以下为常用操作命令，均在 **一般命令模式** 下执行，除非特别说明：
##### 光标移动
- `h` 或 **左箭头**：光标左移一个字符。
- `j` 或 **下箭头**：光标下移一行。
- `k` 或 **上箭头**：光标上移一行。
- `l` 或 **右箭头**：光标右移一个字符。
- `n<Space>`：光标右移 n 个字符（n 为数字）。
- `0` 或 **Home 键**：光标移至本行开头。
- `$` 或 **End 键**：光标移至本行末尾。
- `G`：光标移至文件最后一行。
- `:n` 或 `nG`：光标移至第 n 行（n 为数字）。
- `gg`：光标移至文件第一行（等同于 `1G`）。
- `n<Enter>`：光标下移 n 行（n 为数字）。
##### 查找
- `/word`：向下查找第一个匹配 `word` 的字符串。
- `?word`：向上查找第一个匹配 `word` 的字符串。
- `n`：重复上一次查找（同方向）。
- `N`：反向重复上一次查找。
- `:noh`：关闭查找关键词高亮。
##### 替换
- `:n1,n2s/word1/word2/g`：在第 n1 行到第 n2 行间，将 `word1` 替换为 `word2`。
- `:1,$s/word1/word2/g`：全文将 `word1` 替换为 `word2`。
- `:1,$s/word1/word2/gc`：全文替换 `word1` 为 `word2`，替换前逐一确认。
##### 编辑
- `i`：进入编辑模式（在光标前插入）。
- `ESC`：退出编辑模式，返回一般命令模式。
- `v`：进入可视模式，选择文本。
- `d`：删除选中文本。
- `dd`：删除当前行。
- `y`：复制选中文本。
- `yy`：复制当前行。
- `p`：粘贴复制的内容到光标后/下一行。
- `u`：撤销上一步操作。
- `Ctrl + r`：恢复撤销的操作。
- `>`：选中文本右缩进一次。
- `<`：选中文本左缩进一次。
- `gg=G`：格式化全文代码（自动调整缩进）。
##### 保存与退出
- `:w`：保存文件。
- `:w!`：强制保存文件。
- `:q`：退出 Vim。
- `:q!`：强制退出，不保存更改。
- `:wq`：保存并退出。
##### 配置
- `:set paste`：进入粘贴模式，禁用自动缩进，便于粘贴代码。
- `:set nopaste`：取消粘贴模式，恢复自动缩进。
- `:set nu`：显示行号。
- `:set nonu`：隐藏行号。
##### 其他
- `Ctrl + q`：当 Vim 卡死时，取消当前执行的命令。

#### 异常处理
- **.swp 文件问题**：
- 每次编辑文件时，Vim 会生成一个 `.filename.swp` 临时文件。
- 若打开文件时提示 `.swp` 文件已存在，说明文件可能正在被其他 Vim 实例编辑。
- **解决方法**：
1. 找到正在编辑该文件的 Vim 进程并关闭。
2. 直接删除 `.filename.swp` 文件（确保无其他进程编辑该文件）。
- **注意事项**：删除 `.swp` 文件前，确认没有其他 Vim 实例正在编辑，避免丢失未保存的更改。

#### 注意事项
- **区分大小写**：Vim 命令对大小写敏感，如 `G` 和 `g` 功能不同。
- **多键组合**：部分命令需连续输入（如 `dd`, `yy`），确保快速准确输入。
- **保存习惯**：编辑完成后，养成使用 `:w` 保存的习惯，避免意外退出丢失更改。
- **学习曲线**：Vim 操作需练习，建议初学者使用 `vimtutor`（命令行输入 `vimtutor`）学习基础操作。
- **配置文件**：可通过编辑 `~/.vimrc` 自定义 Vim 设置，如默认显示行号、设置主题等。

### Nano
#### 模式
Nano 的操作较为直观，没有 Vim 那样的复杂模式切换，主要在单一模式下操作：
- **默认编辑模式**：启动 Nano 后直接进入编辑模式，可立即输入或编辑文本。
- **命令模式**：通过快捷键（如 `Ctrl` 组合键）执行保存、退出、查找等操作，快捷键提示显示在屏幕底部。

#### 操作
Nano 的操作主要通过 `Ctrl` 或 `Alt` 组合键完成，常用命令如下（`^` 表示 `Ctrl` 键，`M-` 表示 `Alt` 键）：
##### 光标移动
- `^F` 或 **右箭头**：光标右移一个字符。
- `^B` 或 **左箭头**：光标左移一个字符。
- `^N` 或 **下箭头**：光标下移一行。
- `^P` 或 **上箭头**：光标上移一行。
- `^A`：光标移至本行开头。
- `^E`：光标移至本行末尾。
- `M-G`：跳转到指定行（输入行号后回车）。
- `^Y`：向上翻页。
- `^V`：向下翻页。

##### 查找
- `^W`：查找字符串（输入字符串后回车）。
- `M-W`：重复上一次查找（向下）。
- `^W` 后按 `^R`：反向查找（向上）。
- `^T`（部分版本）：在查找时启用正则表达式。
##### 替换
- `^W` 后按 `M-R`：进入替换模式，输入要替换的字符串和新字符串。
- 替换时可选择逐一确认或全部替换。
##### 编辑
- 直接输入：Nano 默认处于编辑模式，可直接输入文本。
- `^D`：删除光标所在位置的字符。
- `^K`：剪切当前行（删除整行）。
- `^U`：粘贴剪切的行到光标位置。
- `M-U`：撤销上一步操作。
- `M-E`：恢复撤销的操作。
- `^J`：格式化当前段落（调整文本对齐，适用于文本文件）。
- `M-}`：增加缩进（选中文本后）。
- `M-{`：减少缩进（选中文本后）。
##### 保存与退出
- `^O`：保存文件。
- `^X`：退出 Nano（若文件有更改，会提示是否保存）。
- `M-D`：强制保存（覆盖已有文件，部分版本支持）。
##### 配置
- `M-N`：显示/隐藏行号。
- `M-P`：启用/禁用软换行（文本自动换行）。
- `M-8`：启用/禁用自动缩进。
- `^G`：显示帮助文档，列出所有快捷键。
##### 其他
- `^C`：显示当前光标位置（行、列等信息）。
- `M-/`：取消当前操作（若 Nano 卡住）。
#### 异常处理
- **.nano 文件问题**：
- Nano 编辑文件时会创建一个临时文件（通常以 `.save` 或 `.nano` 结尾）。
- 若打开文件时提示临时文件存在，可能是上次编辑未正常退出。
- **解决方法**：
1. Nano 会提示是否恢复临时文件，选择 `Y`（是）以恢复未保存的内容。
2. 若不需要恢复，可选择 `N`（否），或手动删除临时文件（通常位于 `~/.nano/` 目录）。
- **注意事项**：恢复临时文件前，确认是否需要保存上次未保存的更改，以免覆盖。
#### 注意事项

- **快捷键提示**：Nano 界面底部始终显示常用快捷键（如 `^X Exit` 表示 `Ctrl+X` 退出），方便初学者使用。
- **区分大小写**：查找和替换操作默认区分大小写，可通过 `^W` 后按 `M-C` 切换大小写敏感模式。
- **配置文件**：可通过编辑 `~/.nanorc` 自定义 Nano 设置，如默认显示行号、启用语法高亮等。
- **学习资源**：Nano 内置帮助文档（`^G`）是快速学习的好工具，建议初学者阅读。
- **局限性**：Nano 功能较 Vim 简单，适合轻量级编辑，不支持复杂脚本或多文件操作。
- **版本差异**：不同版本的 Nano 可能支持不同快捷键或功能（如正则表达式），建议检查版本（`nano --version`）。

### Tmux
#### 核心概念
Tmux 的结构主要包含三个层级：
1.  **会话 (Session)**：
    -   一个会话是 Tmux 管理的最高层级，可以看作是一个独立的工作区。
    -   每个会话包含一个或多个窗口。
2.  **窗口 (Window)**：
    -   一个窗口占据整个终端屏幕，类似于应用程序中的一个标签页。
    -   每个窗口包含一个或多个窗格。
3.  **窗格 (Pane)**：
    -   一个窗格是窗口中的一个分割区域，它是一个独立的终端 Shell。
    -   用户可以在一个窗口内创建多个窗格，实现分屏效果。

#### Tmux 外部命令 (从 Shell 执行)
**注意**：以下命令直接在的常规终端（例如 Bash, Zsh）中输入并执行，而不是在 Tmux 会话内部。

##### 创建会话 (Creating Sessions)
- `tmux new -s <session_name>`：创建一个名为 `<session_name>` 的新会话并立即进入。
- `tmux new`：创建一个以数字自动编号的新会话。
- `tmux new -d -s <session_name>`：在后台创建一个名为 `<session_name>` 的新会话，但不立即进入。这在系统启动脚本中非常有用。

##### 连接会话 (Attaching to Sessions)
- `tmux ls`：列出所有正在后台运行的 Tmux 会话及其信息。
- `tmux attach -t <session_name>`：连接（Attach）到名为 `<session_name>` 的已存在会话。
- `tmux a -t <session_name>`：`attach` 命令的简写形式，功能完全相同。
- `tmux attach`：如果当前只有一个正在运行的会话，可省略会话名，直接连接。

##### 关闭会话 (Killing Sessions)
- `tmux kill-session -t <session_name>`：关闭名为 `<session_name>` 的指定会话。
- `tmux kill-session -a`：关闭所有正在运行的会话。
- `tmux kill-server`：关闭所有会话并完全终止 Tmux 服务进程。
    -   **警告**：这是一个彻底的操作，所有会话及其内部运行的程序都将被立即终止，未保存的工作会丢失。
#### 操作
所有操作都需要先按下 **前缀键 (Prefix)**，Tmux 的默认前缀键是 `Ctrl + b`。按下前缀键后松开，再按后续的命令键。
##### 会话管理 (Session)
- `tmux new -s <session_name>`：创建一个名为 `<session_name>` 的新会话。
- `tmux attach -t <session_name>` 或 `tmux a -t <session_name>`：重新连接到名为 `<session_name>` 的会话。
- `tmux ls`：列出所有正在运行的 Tmux 会话。
- `Ctrl + b` + `s`：在 Tmux 内部以交互式列表显示所有会话，并可进行切换。
- `Ctrl + b` + `d`：分离 (Detach) 当前会话，返回到常规终端（会话在后台继续运行）。
- `Ctrl + b` + `$`：重命名当前会话。
- `tmux kill-session -t <session_name>`：在 Tmux 外部关闭指定的会话。
- `tmux kill-server`：关闭所有会话并终止 Tmux 服务。

##### 窗口管理 (Window)
- `Ctrl + b` + `c`：在当前会话中创建一个新窗口。
- `Ctrl + b` + `w`：以交互式列表显示当前会话的所有窗口，并可进行切换。
- `Ctrl + b` + `p`：切换到上一个窗口。
- `Ctrl + b` + `n`：切换到下一个窗口。
- `Ctrl + b` + `0-9`：根据窗口编号快速切换到指定窗口（例如 `Ctrl + b` + `1` 切换到 1 号窗口）。
- `Ctrl + b` + `,`：重命名当前窗口。
- `Ctrl + b` + `&`：关闭当前窗口（会进行确认）。

##### 窗格管理 (Pane)
- `Ctrl + b` + `"`：将当前窗格水平分割（上下布局）。
- `Ctrl + b` + `%`：将当前窗格垂直分割（左右布局）。
- `Ctrl + b` + `方向键` (↑, ↓, ←, →)：在不同窗格之间切换。
- `Ctrl + b` + `o`：按顺序在当前窗口的窗格间循环切换。
- `Ctrl + b` + `x`：关闭当前窗格（会进行确认）。
- `Ctrl + b` + `z`：将当前窗格最大化/恢复原状 (Zoom)。
- `Ctrl + b` + `Space`：在预设的窗格布局之间切换。
- `Ctrl + b` + `q`：短暂显示所有窗格的编号，然后按下编号可快速切换到对应窗格。
- `Ctrl + b` + `{`：将当前窗格与上一个窗格交换位置。
- `Ctrl + b` + `}`：将当前窗格与下一个窗格交换位置。
- `Ctrl + b` + `Alt + 方向键`：按住 `Ctrl + b` 不放，同时按 `Alt` 和方向键，可以调整窗格大小。

##### 其他
- `Ctrl + b` + `t`：在当前窗格显示一个大时钟。
- `Ctrl + b` + `[`：进入复制模式 (Copy Mode)。
    -   在此模式下，可以使用类似 Vim 的键位移动光标、滚动屏幕。
    -   按 `Space` 开始选择，移动光标后按 `Enter` 复制所选内容。
    -   按 `q` 退出复制模式。
- `Ctrl + b` + `]`：粘贴最近一次复制的内容。

#### Config
```bash
# ~/.tmux.conf
# tmux source-file ~/.tmux.conf 使配置生效
set -g mouse on
set -g default-terminal "xterm-256color"
set -ga terminal-overrides ",xterm-256color:Tc:clipboard"
set -g status-position top
set -g status-bg default
set -g status-fg white
set -g status-left '#[fg=brightcyan]#S'
set -g status-left-length 20

# 右下角
set -g status-right '#[fg=brightcyan][%H:%M]'

# 状态栏颜色
set -g status-style fg=brightgreen

# 消息颜色
set -g message-style fg=brightgreen

# 默认窗口标题颜色
set -w -g window-status-format '#[dim]#I:#W'

# 当前窗口标题颜色
set -w -g window-status-current-format '#[fg=brightcyan,bold]#I:#W'

# pane border
set -g display-panes-active-colour brightgreen
set -g display-panes-colour brightyellow
```
#### 异常处理
- **无法连接会话**：
    -   **问题**：使用 `tmux attach` 时提示 "no sessions" 或找不到指定的会话。
    -   **解决方法**：
        1.  先使用 `tmux ls` 确认会话是否真的存在以及其确切名称。
        2.  如果会话意外终止，需要重新使用 `tmux new -s <session_name>` 创建。
- **前缀键冲突**：
    -   **问题**：`Ctrl + b` 可能与其他应用程序（如某些 IDE 的快捷键）冲突。
    -   **解决方法**：
        1.  可以通过编辑 `~/.tmux.conf` 配置文件来修改前缀键。例如，将 `set -g prefix C-a` 添加到文件中，可以将前缀键改为 `Ctrl + a`。
        2.  修改后需要重新加载配置 (`tmux source-file ~/.tmux.conf`) 或重启 Tmux 服务。

#### 注意事项
- **前缀键操作**：几乎所有 Tmux 命令都需要先按下前缀键 `Ctrl + b`。这是一个组合操作，即先按下 `Ctrl + b` 并**松开**，然后再按下一个命令键（例如 `c`, `d`, `%` 等）。
- **鼠标模式**：Tmux 支持鼠标操作（如选择窗口、调整窗格大小），但默认通常是关闭的。可以在 `~/.tmux.conf` 中添加 `set -g mouse on` 来开启它，这对于新手非常友好。
- **配置文件**：通过创建和编辑 `~/.tmux.conf` 文件，可以高度自定义 Tmux 的行为和外观，例如更改快捷键、设置状态栏样式、颜色主题等。
- **嵌套会话**：应避免在 Tmux 会话中再次启动一个新的 Tmux 会- 话。这会导致前缀键命令混乱，因为内部和外部的 Tmux 实例都会尝试捕获它。如果需要操作外部会话，可以先分离内部会话。

---

## 文件传输与进度监控

### `mv`, `cp` 进度显示

#### `rsync`
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

#### `pv` (通用管道查看器，非常灵活)
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

#### `progress` (监控已在运行的命令)
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

---

## 网络配置与管理

### nmcli
#### 连接 Wi-Fi 命令备忘录

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

#### **方法一：使用 `nmcli` 创建热点 (推荐)**
`nmcli` 是 NetworkManager 的命令行客户端，是管理网络连接的现代标准。用它来创建热点相对简单直观。
##### **步骤 1：确保 NetworkManager 正在管理的无线设备**
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

##### **步骤 2：创建热点连接**
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

##### **步骤 3：启动热点**

通常，创建后热点会自动启动。如果没启动，可以用以下命令手动启动：
```bash
# 启动名为 MyHotspot 的连接
sudo nmcli con up MyHotspot
```
现在，可以用手机或其他设备搜索 Wi-Fi，应该能找到名为 "MyServerHotspot" 的网络，使用设置的密码即可连接。

##### **管理的热点**

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


### Ubuntu Server Wi-Fi
#### 使用 Netplan 首次连接到 Wi-Fi
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
#### 安装 NetworkManager
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
#### 将网络控制权移交给 NetworkManager
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

#### 使用 nmcli 轻松管理 Wi-Fi 连接
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

#### 系统优化 - 处理 `systemd-networkd-wait-online ` 服务失败
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

---

## 进程与定时任务

### Crontab 常用速查指南（OpenWRT / Linux）

#### 1) 基本概念
- **cron**：定时任务守护进程
- **crontab**：用户级任务表（每个用户一份）
- **任务格式**：`分 时 日 月 周  命令`

#### 2) 常用命令
- 查看当前用户任务：`crontab -l`
- 编辑当前用户任务：`crontab -e`
- 删除当前用户任务：`crontab -r`
- 查看 cron 服务状态（OpenWRT）：`/etc/init.d/cron status`
- 启动 cron（OpenWRT）：`/etc/init.d/cron start`
- 开机自启（OpenWRT）：`/etc/init.d/cron enable`
- 重启 cron（OpenWRT）：`/etc/init.d/cron restart`

#### 3) 时间字段速记（分时日月周）
- 分：0-59
- 时：0-23
- 日：1-31
- 月：1-12
- 周：0-7（0/7 都表示周日；1=周一 … 6=周六）

#### 4) 通配与步进写法
- `*`：任意值
- `,`：列举多个值（如 `1,15`）
- `-`：范围（如 `1-5`）
- `/`：步进（如 `*/5` 表示每 5 分钟）

示例：
- 每 5 分钟：`*/5 * * * * cmd`
- 每天 3:00：`0 3 * * * cmd`
- 每周一到周五 9:30：`30 9 * * 1-5 cmd`
- 每月 1 号 0:10：`10 0 1 * * cmd`

#### 5) 常用时间模板（直接套用）
- 每分钟：`* * * * * cmd`
- 每 10 分钟：`*/10 * * * * cmd`
- 每小时整点：`0 * * * * cmd`
- 每天 02:00：`0 2 * * * cmd`
- 每天 23:59：`59 23 * * * cmd`
- 每周日 04:00：`0 4 * * 0 cmd`
- 每工作日 08:00：`0 8 * * 1-5 cmd`
- 每月 1 号 06:30：`30 6 1 * * cmd`
- 每年 1 月 1 日 00:00：`0 0 1 1 * cmd`

#### 6) 输出与日志（强烈推荐）
- 丢弃输出（不产生日志）：`cmd >/dev/null 2>&1`
- 追加输出到日志：`cmd >> /root/task.log 2>&1`
- 分开记录标准输出/错误：
  - `cmd >> /root/out.log 2>> /root/err.log`

#### 7) 环境与坑位（务必注意）
- cron 环境变量很少：尽量使用**绝对路径**（如 `/usr/bin/curl`）
- 脚本要可执行：`chmod +x /root/myscript.sh`
- 脚本首行解释器：`#!/bin/sh`
- PATH 可在 crontab 顶部自定义：
  - `PATH=/usr/sbin:/usr/bin:/sbin:/bin`
- 需要等待网络就绪：脚本内加检测与重试（如 ping 网关/公网 DNS）

#### 8) 示例：OpenWRT 每天定时跑登录脚本
- 编辑：`crontab -e`
- 添加（每天 07:05 执行，记录日志）：
  - `5 7 * * * /root/netlogin.sh >> /root/netlogin.log 2>&1`

#### 9) 排错清单（任务不执行时）
- 是否保存了 crontab：`crontab -l`
- cron 服务是否在跑：`/etc/init.d/cron status`
- 命令/脚本是否用绝对路径
- 权限是否可执行：`ls -l /root/netlogin.sh`
- 手动执行脚本是否正常：`/root/netlogin.sh`
- 日志是否有输出：`tail -n 50 /root/netlogin.log`


### systemd
#### `systemd ` 的基本概念
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

#### 常用的 systemd 命令
所有与 systemd 相关的命令都以 `systemctl` 开头，这是你最常用到的工具。
##### 管理服务
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
##### 开机自启
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

##### 列出所有单元
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

#### 创建自定义服务
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

#### 其他有用的命令
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


