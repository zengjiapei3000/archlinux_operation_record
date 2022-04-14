# auto-power-on-after-boot-or-resume
因为每次开机，蓝牙都是关闭状态，遂参考Archwiki 使其开机自动打开蓝牙。

## reference
1. Archwiki 的 bluetooth 页面的 Configuration 章节:
https://wiki.archlinux.org/title/Bluetooth#Auto_power-on_after_boot/resume

## operation
1. `sudo vim /etc/bluetooth/main.conf`, 编辑该文件，找到 `[Policy]` 部分，在 `#AutoEnable=false` 下方另起一行，加入: `AutoEnable=true`。

完成。