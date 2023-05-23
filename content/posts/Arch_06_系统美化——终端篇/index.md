---
No: 007
title: "Archlinux 小记（六）系统美化——终端篇"
date: 2023-05-23
---
# 一、[zsh](https://www.zsh.org/) 美化
---

## 1. 简介
---

### 1.1. 什么是 zsh
> `Z shell`，即`zsh`是一个 [UNIX](https://en.wikipedia.org/wiki/Unix) [shell](https://en.wikipedia.org/wiki/Shell_%28computing%29)。

### 1.2. 常见 shell 比较
详见[ ZSH 有什么优势](https://zsh.sourceforge.io/FAQ/zshfaq01.html#l4)

值得一提，就我个人体验而言，`zsh`对比 [`fish`](https://fishshell.com/)：

`zsh`的主要优点在于：

-  `zsh`兼容`bash`。一方面，我的脚本多是 bash 脚本，另一方面，软件包提供的脚本也多是`bash`脚本，在此情况下，使用兼容`bash`语法的`zsh`会更方便修改和使用脚本。
- `zsh`具备更完善的补全。安装 [zsh-completions](https://github.com/zsh-users/zsh-completions) 后，我目前没有遇到不支持的补全，这是令`fish`暂时望其项背的。


`fish`的主要优点在于：

- 开箱即用的配置。`zsh`+[oh-my-zsh](https://ohmyz.sh/) 因拖慢启动速度饱受诟病，而`fish`拥有开箱即用的友善默认配置。但是，`oh-my-zsh`之外，还有其他选择，比如使用[zim](https://zimfw.sh/)插件，兼顾速度和功能。
- 暂时没想到。

总之，对于有条件深度定制`shell`的用户而言，`zsh`暂时有着相当优势；而对`shell`，特别是对于`bash`既了解不多，也依赖不深的用户，不妨大胆尝试一下`fish`。

## 1. 安装并启用 [zim](https://zimfw.sh/)
---
> zim 是一个 zsh 配置框架，既速度惊人，还支持诸多扩展模块。

### 1.1. 安装`zsh`，并设置`zsh`为默认`shell`
运行以下命令以更改默认`shell`：
```bash
chsh -l 
chsh -s /usr/bin/zsh 
```

### 1.2. 安装`zim`
从`wget`和`curl`中选择一种安装方式，将对应命令输入终端并运行：
```bash
curl -fsSL https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh<br> 
wget -nv -O - https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh 
```

### 1.3. 配置主题
1.3-1 编辑`$ZDOTDIR/.zimrc`：
```bash
nvim $ZDOTDIR/.zimrc
```
1.3-2 在文件末尾加入下面一行内容，以添加[powerlevel10k](https://github.com/romkatv/powerlevel10k)模块，保存并退出
```bash
zmodule romkatv/powerlevel10k
```
1.3-3 在终端输入以下命令以安装`powerlevel10k`模块
```bash
zimfw install 
#之后的模块也由此方法安装
```
安装之后，`powerlevel10k`会要求用户进行配置，请确保安装了 [Nerd Font](https://www.nerdfonts.com/) 字体，若要再次配置，在终端输入`p10k configure`即可。

### 1.4. 配置模块

1.4-1 安装`zsh`模块包：
```bash
sudo pacman -S zsh-autosuggestions zsh-syntax-highlighting zsh-completions
```
> 说明<br>
> [`zsh-autosuggestions`](https://github.com/zsh-users/zsh-autosuggestions) —— 命令补全<br>
> [`zsh-syntax-highlighting`](https://github.com/zsh-users/zsh-syntax-highlighting) —— 语法高亮<br>
> [`zsh-completions`](https://github.com/zsh-users/zsh-completions) —— 定义补全

1.4-2 安装 [zoxide](https://github.com/ajeetdsouza/zoxide) 插件：
```bash
sudo pacman -S zoxide
```

1.4-3 在`$ZDOTDIR/.zshrc`中写入以下内容，使以上模块和插件生效：
```bash
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
eval "$(zoxide init zsh)"
```

## 3. 优化 .zshrc
---
随着配置不断深入，.zshrc 文件会愈发复杂冗长，为了提高维护效率，可以把不同功能的代码至于不同的文件内，再以 .zshrc 为纽带，通过 source 链接起来，。

### 3.1. 分化
创建`$XDG_CONFIG_HOME/shell`，并根据以下分类分别创建配置文件：

3.1-1 环境变量类
将设定环境变量的代码部分移植到`$XDG_CONFIG_HOME/shell/profile`内，形如：
```bash
#!/usr/bin/zsh

#LANG & INPUT
#-------------------
export GTK_IM_MODULE=fcitx5
export QT_IM_MODULE=fcitx5
export XMODIFIERS="@im=fcitx5"
export SDL_IM_MODULE=fcitx5
export GLFW_IM_MODULE=ibus
# export LANG="zh_CN.UTF-8"
# export LC_CTYPE="zh_CN.UTF-8"
#
#-------------------
#DEFAULT_PROGS
#-------------------
export EDITOR="nvim"
export TERMINAL="st"
export TERMINAL_PROG="st"
export BROWSER="chromium"

#-------------------
#XDG_PATH
#-------------------
export XDG_CONFIG_HOME="$HOME/.config"
export XDG_CACHE_HOME="$HOME/.cache"
export XDG_DATA_HOME="$HOME/.local/share"
export XDG_STATE_HOME="$HOME/.local/state"
export XDG_RUNTIME_DIR="$HOME/.local/tmp"
export PATH="/usr/bin:/usr/local/bin:$HOME/.local/bin"

#-------------------
#CONFIG
#-------------------
export _JAVA_OPTIONS=-Djava.util.prefs.userRoot="$XDG_CONFIG_HOME"/java

#--------------------
#DATA
#--------------------
export CARGO_HOME="$XDG_DATA_HOME/cargo"

#---------------------
#CACHE
#---------------------
export CUDA_CACHE_PATH="$XDG_CACHE_HOME"/nv

#---------------------
#STATE
#---------------------
export W3M_DIR="$XDG_STATE_HOME/w3m"
...
```

3.1-2 扩展功能类
将增加扩展功能的代码部分移植到`$XDG_CONFIG_HOME/shell/functionsrc`内，形如：
```bash
#!/usr/bin/zsh
# fzf
export FZF_DEFAULT_COMMAND="fd --hidden --exclude .git --type f"
export FZF_DEFAULT_OPTS='--color=fg:#f8f8f2,bg:#282a36,hl:#bd93f9 
                         --color=fg+:#f8f8f2,bg+:#44475a,hl+:#bd93f9 
                         --color=info:#ffb86c,prompt:#50fa7b,pointer:#ff79c6 
                         --color=marker:#ff79c6,spinner:#ffb86c,header:#6272a4 
                         --height 90% 
                         --layout=reverse 
                         --bind=alt-j:down,alt-k:up,alt-i:toggle+down 
                         --border 
                         --preview "bat --color=always --style=numbers --line-range=:30 {}" 
                         --preview-window=down'
export FZF_COMPLETION_OPTS='\'
...
```

3.1-3 快捷键位类
将配置快捷按键的代码部分移植到`$XDG_CONFIG_HOME/shell/aliasrc`内，形如：
```bash
#!/usr/bin/sh
alias \
  zrc="nvim ~/.config/zsh/.zshrc" \
  arc="nvim ~/.config/shell/aliasrc" \
  prc="nvim ~/.config/shell/profile" \
  frc="nvim ~/.config/shell/functionsrc" \
  lrc="nvim ~/.config/lf/lfrc" \

alias \
  cp="cp -iv" \
  mv="mv -iv" \
  rm="rm -vI" \
  mkd="mkdir -pv" \
```

### 3.2. 联合

在`.zshrc`的**合适位置**链接上以上三个文件：
```bash
...
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
eval "$(zoxide init zsh)"

[ -f "$HOME/.config/shell/profile" ] && source "$HOME/.config/shell/profile"
[ -f "$HOME/.config/shell/aliasrc" ] && source "$HOME/.config/shell/aliasrc"
[ -f "$HOME/.config/shell/functionsrc" ] && source "$HOME/.config/shell/functionsrc"
...
```
关于“合适位置”，可以参考`.zshrc`里的注释，比如，任何涉及使终端输出字符的代码需要置顶（screnfetch类的banner装饰代码），其次应为`powerlevel10k`的配置代码等等。

# 三、多彩终端小工具
---
可以通过`alias`来实现许多终端工具彩色输出，编辑`$XDG_CONFIG_HOME/shell/aliasrc`：
```bash
...
alias \
  ip="ip -color=auto" \
  e="exa --long --header --color=auto --icons -a" \
  ls="exa --long --header --color=auto --icons -a" \
  grep="grep --color=auto" \
  diff="diff --color=auto" \
  bat="bat --color=auto" \
  make="colormake" \
  ping="gping" \ 
...
```

##### 参考链接
1. [zsh](https://www.zsh.org/)
2. [github](https://github.com/)
3. [Nerd Font](https://www.nerdfonts.com/)
