# Qv2ray 折腾记录


## Qv2ray 启动时使用 Wayland 显示协议(**已废弃**, wayland 在 nvidia 驱动下**性能太差**)

### 环境:
本节仅适用于:
- GNU/Linux
- Gnome3(不是 gnome-classic、GNOME on Xorg)

### 参考: 
1. https://qv2ray.net/lang/zh/manual/wayland.html
2. https://forum.manjaro.org/t/howto-check-a-user-session-type-wayland-x11-you-are-using-on-gnome-kde/87165
3. https://unix.stackexchange.com/questions/116539/how-to-detect-the-desktop-environment-in-a-bash-script

### 操作:
1. 先检查会话，有无 wayland 会话(**tip**: 需要在本地机器而不是ssh检查该变量, ssh下显示 `TTY_TYPE`): `echo $XDG_SESSION_TYPE`, 结果输出 `X11`.
2. 继续检查有无 gnome 会话: `echo $DESKTOP_SESSION`, 输出 `gnome`, 满足要求.
3. `$ sudo pacman -S qt5-wayland` 安装 qt5 的 wayland 支持.(**已卸载`$ sudo pacman -Rsn qt5-wayland`**)
4. 把 `QT_QPA_PLATFORM=wayland` 添加到 `~/Desktop/qv2ray.desktop` 的 `[Desktop Entry]` 条目里.(**已注释掉**)
---
## Qv2ray 在 gnome 原生桌面添加 显示托盘图标 的 feature

### 参考:
1. https://qv2ray.net/lang/zh/getting-started/step5.html#%E8%B0%83%E6%95%B4%E7%95%8C%E9%9D%A2-%E8%AF%AD%E8%A8%80
2. [Linux 杀死和注销用户命令#如何远程终止 Linux 登录会话](https://www.cyberciti.biz/tips/howto-linux-kill-and-logout-users.html)
3. [appindicator拓展](https://github.com/ubuntu/gnome-shell-extension-appindicator)

### 操作:
1. `$ sudo pacman -S gnome-tweaks` 安装 gnome tweaks 拓展, 以启用 gnome 拓展功能.
2. `$ sudo pacman -S gnome-shell-extension-appindicator`(也叫 ubuntu-appindicator).
3. 安装扩展后, 注销并重新登录(ssh情况下): 
    ```
    # tty                         # 找到当前的tty, 防止杀掉 root 进程导致:杀死我们当前的进程
    /dev/pts/0

    # ps -fu {userNameHere}
    # ps -fu peter                                                                  # 找到特定用户(这里为 peter)的所有正在运行的进程, 第二列显示 进程的 PID(process ID),
                                                                                    # 第六列显示 我的进程 连接到的 tty, 最后一列是 进程名称.
    UID          PID    PPID  C STIME TTY          TIME CMD
    ...
    ...                                                                             # 从输出中找 "shell PID", 选择不是 当前tty的PID值, 准备杀死
    ...
    peter        923     900  0 00:12 tty2     00:00:00 /usr/lib/gdm-x-session      # 找到目标
    ...
    # kill -HUP {PID}
    # kill -HUP 923                                                                 # 检查: pts2 的 PID, 不是 pts0, 安全

    ```
4. 搜索 `Extensions`, 开启上方标题栏的 按钮, 再找到下方 `appindicator`, 开启, 之后可能要重启， 就可以看到 qv2ray 在上方栏里.