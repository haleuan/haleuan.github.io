+++
categories = ['Workflow', 'Tech Notes']
comments = false
date = 2024-07-29
description = 'Notes on configuring my MacBook environment'
draft = true
image = 'screenshot_1.png'
readingTime = true
slug = 'macbook-silicon-configuration'
tags = ['OSX', 'ZSH', 'Homebrew']
title = 'MacBook Silicon Configuration'
+++

## Applications

### Skim
A lightweight PDF viewer, available through Homebrew.

### Keka
An archive utility.

### VLC
A media player.

## CLI

### ~~Alacritty~~
I no longer use this application. It is lightweight, but development seems insufficiently active, and the lack of tabs is a serious drawback. I do not want to go through the trouble of setting up tmux.

#### Install
With Homebrew:
```bash
brew install --cask alacritty
```

#### Install a font
With Homebrew:
```bash
brew install font-hack-nerd-font
```

#### Configuration
Create the configuration file:
```bash
mkdir -p ~/.config/alacritty
vim alacritty.toml
```

`alacritty.toml`:
```toml
import = [
	"~/.config/alacritty/themes/themes/night_owl.toml"
]

live_config_reload=true

[env]
TERM = "xterm-256color"

[window]
padding.x = 30
padding.y = 30
dimensions.columns = 135
dimensions.lines = 40
opacity=0.85


[font]
normal.family = "Hack Nerd Font"
size = 14.0
```

### powerlevel10k
Install and enable it:
```bash
brew install powerlevel10k
echo "source $(brew --prefix)/share/powerlevel10k/powerlevel10k.zsh-theme" >> ~/.zshrc
source ~/.zshrc
```

To uninstall it, see this [GitHub Gist](https://gist.github.com/breithbarbot/254e58bd87009963b3f58405d75cbe6c).

### ZSH plugins
#### zsh-autosuggestions
Install and enable it:
```
brew install zsh-autosuggestions
echo "source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ~/.zshrc
source ~/.zshrc
```

#### zsh-syntax-highlighting
Install and enable it:
```
brew install zsh-syntax-highlighting
echo "source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/.zshrc
source ~/.zshrc
```

### Vim
#### Where `.vimrc` files are located
##### System-wide and local vimrc files
When Vim starts, it searches for a system-wide vimrc file to perform default initialization for the system.

This file is usually at `$VIM/vimrc`. If it is not there, run `:version` in Vim to find its location. For example, the relevant output on my machine looks like this:
```bash
:version
... ... ... 
system vimrc file: "$VIM/vimrc" 
user vimrc file: "$HOME/.vimrc" 
2nd user vimrc file: "~/.vim/vimrc" 
user exrc file: "$HOME/.exrc" 
fall-back for $VIM: "/usr/share/vim" 
... ... ...
```

The system vimrc is indeed at `$VIM/vimrc`, but I found that the `$VIM` environment variable was not set on my machine. In this example, as the output shows, `$VIM` resolves to `/usr/share/vim`, a fallback value (translator's note: the value used if earlier options fail).

I looked for `vimrc` at that location and found the file. This is the system vimrc, which Vim reads at startup.

After reading and parsing the system vimrc, Vim looks for a user-specific (local) vimrc.

The search order for a local vimrc is: the environment variable `VIMINIT`, `$HOME/.vimrc`, the environment variable `EXINIT`, and a file named `exrc`. Usually a file such as `$HOME/.vimrc` or `~/.vimrc` exists and serves as the local vimrc.

##### Set the vimrc path
As mentioned above, `.vimrc` is the user configuration file for Vim and is usually in the home directory (`~/.vimrc`). To use a different location, set the `VIMINIT` environment variable. `VIMINIT` contains Vim commands that run when Vim starts.

For example, to have Vim use `/path/to/your/vimrc`, set `VIMINIT` like this:
```bash
export VIMINIT='source /path/to/your/vimrc'
```
You can add this line to `.bashrc` or `.bash_profile` (depending on your system and shell) so that `VIMINIT` is set whenever a new shell starts. When Vim starts, it runs the command in `VIMINIT`, namely `source /path/to/your/vimrc`, and loads the specified configuration file.

**Note that settings in `.vimrc` still affect Vim's global settings, so use this approach carefully in multi-user environments.**

### Miniconda3
Use the `Command Line Quick install` on the official website to install it under the current user. Installing with the `.pkg` file from the official site or a mirror requires administrator privileges and places the environment under `/opt/miniconda3`.

If you installed it under `/opt` and want to uninstall it, delete the related files and configuration files.

### Homebrew

#### Installation
Homebrew can only be installed under `/opt/homebrew`. See the [release note](https://brew.sh/2021/02/05/homebrew-3.0.0/) for the reason. For uninstall instructions, see this [guide](https://mac.install.guide/homebrew/5).

#### Formulae and Casks
##### Formulae
- **Definition**: Formulae are scripts that describe how to build and install software with Homebrew. They are usually written in Ruby and stored in Homebrew's GitHub repository.
- **Function**: Formulae handle downloading, compiling, linking, and installing source code. They let you install command-line tools and other software that can be built from source.
- **Examples**: Common command-line tools such as `git`, `wget`, and `curl` have corresponding formulae.

##### Casks
- **Definition**: Casks are a Homebrew extension for installing GUI applications, fonts, or software that requires additional installation steps.
- **Function**: Unlike formulae, Casks typically install applications distributed as binaries, such as installers or archives. A Cask moves the application to `/Applications` and may create symlinks or menu items.
- **Examples**: Desktop applications such as `google-chrome`, `visual-studio-code`, and `spotify` are installed through Casks.

##### Differences
1. **Installation method**:
   - Formulae usually involve compiling source code; Casks install prebuilt binaries or application packages.
2. **Use cases**:
   - Formulae are for command-line tools and other software that can be installed by compiling it.
   - Casks are for GUI applications, fonts, and software requiring a special installation process.

##### Usage
- Install a formula: `brew install <formula-name>`
- Install a Cask: `brew install --cask <cask-name>`

