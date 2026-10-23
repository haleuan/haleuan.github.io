+++
title = 'A Linux Survival Guide: Terminal, Files, Networking, and Service Management'
date = "2025-08-26T20:00:00+08:00"
draft = false
comments = false
ShowToc = true
categories = ['Tech', 'Linux']
tags = ['Linux', 'Command Line', 'Vim', 'Nano', 'Tmux', 'File System', 'rsync', 'Networking', 'nmcli', 'Netplan', 'Crontab', 'systemd', 'Background Services']
+++


## Terminal Tools: Vim, Nano, and Tmux

### Vim
#### Features
- **Command-line text editor**: Vim is a powerful command-line text editor available on many operating systems.
- **Automatic programming-language detection**: It detects the programming language from the file extension and supports syntax highlighting, automatic indentation, and more.
- **How to use it**:
- Command: `vim filename`
- If the file exists, it opens it; otherwise, it creates a new file named `filename`.
#### Modes
Vim has three main modes:
1. **Normal mode** (the default mode):
- Used for operations such as copying, pasting, and deleting.
- Different functions are performed by entering specific commands on the keyboard.
2. **Insert mode**:
- Press `i` in Normal mode to enter Insert mode and type text.
- Press `ESC` to leave Insert mode and return to Normal mode.
3. **Command-line mode**:
- Press `:`, `/`, or `?` in Normal mode to enter it.
- It appears at the bottom of the screen and is used to search, replace, save, quit, or configure the editor.
#### Operations
The following common operations are performed in **Normal mode**, unless otherwise noted:
##### Moving the cursor
- `h` or **Left Arrow**: move the cursor one character to the left.
- `j` or **Down Arrow**: move the cursor down one line.
- `k` or **Up Arrow**: move the cursor up one line.
- `l` or **Right Arrow**: move the cursor one character to the right.
- `n<Space>`: move the cursor right by n characters (where n is a number).
- `0` or **Home**: move the cursor to the beginning of the current line.
- `$` or **End**: move the cursor to the end of the current line.
- `G`: move the cursor to the last line of the file.
- `:n` or `nG`: move the cursor to line n (where n is a number).
- `gg`: move the cursor to the first line of the file (equivalent to `1G`).
- `n<Enter>`: move the cursor down n lines (where n is a number).
##### Searching
- `/word`: search downward for the first occurrence of the string `word`.
- `?word`: search upward for the first occurrence of the string `word`.
- `n`: repeat the previous search in the same direction.
- `N`: repeat the previous search in the opposite direction.
- `:noh`: turn off search-term highlighting.
##### Replacing
- `:n1,n2s/word1/word2/g`: replace `word1` with `word2` between lines n1 and n2.
- `:1,$s/word1/word2/g`: replace `word1` with `word2` throughout the file.
- `:1,$s/word1/word2/gc`: replace `word1` with `word2` throughout the file, confirming each replacement.
##### Editing
- `i`: enter Insert mode (insert before the cursor).
- `ESC`: leave Insert mode and return to Normal mode.
- `v`: enter Visual mode and select text.
- `d`: delete the selected text.
- `dd`: delete the current line.
- `y`: copy the selected text.
- `yy`: copy the current line.
- `p`: paste the copied content after the cursor/on the next line.
- `u`: undo the previous operation.
- `Ctrl + r`: redo an undone operation.
- `>`: indent the selected text one level to the right.
- `<`: indent the selected text one level to the left.
- `gg=G`: format the entire file's code (automatically adjust indentation).
##### Saving and quitting
- `:w`: save the file.
- `:w!`: force-save the file.
- `:q`: quit Vim.
- `:q!`: force-quit without saving changes.
- `:wq`: save and quit.
##### Configuration
- `:set paste`: enter paste mode and disable automatic indentation, making it easier to paste code.
- `:set nopaste`: leave paste mode and restore automatic indentation.
- `:set nu`: show line numbers.
- `:set nonu`: hide line numbers.
##### Other
- `Ctrl + q`: cancel the current command if Vim becomes unresponsive.

#### Troubleshooting
- **`.swp` file issue**:
- Vim creates a temporary `.filename.swp` file whenever you edit a file.
- If Vim reports that a `.swp` file already exists when you open a file, another Vim instance may be editing it.
- **How to resolve it**:
1. Find and close the Vim process that is editing the file.
2. Delete the `.filename.swp` file directly (make sure no other process is editing the file).
- **Note**: Before deleting a `.swp` file, make sure no other Vim instance is editing the file, to avoid losing unsaved changes.

