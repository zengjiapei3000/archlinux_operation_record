# shell history

## install
先到 https://wiki.archlinux.org/title/NVIDIA#Installation 章节, 因为Nvidia 显卡型号为 GeForce 940M(通过 `$ lspci -k | grep -A 2 -E "(VGA|3D)"` 查询), 在文中找到应安装 archlinux 官方包 nvidia.

**tips**: 
1. 对于32位应用程序支持，请安装`multilib`仓库中对应的 lib32 nvidia 软件包（例如`lib32-nvidia-utils`）.
2. 如果以后切换 Kernel 从`linux`到`linux-lts`时, 可能需要把`nvidia`换成`nvidia-lts`.
```
peter@archlinux ~> sudo pacman -S nvidia
[sudo] password for peter: 
resolving dependencies...
looking for conflicting packages...

Packages (1) nvidia-495.46-5

Total Download Size:   25.68 MiB
Total Installed Size:  25.69 MiB

:: Proceed with installation? [Y/n] Y
:: Retrieving packages...
 nvidia-495.46-5-x86_64           25.7 MiB   974 KiB/s 00:27 [################################] 100%
(1/1) checking keys in keyring                               [################################] 100%
(1/1) checking package integrity                             [################################] 100%
(1/1) loading package files                                  [################################] 100%
(1/1) checking for file conflicts                            [################################] 100%
(1/1) checking available disk space                          [################################] 100%
:: Processing package changes...
(1/1) installing nvidia                                      [################################] 100%
:: Running post-transaction hooks...
(1/2) Arming ConditionNeedsUpdate...
(2/2) Updating module dependencies...
```
 

