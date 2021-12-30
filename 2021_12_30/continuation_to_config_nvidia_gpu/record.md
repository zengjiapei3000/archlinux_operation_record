# continuation_to_config_nvidia_gpu_driver

之前在 archlinux [安装了nvidia显卡驱动](../../2021_12_26/install_nvidia_gpu_driver/shell_history.md), 但没有完全配置好配置文件.
采用**手工配置**.
## 参考
1. [archwiki nvidia驱动](https://wiki.archlinux.org/title/NVIDIA#Manual_configuration)
2. [nvidia 官方 Linux 驱动安装教程](https://download.nvidia.com/XFree86/Linux-x86_64/470.63.01/README/)

##  操作

### Minimal configuration
添加 `/etc/X11/xorg.conf.d/20-nvidia.conf`:
   ```
    Section "Device"
        Identifier "Nvidia Card"
        Driver "nvidia"
        VendorName "NVIDIA Corporation"
        BoardName "GeForce 940M"
    EndSection
   ```

### Disabling the logo on startup
在 `Section "Device"`下继续添加 `"NoLogo"` 选项:
   `Option "NoLogo" "1"`

### Overriding monitor detection
在 `Section "Device"`下添加 `"ConnectedMonitor"` 选项, 允许在 X 服务器启动时覆盖监视器检测, 可以在启动时节省大量时间.
可用选项包括: `"CRT"`用于模拟连接, `"DFP"`用于数字显示器, `"TV"`用于电视.
**tips**: 对所有类似 15 针 VGA 连接使用"CRT"，即使显示器是平板显示器。“DFP”仅适用于 DVI、HDMI 或 DisplayPort 数字连接.
   ```
    Option "ConnectedMonitor" "CRT"  
   ```
### (自己操作没有参考教程不推荐他人学) 把 /etc/X11/xorg.conf.backup 复制为 /etc/X11/xorg.conf
   上面操作完了 reboot X server 好像没有启动, 于是复原了之前的 /etc/X11/xorg.conf:
   `$ sudo cp /etc/X11/xorg.conf.backup /etc/X11/xorg.conf`
    再次 reboot, X server 启动成功.