#### Notes
- **Case sensitivity**: Vim commands are case-sensitive; for example, `G` and `g` do different things.
- **Multi-key commands**: Some commands require consecutive key presses (such as `dd` and `yy`), so enter them quickly and accurately.
- **Save regularly**: Get into the habit of using `:w` after editing to avoid losing changes if you quit unexpectedly.
- **Learning curve**: Vim takes practice. Beginners can learn the basics with `vimtutor` (run `vimtutor` in the command line).
- **Configuration file**: Edit `~/.vimrc` to customize Vim, for example, to show line numbers by default or set a theme.

### Nano
#### Modes
Nano is more straightforward to use than Vim and has no complex mode switching. Most work takes place in a single mode:
- **Default editing mode**: Nano enters editing mode when it starts, so you can type or edit text immediately.
- **Command mode**: Use keyboard shortcuts (such as `Ctrl` combinations) to save, quit, search, and perform other actions. Shortcut hints appear at the bottom of the screen.

#### Operations
Nano operations mainly use `Ctrl` or `Alt` key combinations. Common commands follow (`^` means the `Ctrl` key, and `M-` means the `Alt` key):
##### Moving the cursor
- `^F` or **Right Arrow**: move one character to the right.
- `^B` or **Left Arrow**: move one character to the left.
- `^N` or **Down Arrow**: move down one line.
- `^P` or **Up Arrow**: move up one line.
- `^A`: move to the beginning of the current line.
- `^E`: move to the end of the current line.
- `M-G`: jump to a specified line (enter the line number and press Enter).
- `^Y`: page up.
- `^V`: page down.

##### Searching
- `^W`: search for a string (enter the string and press Enter).
- `M-W`: repeat the previous search downward.
- Press `^R` after `^W` to search in reverse (upward).
- `^T` (some versions): enable regular expressions while searching.
##### Replacing
- Press `M-R` after `^W` to enter replace mode, then enter the string to replace and the new string.
- During replacement, you can confirm each match individually or replace all matches.
##### Editing
- Type directly: Nano is in editing mode by default, so you can type text directly.
- `^D`: delete the character at the cursor.
- `^K`: cut the current line (delete the entire line).
- `^U`: paste the cut line at the cursor.
- `M-U`: undo the previous operation.
- `M-E`: redo an undone operation.
- `^J`: justify the current paragraph (adjust text alignment; intended for text files).
- `M-}`: increase indentation (after selecting text).
- `M-{`: decrease indentation (after selecting text).
##### Saving and quitting
- `^O`: save the file.
- `^X`: quit Nano (if the file has changes, Nano asks whether to save them).
- `M-D`: force-save (overwrite an existing file; supported by some versions).
##### Configuration
- `M-N`: show/hide line numbers.
- `M-P`: enable/disable soft wrapping (automatic text wrapping).
- `M-8`: enable/disable automatic indentation.
- `^G`: show the help documentation, which lists all shortcuts.
##### Other
- `^C`: show the current cursor position (line, column, and other information).
- `M-/`: cancel the current operation (if Nano becomes stuck).
#### Troubleshooting
- **`.nano` file issue**:
- Nano creates a temporary file while editing (usually ending in `.save` or `.nano`).
- If Nano reports a temporary file when you open a file, the previous editing session may not have exited normally.
- **How to resolve it**:
1. Nano asks whether to recover the temporary file. Choose `Y` (yes) to restore unsaved content.
2. If you do not need to recover it, choose `N` (no), or manually delete the temporary file (usually in `~/.nano/`).
- **Note**: Before recovering a temporary file, decide whether you need to save the previous unsaved changes so that you do not overwrite them.
#### Notes

- **Shortcut hints**: Nano always displays common shortcuts at the bottom of the screen (for example, `^X Exit` means `Ctrl+X` quits), which makes it convenient for beginners.
- **Case sensitivity**: Search and replace are case-sensitive by default. Press `M-C` after `^W` to toggle case-sensitive matching.
- **Configuration file**: Edit `~/.nanorc` to customize Nano, for example, to show line numbers by default or enable syntax highlighting.
- **Learning resources**: Nano's built-in help (`^G`) is a useful way to learn quickly; beginners are encouraged to read it.
- **Limitations**: Nano has fewer features than Vim and is suitable for lightweight editing. It does not support complex scripts or multi-file operations.
- **Version differences**: Different Nano versions may support different shortcuts or features (such as regular expressions); check your version with `nano --version`.

