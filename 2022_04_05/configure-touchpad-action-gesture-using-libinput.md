# configure-touchpad-action-gesture-using-libinput

为 Archlinux 的 gnome 添加触控板手势支持。

## references
1. archwiki 上 touchpad synaptics(推荐被libinput替代) 的页面：
https://wiki.archlinux.org/title/Touchpad_Synaptics
2. archwiki 上 touchpad libinput(用于替代synaptics) 的页面：
https://wiki.archlinux.org/title/Libinput
3. 

## operations
1. `sudo pacman -Rsnc xf86-input-syncaptics` 这两个驱动一个新一个老，因为选择用 libinput 为防止冲突，确认 xf86-input-syncaptics 删掉。
2. `sudo pacman -S archlinuxcn/libinput-gestures` 这是一个 libinput 手势相关的包，
3. 安装完成后有一段提示:
```
=== INSTALLATION NOTES for libinput-gestures ===
A user must be a member of the input group, i.e. run:
   sudo gpasswd -a $USER input
A user can start libinput-gestures from their DE, or run:
   libinput-gestures-setup start
A user can enable auto start of libinput-gestures, i.e. run:
   libinput-gestures-setup autostart
Configuration files are at:
   /etc/libinput-gestures.conf (system wide default)
   $HOME/.config/libinput-gestures.conf (optional per user)
```
按他说的，执行 `sudo gpasswd -a {username} input` 把你的用户添加进 input 组里，之后会输出：
```
[sudo] password for peter: 
Adding user peter to group input
```
下一步为了开启，并自动运行libinput-gestures，执行：
```
libinput-gestures-setup start
libinput-gestures-setup autostart
```
start 输出 `libinput-gestures failed to start as a desktop application.`，
autostart 输出 `installed or updated /home/peter/.config/autostart/libinput-gestures.desktop`。 重启看看start是不是有问题。
4. 重启完发现，settings 里 mouse&touchpad 已经好了。