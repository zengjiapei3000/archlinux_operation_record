# edit-grub.cfg-to-display-service-status

因为想把arch linux 改成启动时能像debian、ubuntu 一样看到启动时的service启动状态, 所以记录一下.

## 参考

1. 有关 grub 的配置: https://www.gnu.org/software/grub/manual/grub/html_node/Simple-configuration.html#Simple-configuration
2. 有关 `update-grub: command not found` 的解释: https://askubuntu.com/questions/418666/update-grub-command-not-found
3. 有关 `/etc/default/grub` 里的 
    ```
    GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
    GRUB_CMDLINE_LINUX=""
    ``` 
    两个参数的区别: https://unix.stackexchange.com/questions/440961/grub-cmdline-linux-default-vs-grub-cmdline-linux
4. 有关双系统探测其他系统的内容: https://wiki.archlinux.org/title/GRUB#Detecting_other_operating_systems

## 思路

根据 1. 的内容, 文件 `/etc/default/grub` 控制 `grub-mkconfig` 命令.

主要参考 3. 的内容, 把 `GRUB_CMDLINE_LINUX=""`设置添加到 `/etc/default/grub`.
因为是双系统, 要注意输出里有关 os-prober 的内容, 如果没有识别 win7 的 NTFS 文件系统, 要根据 1. 和 4. 的内容, 把 `/etc/default/grub` 里的 `GRUB_DISABLE_OS_PROBER=false` 这行在最后的注释取消(或注释).

## 操作步骤

1. 修改 `/etc/default/grub`. 把 `GRUB_CMDLINE_LINUX=""` 添加进去, 原来的 `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"` 注释掉; 再把 `GRUB_DISABLE_OS_PROBER=false` 取消注释.
2. 执行 `$ sudo grub-mkconfig -o /boot/grub/grub.cfg`, 观察输出里有关 os-porber 的输出.
3. 重启检查一遍.