### Tmux
#### Core concepts
Tmux has three main levels:
1. **Session**:
    - A session is the highest-level object managed by Tmux and can be thought of as an independent workspace.
    - Each session contains one or more windows.
2. **Window**:
    - A window occupies the entire terminal screen, similar to a tab in an application.
    - Each window contains one or more panes.
3. **Pane**:
    - A pane is a split area within a window and is an independent terminal shell.
    - You can create multiple panes in one window to split the screen.

#### Tmux commands from outside (run from the shell)
**Note**: Enter and run these commands in a regular terminal (such as Bash or Zsh), not inside a Tmux session.

##### Creating sessions
- `tmux new -s <session_name>`: create and immediately enter a new session named `<session_name>`.
- `tmux new`: create a new session with an automatically assigned numeric name.
- `tmux new -d -s <session_name>`: create a new session named `<session_name>` in the background without entering it. This is useful in system startup scripts.

##### Attaching to sessions
- `tmux ls`: list all Tmux sessions running in the background and their information.
- `tmux attach -t <session_name>`: attach to an existing session named `<session_name>`.
- `tmux a -t <session_name>`: shorthand for `attach`; it works the same way.
- `tmux attach`: if only one session is running, you can omit its name and attach directly.

##### Killing sessions
- `tmux kill-session -t <session_name>`: close the specified session named `<session_name>`.
- `tmux kill-session -a`: close all running sessions.
- `tmux kill-server`: close all sessions and completely terminate the Tmux server process.
    - **Warning**: This is a destructive operation: all sessions and programs running inside them are terminated immediately, and unsaved work is lost.
#### Operations
For all operations, press the **prefix key** first. Tmux's default prefix key is `Ctrl + b`. Press and release the prefix key, then press the command key that follows.
##### Session management
- `tmux new -s <session_name>`: create a new session named `<session_name>`.
- `tmux attach -t <session_name>` or `tmux a -t <session_name>`: reattach to the session named `<session_name>`.
- `tmux ls`: list all running Tmux sessions.
- `Ctrl + b` + `s`: show an interactive list of all sessions inside Tmux and switch between them.
- `Ctrl + b` + `d`: detach from the current session and return to the regular terminal (the session continues running in the background).
- `Ctrl + b` + `$`: rename the current session.
- `tmux kill-session -t <session_name>`: close the specified session from outside Tmux.
- `tmux kill-server`: close all sessions and terminate the Tmux server.

##### Window management
- `Ctrl + b` + `c`: create a new window in the current session.
- `Ctrl + b` + `w`: show an interactive list of all windows in the current session and switch between them.
- `Ctrl + b` + `p`: switch to the previous window.
- `Ctrl + b` + `n`: switch to the next window.
- `Ctrl + b` + `0-9`: switch to a window by its number (for example, `Ctrl + b` + `1` switches to window 1).
- `Ctrl + b` + `,`: rename the current window.
- `Ctrl + b` + `&`: close the current window (Tmux asks for confirmation).

##### Pane management
- `Ctrl + b` + `"`: split the current pane horizontally (top/bottom layout).
- `Ctrl + b` + `%`: split the current pane vertically (left/right layout).
- `Ctrl + b` + `方向键` (↑, ↓, ←, →): switch between panes.
- `Ctrl + b` + `o`: cycle through the panes in the current window.
- `Ctrl + b` + `x`: close the current pane (Tmux asks for confirmation).
- `Ctrl + b` + `z`: maximize/restore the current pane (Zoom).
- `Ctrl + b` + `Space`: cycle through the predefined pane layouts.
- `Ctrl + b` + `q`: briefly show the numbers of all panes; press a number to switch to that pane.
- `Ctrl + b` + `{`: swap the current pane with the previous pane.
- `Ctrl + b` + `}`: swap the current pane with the next pane.
- `Ctrl + b` + `Alt + 方向键`: hold `Ctrl + b`, then press `Alt` and an arrow key to resize panes.

##### Other
- `Ctrl + b` + `t`: show a large clock in the current pane.
- `Ctrl + b` + `[`: enter Copy mode.
    - In this mode, use Vim-like keys to move the cursor and scroll the screen.
    - Press `Space` to start selecting, move the cursor, and press `Enter` to copy the selection.
    - Press `q` to leave Copy mode.
- `Ctrl + b` + `]`: paste the most recently copied content.

