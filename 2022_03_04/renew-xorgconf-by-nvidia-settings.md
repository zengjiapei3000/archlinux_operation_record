# renew-xorgconf-by-nvidia-settings
因为 nouveau 驱动发热很大, 且性能不太好, 所以之前决定配置 nvidia 闭源驱动.
本次是为了完善之前的步骤, 进一步完善配置文件, 但基本上把步骤重头来了一遍.
实测这个 xorg.conf 既能运行 intel核显也能运行 nvidia独显.

## reference
1. nvidia drive 平台的文档的 X11 Window System 文档:
https://docs.nvidia.com/drive/drive_os_5.1.6.1L/nvvib_docs/index.html#page/

DRIVE_OS_Linux_SDK_Development_Guide/Windows%20Systems/window_system_x11.html#
2.  archwiki 的nvidia:
https://wiki.archlinux.org/title/NVIDIA
3. man.archlinux.org 的 nvidia-settings, nvidia-xconfig, Xorg, X, xorg.conf/xorg.conf.d 页面:
https://man.archlinux.org/man/extra/nvidia-settings/nvidia-settings.1.en

https://man.archlinux.org/man/extra/nvidia-utils/nvidia-xconfig.1.en

https://man.archlinux.org/man/X.7

https://man.archlinux.org/man/Xorg.1

https://man.archlinux.org/man/xorg.conf.5

4. fedora wiki 上的 Input_device_configuration#xorg.conf.d:
https://fedoraproject.org/wiki/Input_device_configuration#xorg.conf.d

## operation
1. 根据内核安装对应的 nvidia 驱动, nvidia 对应 linux, nvidia-lts 对应 linux-lts, 其他内核需要 nvidia-dkms. 这里安装了 nvidia 和 nvidia-lts.
`sudo pacman -S nvidia nvidia-lts`

2. 备份之前的 `/etc/X11/xorg.conf`:
`sudo cp /etc/X11/xorg.conf /etc/X11/xorg.conf.gen_by_Xorg_config.bak`

3. 生成一个固定位置的配置文件, `/etc/X11/xorg.conf`, archwiki 是更推荐在 `/etc/X11/xorg.conf.d/20-nvidia.conf` 生成一个最小的配置文件, 我也照做了: 
```
Section "Device"
        Identifier "Nvidia Card"
        Driver "nvidia"
        VendorName "NVIDIA Corporation"
        BoardName "GeForce GeForce 940M"
EndSection
```
但是由于之前用 Xorg 生成的 Xorg 配置文件不够好与nvidia配置文件有冲突, 所以这里还要替换掉原 xorg.conf, 主要参考了 nvidia drive 平台的文档:
`sudo nvidia-xconfig -depth=8` 
, 之后若启动 xserver 黑屏, 需要在 xorg.conf 最后面把 Extension 的 Disable 改成 Enable,
```
Section "Extensions"
    Option         "COMPOSITE" "Enable"
EndSection
```
还要把nvidia 显卡对应的 Screen, device的色深(depth) 改掉, 个人改成了8, 然后把过高的 depth 条目注释掉:
```
Section "Screen"

    #SubSection     "Display"
	#Virtual         1920 1080
	#Depth           16
    #EndSubSection
    #SubSection     "Display"
    #    Virtual     1920 1080
    #    Depth       24
    #EndSubSection
    Identifier     "Screen1"
    Device         "Device1"
    Monitor        "Monitor0"
    DefaultDepth    8
    Option         "UseDisplayDevice" "none"
    SubSection     "Display"
        Virtual     1920 1080
        Depth       4
    EndSubSection
    SubSection     "Display"
        Virtual     1920 1080
        Depth       8
    EndSubSection
EndSection
```
4. 在更新一下之前为了在更新 initramfs 时把nvidia 一起生成 initram 的hook 更新一下, 为 linux 和 linux-lts 两个内核都设置hooks, 位置在 `/etc/pacman.d/hooks/nvidia.hook`:
```
## Add on 2022-03-02, 23:28, for add "nvidia-lts" hooks to update initramfs 
when roll-update nvidia-lts driver.
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia-lts
Target=linux-lts
# Change the linux part above and in the Exec line if a different kernel is 
used
## End add on 2022-03-02, 23:28, for add "nvidia-lts" hooks to update initramfs when roll-update nvidia-lts driver


[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia
Target=linux
# Change the linux part above and in the Exec line if a different kernel is 
used

[Action]
Description=Update Nvidia module in initcpio
Depends=mkinitcpio
When=PostTransaction
NeedsTargets
Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
```

完成.