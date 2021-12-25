# shell history

2021/12/24/17:27 archlinux
shell: fish
`$ sudo pacman -S gdm`
```
peter@archlinux ~> sudo pacman -S gdm
resolving dependencies...
looking for conflicting packages...

Packages (19) alsa-plugins-1:1.2.6-1  cracklib-2.9.7-2  gnome-autoar-0.4.1-1
              gnome-bluetooth-3.34.5-2  gnome-disk-utility-41.0-1
              gnome-session-40.1.1-1  gnome-settings-daemon-41.0-1
              gnome-shell-1:41.2-2  gst-plugin-pipewire-1:0.3.42-2  gtk4-1:4.4.1-1    
              libgdm-41.0+r15+g23ebe617-1  libgweather-40.0-1  libpwquality-1.4.4-5   
              mutter-41.2-1  pulseaudio-alsa-1:1.2.6-1  pulseaudio-bluetooth-15.0-1   
              startup-notification-0.12-7  zenity-3.41.0-1  gdm-41.0+r15+g23ebe617-1  

Total Download Size:    18.96 MiB
Total Installed Size:  128.98 MiB

:: Proceed with installation? [Y/n] Y
```
`$ sudo systemctl disable sddm.service`
`$ sudo systemctl enable gdm.service`
`$ sudo reboot`
`$ sudo pacman -S gnome-session gnome-terminal`
`$ sudo pacman -Rsnuc sddm sddm-kcm darkhttpd plasma-pa dolphin kdeplasma-addons khotkeys plasma-vault plasma-browser-integration`
`$ sudo pacman -Rsu plasma`
此时出错，机器wifi下载无速度
**开始解决网络问题:**
`$ sudo reboot`
重启后无网络, 无法ssh.
查找有无办法使用本地cache安装plasma组.
确保系统已经启用了 网络接口，用 ip-link(8) 检查:
`$ ip link` 显示正常, `ping archlinux.org` 正常, 说明 ICMP 正常.
要连接到 WiFi, 使用 `iwctl` 验证无线网络. 
输入 `$ iwctl` 进入 iwd交互式命令行.
`[iwd]# device list` 看到 wlan0;
`[iwd]# station wlan0 scan` 探测附近WiFi;
`[iwd]# station device get-networks` 显示附近WiFi的SSID和其他信息;
`[iwd]# station device connect {SSID}` 连接指定SSID的WiFi, 之后弹出输入密码.
(经过一顿操作，发现其实之前可能已经连上了...)
`ip addr`发现 局域网内 ip 地址, ssh 连接成功.
`$ sudo pacman -S gnome`, 全部安装.
安装完之后 `$ sudo reboot`.
`$ sudo pacman -S networkmanager`全部安装后 `$ sudo reboot`.
**网络问题解决.**
重启之后基本解决问题，完成从 kde plasma 到 gnome3 的切换.
`$ sudo pacman -Rsnu qt5`
```
[sudo] password for peter: 
checking dependencies...
warning: removing qt5-declarative from target list
warning: removing qt5-svg from target list
warning: removing qt5-base from target list
warning: removing qt5-x11extras from target list
warning: removing qt5-wayland from target list
warning: removing qt5-quickcontrols from target list
warning: removing qt5-quickcontrols2 from target list
warning: removing qt5-graphicaleffects from target list
warning: removing qt5-speech from target list
warning: removing qt5-webengine from target list
warning: removing qt5-location from target list
warning: removing qt5-sensors from target list
warning: removing qt5-webchannel from target list
warning: removing qt5-tools from target list
warning: removing qt5-imageformats from target list
warning: removing tslib from target list
warning: removing md4c from target list
warning: removing qt5-translations from target list
warning: removing libmng from target list
warning: removing qt5-multimedia from target list
:: marble-common optionally requires qt5-serialport: APRS plugin
:: vlc optionally requires gnu-free-fonts: subtitle font

Packages (27) assimp-5.1.2-1  gnu-free-fonts-20120503-8  noto-fonts-20201226-2
              python-shiboken2-5.15.2-13  pyside2-5.15.2-5  qt5-3d-5.15.2+kde+r39-1    
              qt5-charts-5.15.2-1  qt5-connectivity-5.15.2+kde+r5-1
              qt5-datavis3d-5.15.2-1  qt5-doc-5.15.2-2  qt5-examples-5.15.2-2
              qt5-gamepad-5.15.2-1  qt5-lottie-5.15.2-1  qt5-networkauth-5.15.2-1      
              qt5-purchasing-5.15.2-1  qt5-quick3d-5.15.2+kde+r19-3
              qt5-quicktimeline-5.15.2-1  qt5-remoteobjects-5.15.2-1
              qt5-script-5.15.7-1  qt5-scxml-5.15.2-1  qt5-serialbus-5.15.2-1
              qt5-serialport-5.15.2-1  qt5-virtualkeyboard-5.15.2+kde+r4-1
              qt5-webglplugin-5.15.2-1  qt5-websockets-5.15.2+kde+r7-1
              qt5-webview-5.15.2-1  qt5-xmlpatterns-5.15.2-1

Total Removed Size:  843.14 MiB

:: Do you want to remove these packages? [Y/n] Y
```
最后 `$ sudo pacman -Syu`.完成后继续`$ sudo reboot`, 以避免滚挂.