#### Config
```bash
# ~/.tmux.conf
# Run tmux source-file ~/.tmux.conf to apply the configuration
set -g mouse on
set -g default-terminal "xterm-256color"
set -ga terminal-overrides ",xterm-256color:Tc:clipboard"
set -g status-position top
set -g status-bg default
set -g status-fg white
set -g status-left '#[fg=brightcyan]#S'
set -g status-left-length 20

# Bottom right
set -g status-right '#[fg=brightcyan][%H:%M]'

# Status bar color
set -g status-style fg=brightgreen

# Message color
set -g message-style fg=brightgreen

# Default window title color
set -w -g window-status-format '#[dim]#I:#W'

# Current window title color
set -w -g window-status-current-format '#[fg=brightcyan,bold]#I:#W'

# pane border
set -g display-panes-active-colour brightgreen
set -g display-panes-colour brightyellow
```
#### Troubleshooting
- **Unable to attach to a session**:
    - **Problem**: `tmux attach` reports "no sessions" or cannot find the specified session.
    - **Solution**:
        1. Run `tmux ls` first to confirm that the session exists and check its exact name.
        2. If the session terminated unexpectedly, create it again with `tmux new -s <session_name>`.
- **Prefix-key conflict**:
    - **Problem**: `Ctrl + b` may conflict with shortcuts in other applications (such as some IDEs).
    - **Solution**:
        1. Edit `~/.tmux.conf` to change the prefix key. For example, add `set -g prefix C-a` to change it to `Ctrl + a`.
        2. After editing, reload the configuration (`tmux source-file ~/.tmux.conf`) or restart the Tmux server.

#### Notes
- **Using the prefix key**: Almost all Tmux commands require the `Ctrl + b` prefix. This is a two-step action: press and **release** `Ctrl + b`, then press a command key (such as `c`, `d`, or `%`).
- **Mouse mode**: Tmux supports mouse operations (such as selecting windows and resizing panes), but they are usually disabled by default. Add `set -g mouse on` to `~/.tmux.conf` to enable this, which is beginner-friendly.
- **Configuration file**: Create and edit `~/.tmux.conf` to customize Tmux's behavior and appearance, such as changing shortcuts, styling the status bar, or setting a color theme.
- **Nested sessions**: Avoid starting another Tmux session from inside a Tmux session. This can make prefix-key commands confusing because both the inner and outer Tmux instances try to capture them. To operate on the outer session, detach from the inner session first.

---

## File Transfer and Progress Monitoring

### Showing progress for `mv` and `cp`

#### `rsync`
Copy a single file:
```bash
# -a: archive mode; preserve file attributes (permissions, timestamps, etc.)
# -h: human-readable; display sizes in a human-readable format (e.g., GB, MB)
# -P: show progress and support resuming partial transfers
rsync -ahP /path/to/source/very_large_dataset.zip /path/to/destination/
```

Copy an entire directory:
```bash
rsync -ahP /path/to/source_directory/ /path/to/destination_directory/
```

#### `pv` (Pipe Viewer, a versatile general-purpose tool)
`pv` (Pipe Viewer) is a terminal tool for measuring the flow of data through a pipe. You can insert it between any two commands to monitor transfer progress. It can monitor not only file copies, but also any data transfer through a pipe, including `tar` archives, `gzip` compression, database exports, and `dd` disk images.
Install it:
```bash
sudo apt install pv
```

Usage:
```bash
# Basic format: pv [source_file] > [destination_file]
pv /path/to/source/large_model.pth > /path/to/destination/large_model.pth
```

#### `progress` (monitoring commands that are already running)
It can monitor the progress of commands such as `cp`, `mv`, `dd`, and `tar` that are already running on the system, **without interrupting** them.
Install it:
```bash
sudo apt install progress
```

Usage:
- In the first terminal window, run the copy command as usual:
```bash
cp very_large_file.iso /mnt/usb/
```
- Open a second terminal window and run `progress`:
```bash
# The -m option means continuous monitoring
progress -m
```

**Example output:** `progress` automatically finds the running `cp` process and displays its PID, filename, progress, speed, and other information.
```bash
[12345] cp /path/to/very_large_file.iso
        4.1 GiB (55.1%) of 7.5 GiB at 98.7 MiB/s eta 0:35
```

---

## Network Configuration and Management

### nmcli
#### Wi-Fi connection command reference

