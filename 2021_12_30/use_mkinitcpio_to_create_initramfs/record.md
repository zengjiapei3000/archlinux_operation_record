# Use mkinitcpio to create initramfs

## 参考 archwiki

https://wiki.archlinux.org/title/Mkinitcpio_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

`mkinitcpio` 是一个创建 initramfs 的 bash 脚本.

**每次升级内核**, mkinitcpio都会默认**自动**创建两个内存盘镜像: 
默认镜像`/boot/initramfs-linux.img`和 fallback镜像`/boot/initramfs-linux-fallback.img`.

fallback镜像和默认镜像只有一个区别, 就是创建时跳过了autodetect钩子扩展, 因而它包含更多的内核模块. autodetect扩展会探测硬件信息, 针对硬件向镜像添加需要的模块, 因此缩小了镜像.

在创建初始化内存盘镜像时，可以使用很多不同配置。创建完成后，在[启动引导器配置文件](https://wiki.archlinux.org/title/Arch_boot_process#Boot_loader)中添加启动项目，即可使用新镜像。更改mkinitcpio配置后，需要手动重新生成镜像。以Arch默认的内核linux为例：
```
# mkinitcpio -p linux
```
`-p`选项定义了要使用的预配置(preset); 多数内核软件包都会提供一套 mkinitcpio 预配置文件，放在`/etc/mkinitcpio.d`目录（比如，`linux`内核是`/etc/mkinitcpio.d/linux.preset`）。预配置文件中包含内存盘镜像的基本配置。

**警告**:  *每次升级内核，系统都会自动使用preset文件重新生成内存盘镜像。不要轻易编辑这些文件。*

## 操作
1. `$ sudo mkinitcpio -p linux`
```
    ==> Building image from preset: /etc/mkinitcpio.d/linux.preset: 'default'
  -> -k /boot/vmlinuz-linux -c /etc/mkinitcpio.conf -g /boot/initramfs-linux.img
    ==> Starting build: 5.15.12-arch1-1
  -> Running build hook: [base]
  -> Running build hook: [udev]
  -> Running build hook: [autodetect]
  -> Running build hook: [modconf]
  -> Running build hook: [block]
    ==> WARNING: Possibly missing firmware for module: xhci_pci
  -> Running build hook: [filesystems]
  -> Running build hook: [keyboard]
  -> Running build hook: [fsck]
    Decompress:  1/25 files. Current: ...el/cdrom.ko.zst : 0 B...    
    ==> Generating module dependencies
    ==> Creating zstd-compressed initcpio image: /boot/initramfs-linux.img
    ==> Image generation successful
    ==> Building image from preset: /etc/mkinitcpio.d/linux.preset: 'fallback'
    -> -k /boot/vmlinuz-linux -c /etc/mkinitcpio.conf -g /boot/initramfs-linux-fallback.img -S autodetect
    ==> Starting build: 5.15.12-arch1-1
  -> Running build hook: [base]
  -> Running build hook: [udev]
  -> Running build hook: [modconf]
  -> Running build hook: [block]
    ==> WARNING: Possibly missing firmware for module: wd719x
    ==> WARNING: Possibly missing firmware for module: aic94xx
    ==> WARNING: Possibly missing firmware for module: xhci_pci
  -> Running build hook: [filesystems]
  -> Running build hook: [keyboard]
  -> Running build hook: [fsck]
    Decompress: 442/629 files. Current: ...nel/cifs.ko.zst : 1.12 MiB...    
    ==> Generating module dependencies
    ==> Creating zstd-compressed initcpio image: /boot/initramfs-linux-fallback.img
    ==> Image generation successful
```

发现两处警告: 
```
  -> Running build hook: [block]
    ==> WARNING: Possibly missing firmware for module: xhci_pci
```
```
  -> Running build hook: [block]
    ==> WARNING: Possibly missing firmware for module: wd719x
    ==> WARNING: Possibly missing firmware for module: aic94xx
    ==> WARNING: Possibly missing firmware for module: xhci_pci
```
查阅 [archwiki](https://wiki.archlinux.org/title/Mkinitcpio#Possibly_missing_firmware_for_module_XXXX), `WARNING` 一般不用管, 除非你有相应硬件.
可以通过安装 AUR包(`wd719x-firmware`、`aic94xx-firmware`、`upd72020x-fw`)来修正 WARNING
`$ yay -S wd719x-firmware aic94xx-firmware upd72020x-fw`, 之后会询问:
```
==> Packages to cleanBuild?
==> [N]one [A]ll [Ab]ort [I]nstalled [No]tInstalled or (1 2 3, 1-3, ^4)
...
==> Diffs to show?
==> [N]one [A]ll [Ab]ort [I]nstalled [No]tInstalled or (1 2 3, 1-3, ^4)
```
安装完之后再次运行 `$ sudo mkinitcpio -p linux`, WARNING 已经消失.