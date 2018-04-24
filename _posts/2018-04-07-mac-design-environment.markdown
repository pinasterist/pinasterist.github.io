---
layout:     post
title:      "在macbook上打造工作环境"
subtitle:   "让自己更舒服点"
date:       2018-04-07
author:     "pinasterist"
header-img: "img/post-bg-design.jpg"
tags:
    - macos
    - tmux
    - iterm2
    - zsh
    - oh my zsh
---

macos基于freebsd，与linux有很大不同，所以得费点功夫才能把macos折腾得舒服点。  
这里我把调配工作环境的过程写下来，算是一个记录。

### 第一步：安装软件
1. 安装brew
    ```shell
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
    加速brew
    ```shell
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

2. 安装cask
    ```shell
brew tap caskroom/cask
```
    加速cask
    ```shell
cd "$(brew --repo)"/Library/Taps/caskroom/homebrew-cask
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
```

3. 安装iterm2
    ```shell
brew cast install iterm2
```

4. 安装zsh
    ```shell
brew install zsh
```

5. 安装tmux
    ```shell
brew install tmux
brew install tmux-xpanes
```

    - 安装TPM插件
    ```shell
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
vi ~/.tmux.conf
```
    添加下面的文本到.tmux.conf中
    ```
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```
    激活TPM如果你在tmux中的话
    ```shell
tmux source ~/.tmux.conf
```
    - 安装tmux themespark  
    添加下面的一行到.tmux.conf中
    ```shell
set -g @plugin 'jimeh/tmux-themepack'
```
    在tmux中，敲入prefix+I，TPM会自动下载并激活该插件  
    再将下面的一行放入.tmux.conf，激活magenta方案
    ```shell
set -g @themepack 'powerline/double/magenta'
```

6. 安装oh my zsh
    ```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
vi ~/.zshrc
```
    在.zshrc中加入下列文本
    ```
export ZSH=${HOME}/.oh-my-zsh
plugins=(history)
source $ZSH/oh-my-zsh.sh
```
    - 安装zsh-autosuggestions插件
    ```shell
brew install zsh-autosuggestions
mkdir ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
cd ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
ln -s /usr/local/share/zsh-autosuggestions/zsh-autosuggestions.zsh zsh-autosuggestions.plugin.zsh
vi ~/.zshrc
```
    添加zsh-autosuggestions到.zshrc中的plugins
    ```
plugins=(zsh-autosuggestions)
```

7. 安装stow
    ```shell
brew install stow
```

### 第二步：配置软件
1. 配置zsh  
    - 更改颜色方案为agnoster
    ```shell
vi ~/.zshrc
```
    修改ZSH_THEME环境变量
    ```
ZSH_THEME="agnoster"
```
    - 设置macos默认shell
    ```shell
sudo vi /etc/shells
```
    添加下列一行
    ```
/usr/local/bin/zsh
```
    执行下列命令
    ```shell
chsh -s /usr/local/bin/zsh
```

2. 配置oh-my-zsh  
    添加有用的插件
    ```
plugins=(git history z tmux vagrant zsh-autosuggestions)
```
    z可以实现快速目录跳转  
    tmux可以实现zsh和tmux的集成  
    vagrant可以提供针对vagrant的自动完成  

3. 配置tmux
    ```shell
vi ~/.tmux.conf
```
    加入下列内容
    ```
set -g prefix `
unbind C-b
bind ` send-prefix

set-option -g base-index 1
set-option -g renumber-windows on
set-option -g set-titles on

set-window-option -g automatic-rename on

bind-key r source-file ~/.tmux.conf \; display "Reloaded!"

unbind-key '"'
bind-key - splitw -v -c '#{pane_current_path}'
unbind-key %
bind-key \ splitw -h -c '#{pane_current_path}' 

bind-key = select-pane -U
bind-key "'" select-pane -D
bind-key [ select-pane -L
bind-key ] select-pane -R

bind-key -n M-= resize-pane -U
bind-key -n M-"'" resize-pane -D
bind-key -n M-[ resize-pane -L
bind-key -n M-] resize-pane -R

bind-key -n M-- set-option synchronize-panes
bind-key -n M-\ resize-pane -Z

bind-key TAB select-window -l
bind-key -n M-, previous-window
bind-key -n M-. next-window
bind-key -n M-< swap-window -t -1
bind-key -n M-> swap-window -t +1

bind-key 1 select-window -t 1
bind-key 2 select-window -t 2
bind-key 3 select-window -t 3
bind-key 4 select-window -t 4
bind-key 5 select-window -t 5
bind-key 6 select-window -t 6
bind-key 7 select-window -t 7
bind-key 8 select-window -t 8
bind-key 9 select-window -t 9
bind-key 0 select-window -t 0

set -g @plugin 'jimeh/tmux-themepack'
set -g @themepack 'powerline/double/magenta'
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
run '~/.tmux/plugins/tpm/tpm'
```

4. 配置dotfiles  
    配合stow我们可以借助git管理我们的配置文件（点文件)
    ```shell
mkdir ~/.dotfiles
cd ~/.dotfiles
mkdir tmux
mv ~/.tmux ~/.tmux.conf tmux/
stow tmux
mkdir oh-my-zsh
mv ~/.oh-my-zsh oh-my-zsh/
stow oh-my-zsh
mkdir zsh
mv ~/.zshrc zsh/
stow zsh
git init
git add --all
git commit -m 'init dotfiles'
```