| Task              | Command                                                   |
| --------------- | ---------------------------------------------------- |
| **Scan for networks**        | `nmcli device wifi list`                             |
| **Connect to a network**        | `sudo nmcli dev wifi connect "SSID" password "PASS"` |
| **Show active connections**      | `nmcli con show --active`                            |
| **Show all saved connections**    | `nmcli con show`                                     |
| **Disconnect**        | `sudo nmcli dev disconnect wlan0`                    |
| **Connect to a saved network**      | `sudo nmcli con up "连接名"`                            |
| **Delete a saved network**      | `sudo nmcli con del "连接名"`                           |
| **Turn Wi-Fi off/on** | `nmcli radio wifi off/on`                            |

#### **Method 1: Create a hotspot with `nmcli` (recommended)**
`nmcli` is the command-line client for NetworkManager and is the modern standard for managing network connections. It makes creating a hotspot relatively simple and intuitive.
##### **Step 1: Make sure NetworkManager manages the wireless device**
Ubuntu Server 24.04 uses `netplan` by default to configure networking, and the `netplan` backend can be either `systemd-networkd` or `NetworkManager`. To use `nmcli`, make sure NetworkManager controls the wireless card.
1. Check the Netplan configuration: view the `.yaml` configuration files in `/etc/netplan/`.
```bash
#
# View the contents of the Netplan configuration files
#
cat /etc/netplan/*.yaml
```

Make sure `renderer` is set to `NetworkManager`, or specify `renderer: NetworkManager` for the wireless device (`wifis`).

**Example configuration `/etc/netplan/01-netcfg.yaml`**:
```yaml
network:
  version: 2
  # Set NetworkManager as the default renderer
  renderer: NetworkManager
```

2. Modify and apply the configuration: if your configuration does not use NetworkManager, edit the file and then run:
```bash
#
# Apply the new Netplan configuration
#
sudo netplan apply
```

##### **Step 2: Create a hotspot connection**
Now you can create a hotspot with `nmcli`. It only takes one command.
- `con add`: add a new connection.
- `type wifi`: set the connection type to Wi-Fi.
- `ifname wlan0`: specify the wireless interface to use (replace this with your interface name).
- `con-name MyHotspot`: give the hotspot connection a name for easier management.
- `autoconnect yes`: configure the hotspot to start automatically at boot (if needed).
- `ssid "MyServerHotspot"`: set the hotspot name (Wi-Fi name).
- `wifi-sec.key-mgmt wpa-psk`: set the security mode to WPA2-Personal.
- `wifi-sec.psk "YourStrongPassword"`: set the Wi-Fi password (**use a strong password of at least 8 characters!**).


**Combine the parameters above into one command:**

```bash
# Create a new Wi-Fi hotspot connection
# Replace wlan0, MyHotspot, MyServerHotspot, and YourStrongPassword with your own settings
sudo nmcli con add type wifi ifname wlp33s0 con-name MyHotspot autoconnect yes ssid "hpz8g4workstation" -- wifi-sec.key-mgmt wpa-psk wifi-sec.psk "cqnu123..."
```

##### **Step 3: Start the hotspot**

The hotspot usually starts automatically after it is created. If it does not, start it manually with this command:
```bash
# Start the connection named MyHotspot
sudo nmcli con up MyHotspot
```
You can now search for Wi-Fi on your phone or another device. You should find a network named "MyServerHotspot" and be able to connect using the password you set.

##### **Manage your hotspot**

- **Check connection status**:
```bash
# Show all network connections and their status
nmcli con show
```

- **Turn off the hotspot**:
```bash
# Turn off (disconnect) the hotspot connection
sudo nmcli con down MyHotspot
```

- **Delete the hotspot**:
```bash
# Delete the hotspot configuration
sudo nmcli con delete MyHotspot
```


### Ubuntu Server Wi-Fi
#### Connect to Wi-Fi for the first time with Netplan
In this step, use the built-in `netplan` tool to establish the first network connection when `nmcli` is unavailable and there is no network connection. At this point, the default `netplan` backend is `systemd-networkd`.
1. **Find the wireless interface name**
First, find the name of the system's wireless network card.
```bash
# List all network interfaces
ip a

```
Find the wireless card in the output. Its name is usually `wlan0` or of the form `wlpXsY`. Note this name.

Next, use the `iw` command to scan for available Wi-Fi networks. `iw` is the standard command-line tool for configuring wireless devices on modern Linux systems and replaces the older `iwconfig`. It can communicate directly with the wireless hardware and perform scans. Assume the wireless interface found above is named `wlan0`.
**Run the scan**
```bash
# Format: sudo iw dev [interface_name] scan
sudo iw dev wlan0 scan
```
The output of this command is **very detailed**. It lists all information for every access point (AP) in range, including its BSSID (MAC address), frequency, signal strength, supported rates, encryption method, and more.

