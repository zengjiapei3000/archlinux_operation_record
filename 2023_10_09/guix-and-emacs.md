# guix-and-emacs-and-fl750-f1tof12

## topics

1. 解决root没有emacs的配置文件.
1. 解决DE读不到guix安装的emacs.
1. 解决FL750的F1-F12按键失效.
1. (未解决)解决chrome的右键菜单误触(在右边偏中间打开右键菜单).
1. 解决emacs不能输入中文.

## references

1. <https://wiki.archlinux.org/title/guix>
1. <https://wiki.archlinux.org/title/Nix>
1. <https://lists.gnu.org/archive/html/help-guix/2019-04/msg00096.html>
1. <https://guix.gnu.org/manual/en/html_node/Getting-Started.html>
1. <https://guixcn.github.io/wiki/input-method/>
1. <https://fcitx-im.org/wiki/Input_method_related_environment_variables/zh-hans>

## operations

1. 解决root没有emacs的配置文件, 方法是链接:

    ```bash
    sudo -i
    ln -sf /home/peter/.emacs.d ~/.emacs.d
    ```

    测试一下, 成功.

1. 解决DE读不到guix安装的emacs, 应该添加 $XDG_DATA_DIRS, 按archwiki:nix和guix的方法, 可以如此(举例):
    `export XDG_DATA_DIRS=$HOME/.nix-profile/share:$XDG_DATA_DIRS`
    或者按照打包者说的, 安装sbcl包自动设置xdg变量.
    采用后者.
    `guix install sbcl`

    测试发现成功.

1. 解决FL750在linux下的F1-F12功能键失效, 会变为次级功能键的问题.
    先在bios里设置:

    ```bios->config->keyboard/mouse
    Fn and Ctrl Key swap: disabled
    Fn sticky key: disabled
    F1-F12 as Primary Function: disabled
    ```

    再把键盘调成mac模式, 目前可以加fn键使用F1-F12键.
    基本成功.

1. (未解决, 原因是linux DE里按键下去就出右键菜单, windows按键松开后才弹出右键菜单)chrome的右键菜单误触(当在右边靠净重键打开右键菜单时).

1. emacs的中文输入问题, 这同时也是一个症状: emacs启动时有一行`Warning: locale not supported by C library, locale unchanged`.
   这个参考了fcitx的wiki:输入法相关环境变量, 和guixcn:设置输入法.
   在`~/.bashrc.d/guix.bashrc`里加上
   
   ```~/.bashrc.d/guix.bashrc
   export GUIX_GTK3_IM_MODULE_FILE="$HOME/.guix-profile/lib/gtk-3.0/3.0.0/immodules-gtk3.cache"
   export GUIX_LOCPATH="$HOME/.guix-profile/lib/locale"
   ```
   
   新开一个terminal, 打开emacs测试, 输入中文, 成功.