## Pacman Hooks
为了避免更新 NVIDIA 驱动之后忘了更新initramfs，你可以使用[Pacman Hooks](https://wiki.archlinux.org/title/Pacman_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#Hooks):
`/etc/pacman.d/hooks/nvidia.hook`

```
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia
Target=linux
# Change the linux part above and in the Exec line if a different kernel is used

[Action]
Description=Update Nvidia module in initcpio
Depends=mkinitcpio
When=PostTransaction
NeedsTargets
Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
```
务必保证 `Target` 项所设置的软件包与你在前面的安装过程中所使用的相符（例如`nvidia` 或 `nvidia-dkms` 或 `nvidia-lts` 或 `nvidia-ck-something`）。

**然后重启, nvidia 软件包包含一个屏蔽了 nouveau 内核模块的文件，所以重启是必要的.**

## Xorg configuration
重启之后开始配置nvidia驱动. 
可能需要创建配置文件（优先创建`/etc/X11/xorg.conf.d/20-nvidia.conf`而不是`/etc/X11/xorg.conf`, 可以用`-o OUTPUT-XCONFIG, --output-xconfig=OUTPUT-XCONFIG`参数输出到`/etc/X11/xorg.conf.d/20-nvidia.conf`:
```
[root@archlinux ~]# nvidia-xconfig --output-xconfig=/etc/X11/xorg.conf.d/20-nvidia.conf

Using X configuration file: "/etc/X11/xorg.conf".
New X configuration file written to '/etc/X11/xorg.conf.d/20-nvidia.conf'
```
**这里采用以上这种方法**, 但是依然要进行之后的检查文件.）来调整Xorg运行时的一些设置. 这会修改`/etc/X11/xorg.conf`, 旧文件备份在`/etc/X11/xorg.conf.backup`.
有自动配置和手动配置, 这里使用**自动配置**:
英伟达的软件包已经包含一个自动配置的工具来帮助您创建Xorg服务器配置文件(`xorg.conf`)。您可以通过运行下面的命令来实现自动配置：
```
[root@archlinux ~]# nvidia-xconfig

Using X configuration file: "/etc/X11/xorg.conf".
Backed up file '/etc/X11/xorg.conf' as '/etc/X11/xorg.conf.backup'
New X configuration file written to '/etc/X11/xorg.conf'

```
该命令会自动检测并根据现有硬件的情况创建（或修改）`/etc/X11/xorg.conf`。如果配置文件中存在DRI的话，请把它们注释掉：
```
#    Load        "dri"
```
再一次检查您的配置文件`/etc/X11/xorg.conf`中的默认色深(`DefaultDepth`)、水平同步、垂直刷新和分辨率是否正确.

**tips:**
写到这里中断了, 因为根据 https://wiki.archlinux.org/title/Xorg_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E4%BD%BF%E7%94%A8_.conf_%E6%96%87%E4%BB%B6 里生成 /etc/X11/xorg.conf 发现有 error: `# Xorg :0 -configure`，
它写的:
>执行后会在 `/root/` 生成 `xorg.conf.new` 文件，然后你可以将它复制到 `/etc/X11/xorg.conf`.
>>提示： 如果已经运行了 X 服务器，请使用不同的 display，例如 `# Xorg :2 -configure`。

但当我运行`# Xorg :2 -configure`时, 有错误提示:
```
    root@archlinux /e/X/xorg.confXorg :2 -configure

X.Org X Server 1.21.1.2
X Protocol Version 11, Revision 0
Current Operating System: Linux archlinux 5.15.11-arch2-1 #1 SMP PREEMPT Wed, 22 Dec 2021 09:23:54 +0000 
x86_64
Kernel command line: BOOT_IMAGE=/boot/vmlinuz-linux root=UUID=3132537f-219b-465a-9c25-6e16fd8da09b rw loglevel=3 quiet

Current version of pixman: 0.40.0
        Before reporting problems, check http://wiki.x.org
        to make sure that you have the latest version.
Markers: (--) probed, (**) from config file, (==) default setting,
        (++) from command line, (!!) notice, (II) informational,
        (WW) warning, (EE) error, (NI) not implemented, (??) unknown.
(==) Log file: "/var/log/Xorg.2.log", Time: Sun Dec 26 22:43:51 2021
List of video drivers:
        intel
        nvidia
        modesetting
        vesa
(++) Using config file: "/root/xorg.conf.new"
(==) Using system config directory "/usr/share/X11/xorg.conf.d"


Xorg detected your mouse at device /dev/input/mice.
Please check your config if the mouse is still not
operational, as by default Xorg tries to autodetect
the protocol.

Xorg has configured a multihead system, please check your config.

Your xorg.conf file is /root/xorg.conf.new

To test the server, run 'X -config /root/xorg.conf.new'

(EE) Server terminated with error (2). Closing log file.
```
错误信息可能在`/var/log/Xorg.2.log`里.
之后我运行`#X -config /root/xorg.conf.new`, 直接黑屏, 说明这个配置文件确实有错误.

# others ways
- 1. 先关闭x server, 设置开机默认进入命令行, 重启:
```
peter@archlinux ~ [1]> sudo systemctl set-default multi-user.target
[sudo] password for peter: 
Created symlink /etc/systemd/system/default.target → /usr/lib/systemd/system/multi-user.target.
peter@archlinux ~> sudo reboot
```
- 2. 重启之后 iwctl:
```
peter@archlinux ~> iwctl
NetworkConfigurationEnabled: disabled
StateDirectory: /var/lib/iwd
Version: 1.20
[iwd]# station list
[iwd]# station wlan0 connection "WLAN_SSID"
[iwd]# quit
peter@archlinux ~> ip a
......
peter@archlinux ~> ping baidu.com
```
当显示正常之后结束这一步.
- 3. 生成`/etc/X11/xorg.conf`:
```
peter@archlinux ~ [1]> sudo Xorg :0 -configure
[sudo] password for peter: 

X.Org X Server 1.21.1.2
X Protocol Version 11, Revision 0
Current Operating System: Linux archlinux 5.15.11-arch2-1 #1 SMP PREEMPT Wed, 22 Dec 2021 09:23:54 +0000 
x86_64
Kernel command line: BOOT_IMAGE=/boot/vmlinuz-linux root=UUID=3132537f-219b-465a-9c25-6e16fd8da09b rw loglevel=3 quiet

Current version of pixman: 0.40.0
        Before reporting problems, check http://wiki.x.org
        to make sure that you have the latest version.
Markers: (--) probed, (**) from config file, (==) default setting,
        (++) from command line, (!!) notice, (II) informational,
        (WW) warning, (EE) error, (NI) not implemented, (??) unknown.
(==) Log file: "/var/log/Xorg.0.log", Time: Mon Dec 27 00:06:52 2021
List of video drivers:
        intel
        nvidia
        modesetting
        vesa
(++) Using config file: "/root/xorg.conf.new"
(==) Using system config directory "/usr/share/X11/xorg.conf.d"


Xorg detected your mouse at device /dev/input/mice.
Please check your config if the mouse is still not
operational, as by default Xorg tries to autodetect
the protocol.

Xorg has configured a multihead system, please check your config.

Your xorg.conf file is /root/xorg.conf.new

To test the server, run 'X -config /root/xorg.conf.new'

(EE) Server terminated with error (2). Closing log file.
```
查看 `/var/log/Xorg.-.log`:
```
  1 [  2328.079]
  2 X.Org X Server 1.21.1.2
  3 X Protocol Version 11, Revision 0
  4 [  2328.080] Current Operating System: Linux archlinux 5.15.11-arch2-1 #1 SMP PREEMPT Wed, 22 Dec 2021 09:23:54 +0000 x86_64
  5 [  2328.080] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-linux root=UUID=3132537f-219b-465a-9c25-6e16fd8da09b rw loglevel=3 quiet
  6 [  2328.080]
  7 [  2328.080] Current version of pixman: 0.40.0
  8 [  2328.080]    Before reporting problems, check http://wiki.x.org
  9     to make sure that you have the latest version.
 10 [  2328.080] Markers: (--) probed, (**) from config file, (==) default setting,
 11     (++) from command line, (!!) notice, (II) informational,
 12     (WW) warning, (EE) error, (NI) not implemented, (??) unknown.
 13 [  2328.080] (==) Log file: "/var/log/Xorg.0.log", Time: Mon Dec 27 00:06:52 2021
 14 [  2328.080] (II) Module ABI versions:
 15 [  2328.080]    X.Org ANSI C Emulation: 0.4
 16 [  2328.080]    X.Org Video Driver: 25.2
 17 [  2328.080]    X.Org XInput driver : 24.4
 18 [  2328.080]    X.Org Server Extension : 10.0
 19 [  2328.081] (--) using VT number 2
 20 
 21 [  2328.081] (II) systemd-logind: logind integration requires -keeptty and -keeptty was not provided, disabling logind integration
 22 [  2328.082] (II) xfree86: Adding drm device (/dev/dri/card0)
 23 [  2328.082] (II) Platform probe for /sys/devices/pci0000:00/0000:00:02.0/drm/card0
```
运行 `$ sudo X -config /root/xorg.conf.new` 检测新生成的文件有无问题:
```
peter@archlinux ~ [1]> sudo X -config /root/xorg.conf.new
[sudo] password for peter: 

X.Org X Server 1.21.1.2
X Protocol Version 11, Revision 0
Current Operating System: Linux archlinux 5.15.11-arch2-1 #1 SMP PREEMPT Wed, 22 Dec 2021 09:23:54 +0000 x86_64
Kernel command line: BOOT_IMAGE=/boot/vmlinuz-linux root=UUID=3132537f-219b-465a-9c25-6e16fd8da09b rw loglevel=3 quiet

Current version of pixman: 0.40.0
        Before reporting problems, check http://wiki.x.org
        to make sure that you have the latest version.
Markers: (--) probed, (**) from config file, (==) default setting,
        (++) from command line, (!!) notice, (II) informational,
        (WW) warning, (EE) error, (NI) not implemented, (??) unknown.
(==) Log file: "/var/log/Xorg.0.log", Time: Mon Dec 27 00:15:02 2021
(++) Using config file: "/root/xorg.conf.new"
(==) Using system config directory "/usr/share/X11/xorg.conf.d"
```
黑屏, 使用的`xorg.conf.d`是系统默认的位置`/usr/share/X11/xorg.conf.d`, 按道理应该使用 `/etc/X11/xorg.conf.d`.
- 4. 先`systemctl set-default graphical.target` 重启X Server, `systemctl reload graphical.target`, 再test new文件