Because the raw output is long, we usually use the `grep` command to filter for the information we care about most: the **network name (SSID)**.
```bash
# The | (pipe) passes the previous command's output as input to the next command
# grep SSID: filters for all lines containing "SSID: "
sudo iw dev wlan0 scan | grep SSID:
```
Example output:
```bash
SSID: MyHomeWiFi 
SSID: Guest-Network 
SSID: Another-WiFi-Here 
SSID:
```
You may see an empty `SSID:`, which usually indicates a hidden network.

To view signal strength at the same time, run:
```bash
# The -i option makes grep case-insensitive
# The -E option enables extended regular expressions, allowing | (or) to match multiple patterns
sudo iw dev wlan0 scan | grep -iE 'SSID:|signal:'
```
**Interpreting signal strength**: The value is in `dBm`, and it is negative. **The larger the number (the closer it is to 0), the stronger the signal**. For example, `-45 dBm` is a very strong signal, while `-80 dBm` is very weak.

2. **Create a Netplan configuration file**
Create a new `netplan` configuration file for the Wi-Fi connection.

```bash
# Use the Nano editor to create a new configuration file
sudo nano /etc/netplan/02-wifi.yaml
```
3. **Enter the Wi-Fi configuration**
Copy and paste the following into the editor. **YAML is extremely sensitive to indentation, so use spaces and preserve the correct hierarchy.**

```yaml
# Netplan Wi-Fi configuration file
network:
  version: 2
  wifis:
	#
	# Replace wlan0 with the name of your wireless interface
	#
	wlan0:
	  dhcp4: true
	  access-points:
		#
		# Replace this with the Wi-Fi network name (SSID) you want to connect to
		#
		"Your_WiFi_SSID":
		  #
		  # Replace this with your Wi-Fi password
		  #
		  password: "Your_WiFi_Password"
```
After editing, save and exit (`Ctrl + X`, `Y`, `Enter`).

4. **Apply the configuration and verify the connection**
Make the configuration effective and check the network status.

```bash
#
# Test the configuration; if it is not confirmed within 120 seconds, it rolls back automatically
#
sudo netplan try
#
# Apply the Netplan configuration
#
sudo netplan apply
#
# Wait a moment and check whether an IP address was obtained
#
ip a
#
# Test external connectivity
#
ping -c 4 baidu.com
```
When an IP address beginning with `inet` appears under the `wlan0` interface and the `ping` command succeeds, congratulations: the server is connected to the Internet!
#### Install NetworkManager
Now that you have a network connection, install the more powerful network-management tool `network-manager`, which provides the `nmcli` command-line program.
1. **Update the package list**
```bash
#
# Make sure the package list is up to date
#
sudo apt update
```
2. **Install NetworkManager**

```bash
#
# Install the network-manager package
#
sudo apt install network-manager
```
#### Hand network control over to NetworkManager
After installation, tell `netplan` to let NetworkManager act as the network manager from now on.
1. **Edit the Netplan configuration file**
Open the `netplan` configuration file created earlier again.
```bash
#
# Edit the configuration file
#
sudo nano /etc/netplan/02-wifi.yaml
```
2. **Simplify the configuration and specify the renderer**
Delete **all contents** of the file and replace them with this simpler configuration.

```yaml
#
# Netplan configuration file - specify NetworkManager as the global renderer
#
network:
  version: 2
  #
  # This is the key line: it tells Netplan to use NetworkManager
  # to manage all network devices
  #
  renderer: NetworkManager
```
This configuration means that `netplan` no longer handles connection details itself, but delegates all work to NetworkManager. Save and exit.

3. **Apply the new configuration and verify it**
```bash
#
# Apply the new configuration; the network service will restart
# Note: your SSH connection may be briefly interrupted
#
sudo netplan apply
#
# Verify that NetworkManager has taken over successfully
#
nmcli device status
```
If the wireless card (`wlan0`) and wired card show a `STATE` other than `unmanaged`, the switch was successful.

#### Easily manage Wi-Fi connections with nmcli
You can now enjoy the convenience of `nmcli`.
1. **Scan nearby Wi-Fi networks**
```bash
#
# List all available Wi-Fi hotspots
#
nmcli device wifi list
```
2. **Connect to a network with `nmcli`**
The previous `netplan` Wi-Fi configuration no longer applies after the switch, so connect once again with `nmcli`. After this connection, NetworkManager automatically saves the configuration permanently.
```bash
#
# Connect to your Wi-Fi with nmcli
#
sudo nmcli device wifi connect "Your_WiFi_SSID" password "Your_WiFi_Password"
```
3. **View the current connection status**

