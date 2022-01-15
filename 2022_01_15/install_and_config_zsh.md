# install and config zsh

1. 安装包
安装 zsh: `$ sudo pacman -S zsh zsh-doc zsh-syntax-highlighting zsh-autosuggestions zsh-completions`

在 AUR 安装 antigen(zsh-plugin-manager):
`$ yay -S antigen`

2. 配置
创建 `~/.zshrc`.

**前半部分**按照 https://wiki.archlinux.org/title/Zsh_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E9%85%8D%E7%BD%AE_Zsh 的大概格式**略作修改**添加到 `.zshrc`.

```
# Lines configured by zsh-newuser-install
# End of lines configured by zsh-newuser-install
# The following lines were added by compinstall
zstyle :compinstall filename '/home/peter/.zshrc'

autoload -Uz compinit
compinit
# End of lines added by compinstall


autoload -Uz compinit promptinit
compinit
promptinit

# 设置 walters 主题的默认命令行提示符
#prompt walters
PROMPT='%F{green}%n%f@%F{magenta}%m%f %F{blue}%B%~%b%f %# '
RPROMPT='[%F{yellow}%?%f]
```

**后半部分**按照 https://github.com/zsh-users/antigen#usage 的格式**略作修改**添加到 `.zshrc`.

```
# 用 antigen 管理 zsh plugin
source /usr/share/zsh/share/antigen.zsh
# use oh-my-zsh
antigen use oh-my-zsh
# Bundles from the default repo(escape zsh-users/zsh-syntax-highlighting)  
antigen bundle command-not-found
antigen bundle git
antigen bundle heroku
antigen bundle pip
antigen bundle lein

antigen bundle zsh-users/zsh-autosuggestions
antigen bundle zsh-users/zsh-completions
antigen bundle zsh-users/zsh-history-substring-search
# Syntax highlighting bundle.
antigen bundle zsh-users/zsh-syntax-highlighting
# Load the theme.
#
# Tell Antigen that you're done.
antigen apply
```
**tip**: `zsh-users/zsh-syntax-highlighting` 一定要放在最后, 见 https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#with-a-plugin-manager.

配置完之后, 运行 `% source ~/.zshrc`, 输出:

```
Seems robbyrussell/oh-my-zsh is already installed!
Seems robbyrussell/oh-my-zsh is already installed!
Seems robbyrussell/oh-my-zsh is already installed!
Seems robbyrussell/oh-my-zsh is already installed!
Seems robbyrussell/oh-my-zsh is already installed!
Seems zsh-users/zsh-autosuggestions is already installed!
Seems zsh-users/zsh-completions is already installed!
Seems zsh-users/zsh-history-substring-search is already installed!
Seems zsh-users/zsh-syntax-highlighting is already installed!
```

测试, 几个插件安装完成.