```bash
#
# Show current active connections
#
nmcli connection show --active
```
4. **Disconnect**
```bash
#
# Disconnect the wireless interface
#
sudo nmcli device disconnect wlan0
```

#### System optimization - handle failure of the `systemd-networkd-wait-online ` service
After switching to NetworkManager, you may see the `systemd-networkd-wait-online` service fail to start in the system log or Cockpit interface. This is a harmless side effect because `systemd-networkd` is no longer in use. Disable this unnecessary service to clean up the system and slightly speed up boot.
1. **Disable the unnecessary service**
```bash
#
# Disable this service so it no longer starts at boot
#
sudo systemctl disable systemd-networkd-wait-online.service
```
2. **Restart to verify (optional)**
You can restart the server; the startup failure message will then disappear.
```bash
sudo reboot
```

---

## Processes and Scheduled Tasks

### Crontab Quick Reference (OpenWRT / Linux)

#### 1) Basic concepts
- **cron**: scheduled-task daemon
- **crontab**: per-user task table (one for each user)
- **Task format**: `minute hour day month week  command`

#### 2) Common commands
- Show the current user's tasks: `crontab -l`
- Edit the current user's tasks: `crontab -e`
- Delete the current user's tasks: `crontab -r`
- Show the cron service status (OpenWRT): `/etc/init.d/cron status`
- Start cron (OpenWRT): `/etc/init.d/cron start`
- Enable cron at boot (OpenWRT): `/etc/init.d/cron enable`
- Restart cron (OpenWRT): `/etc/init.d/cron restart`

#### 3) Time-field quick reference (minute, hour, day, month, week)
- Minute: 0-59
- Hour: 0-23
- Day: 1-31
- Month: 1-12
- Week: 0-7 (0/7 both mean Sunday; 1 = Monday … 6 = Saturday)

#### 4) Wildcards and step syntax
- `*`: any value
- `,`: list multiple values (such as `1,15`)
- `-`: range (such as `1-5`)
- `/`: step (such as `*/5`, meaning every 5 minutes)

Examples:
- Every 5 minutes: `*/5 * * * * cmd`
- Every day at 3:00: `0 3 * * * cmd`
- Monday through Friday at 9:30: `30 9 * * 1-5 cmd`
- At 00:10 on the first day of every month: `10 0 1 * * cmd`

#### 5) Common time templates (ready to use)
- Every minute: `* * * * * cmd`
- Every 10 minutes: `*/10 * * * * cmd`
- At the top of every hour: `0 * * * * cmd`
- Every day at 02:00: `0 2 * * * cmd`
- Every day at 23:59: `59 23 * * * cmd`
- Every Sunday at 04:00: `0 4 * * 0 cmd`
- Every workday at 08:00: `0 8 * * 1-5 cmd`
- At 06:30 on the first day of every month: `30 6 1 * * cmd`
- At 00:00 on January 1 every year: `0 0 1 1 * cmd`

#### 6) Output and logs (strongly recommended)
- Discard output (produce no log): `cmd >/dev/null 2>&1`
- Append output to a log: `cmd >> /root/task.log 2>&1`
- Record standard output and errors separately:
  - `cmd >> /root/out.log 2>> /root/err.log`

#### 7) Environment and pitfalls (pay attention)
- cron has few environment variables: use **absolute paths** whenever possible (such as `/usr/bin/curl`)
- Scripts must be executable: `chmod +x /root/myscript.sh`
- Interpreter on the first line of a script: `#!/bin/sh`
- You can define `PATH` at the top of crontab:
  - `PATH=/usr/sbin:/usr/bin:/sbin:/bin`
- If the network must be ready first, add checks and retries inside the script (such as pinging the gateway/public DNS)

#### 8) Example: run a login script on OpenWRT every day
- Edit: `crontab -e`
- Add this (run daily at 07:05 and record a log):
  - `5 7 * * * /root/netlogin.sh >> /root/netlogin.log 2>&1`

#### 9) Troubleshooting checklist (when a task does not run)
- Did you save the crontab? `crontab -l`
- Is the cron service running? `/etc/init.d/cron status`
- Do the command/script use absolute paths?
- Is it executable? `ls -l /root/netlogin.sh`
- Does it work when run manually? `/root/netlogin.sh`
- Does the log contain output? `tail -n 50 /root/netlogin.log`


### systemd
#### Basic concepts of `systemd `
To understand how to use systemd, first learn a few core concepts:
- **Units:** systemd abstracts everything it manages as a unit. Each unit has a configuration file ending in `.unit_type`, such as `.service`, `.socket`, or `.device`.
- **Targets:** A target is a special unit used to group other units. For example, `multi-user.target` represents a multi-user, non-graphical runlevel, while `graphical.target` represents a runlevel with a graphical interface. This is similar to runlevels in SysVinit.

Here are some common unit types:
- `service`: a service, such as a web server (Nginx) or database (MySQL).
- `socket`: a socket used for inter-process communication.
- `device`: a device, such as a hard disk or network card.
- `mount`: a mount point, such as the `/home` directory.
- `target`: a target used to group multiple units.
- `timer`: a timer, similar to `cron`, used to run tasks on a schedule.

#### Common systemd commands
All systemd-related commands begin with `systemctl`, the tool you will use most often.
##### Managing services
This is the most common use of systemd. Use `systemctl` to start, stop, restart, and view the status of services.
- **Start a service:**
```bash
sudo systemctl start [服务名称].service
# Example: sudo systemctl start nginx.service
```
In most cases, the `.service` suffix can be omitted.

- **Stop a service:**
```bash
sudo systemctl stop [服务名称]
# Example: sudo systemctl stop nginx
```

- **Restart a service:**
```bash
sudo systemctl restart [服务名称]
# Example: sudo systemctl restart nginx
```

- **View service status:**
```bash
systemctl status [服务名称]
# Example: systemctl status nginx
```
This command displays detailed information such as the service's running state, process ID (PID), memory usage, and recent logs.
##### Start at boot
If you want a service to run automatically when the system starts, enable it.
- **Enable a service (start at boot):**
```bash
sudo systemctl enable [服务名称]
# Example: sudo systemctl enable nginx
```
This command creates a symbolic link in `/etc/systemd/system/` pointing to the service configuration file.
- **Disable a service (cancel start at boot):**

```bash
sudo systemctl disable [服务名称]
# Example: sudo systemctl disable nginx
```
- **Check whether a service is enabled:**
```bash
systemctl is-enabled [服务名称]
```

##### List all units
Use the `list-units` command to view the status of all units on the current system.
- **List all running units:**
```bash
systemctl list-units
```

- **List all installed units (including those not running):**
```bash
systemctl list-units --all
```

- **Filter units by type:**
```bash
systemctl list-units --type=service
# List all service units
systemctl list-units --type=target
# List all target units
```

#### Creating a custom service
When you need to create a systemd service for your own application, write a `.service` configuration file. These files are usually stored in `/etc/systemd/system/`.
Here is a simple example, assuming your application is a Python script named `my-app.py`.
1. **Create the service file**

```bash
sudo nano /etc/systemd/system/my-app.service
```
2. **Edit the file contents**
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
- `[Unit]` section: defines the service description and dependencies. `After=network.target` means this service starts after the network service starts.
- `[Service]` section: defines how to run the service.
- `User`: specifies the user that runs the service.
- `ExecStart`: specifies the command used to start the service.
- `WorkingDirectory`: specifies the working directory.
- `Restart`: specifies the restart policy when the service exits unexpectedly; `always` means always restart it.
- `[Install]` section: defines how the service is "installed", that is, how it is associated with a target unit. `WantedBy=multi-user.target` means the service is automatically enabled when the system enters multi-user mode.

3. Reload the systemd configuration
After creating or modifying the service file, reload the configuration so systemd can see it.
```bash
sudo systemctl daemon-reload
```

4. **Start and enable your new service**
```bash
sudo systemctl start my-app.service
sudo systemctl enable my-app.service
```

#### Other useful commands
- **View logs:** `systemd` centrally manages the logs of all services through `journald`.
```bash
journalctl -u [服务名称]
# Example: journalctl -u nginx
```
This displays all logs for a particular service. Use the `-f` option to follow logs in real time: `journalctl -u nginx -f`.
- **Mask a service:** The `mask` command prevents a service from starting, even if another service depends on it.
```bash
sudo systemctl mask [服务名称]
# Example: sudo systemctl mask nginx
```
This creates a symbolic link to `/dev/null` under `/etc/systemd/system/`. Use `unmask` to remove the mask.
- **Mount points:** `systemd` can also manage filesystem mount points; use `systemctl --type=mount` to view them.

> **Translation note:** This English version was translated with the assistance of an LLM.
