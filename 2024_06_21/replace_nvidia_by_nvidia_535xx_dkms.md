# replace_nvidia_by_nvidia_535xx_dkms

因为社区通告<https://t.me/archlinuxcn/274>，决定换掉nvidia驱动。

## Operations

1. 
    `paru -S nvidia-535xx-utils nvidia-535xx-dkms`
    这会卸载掉冲突的包：

    ```cli
    loading packages...
    resolving dependencies...
    looking for conflicting packages...
    :: nvidia-535xx-utils-535.183.01-1 and nvidia-utils-550.90.07-2 are in conflict. Remove nvidia-utils? [y/N] y
    :: nvidia-535xx-dkms-535.183.01-1 and nvidia-550.90.07-2 are in conflict (NVIDIA-MODULE). Remove nvidia? [y/N] y
    :: nvidia-535xx-dkms-535.183.01-1 and nvidia-lts-1:550.90.07-2 are in conflict (NVIDIA-MODULE). Remove nvidia-lts? [y/N]
     y

    Packages (5) nvidia-550.90.07-2 [removal]  nvidia-lts-1:550.90.07-2 [removal]  nvidia-utils-550.90.07-2 [removal]
                 nvidia-535xx-dkms-535.183.01-1  nvidia-535xx-utils-535.183.01-1

    Total Installed Size:  744.00 MiB
    Net Upgrade Size:       68.56 MiB

    :: Proceed with installation? [Y/n] Y
    ```

    然后重启观察。

1. 重启后 `lspci -k` 发现用的是nouveau驱动：

    ```
    08:00.0 3D controller: NVIDIA Corporation GM108M [GeForce 940M] (rev a2)
            Subsystem: Lenovo Device 2225
            Kernel modules: nouveau
    ```

1. 2024/6/25， 0:10:00更新：删除了 nvidia-prime，滚动更新安装了 linux-headers，linux-lts-headers，但是报错：

```
 v2ray-china-list-20240624003118-1-any.pkg.tar.zst failed to download
 Total (120/199)                            1256.2 MiB  8.88 MiB/s 02:21 [########################################] 100%
error: failed retrieving file 'v2ray-china-list-20240624003118-1-any.pkg.tar.zst' from mirrors.bfsu.edu.cn : The requested URL returned error: 404
warning: failed to retrieve some files
error: failed to commit transaction (failed to retrieve some files)
Errors occurred, no packages were upgraded.
```

所以先滚动更新，再安装了linux-headers，linux-lts-headers。
这次安装过程中多了一个之前从未出现的步骤（(3/3) Install DKMS modules）：

```
peter@archlinux ~> sudo pacman -S linux-headers linux-lts-headers
resolving dependencies...
looking for conflicting packages...

Packages (3) pahole-1:1.27-1  linux-headers-6.9.6.arch1-1  linux-lts-headers-6.6.35-2

Total Download Size:     0.29 MiB
Total Installed Size:  256.06 MiB

:: Proceed with installation? [Y/n]
:: Retrieving packages...
 pahole-1:1.27-1-x86_64                      298.3 KiB   912 KiB/s 00:00 [########################################] 100%(3/3) checking keys in keyring                                           [########################################] 100%
(3/3) checking package integrity                                         [########################################] 100%
(3/3) loading package files                                              [########################################] 100%
(3/3) checking for file conflicts                                        [########################################] 100%
(3/3) checking available disk space                                      [########################################] 100%
:: Processing package changes...
(1/3) installing pahole                                                  [########################################] 100%
Optional dependencies for pahole
    ostra-cg: Generate call graphs from encoded traces
(2/3) installing linux-headers                                           [########################################] 100%
(3/3) installing linux-lts-headers                                       [########################################] 100%
:: Running post-transaction hooks...
(1/3) Arming ConditionNeedsUpdate...
(2/3) Updating module dependencies...
(3/3) Install DKMS modules
==> dkms install --no-depmod nvidia/535.183.01 -k 6.6.35-2-lts
==> dkms install --no-depmod nvidia/535.183.01 -k 6.9.6-arch1-1
==> depmod 6.9.6-arch1-1
==> depmod 6.6.35-2-lts
```

1. 之后重启，看看是否使用了正确的nvidia驱动。
    在`lspci`中确实看到不再是nouveau了。
    ```
    08:00.0 3D controller: NVIDIA Corporation GM108M [GeForce 940M] (rev a2)
            Subsystem: Lenovo Device 2225
            Kernel driver in use: nvidia
            Kernel modules: nouveau, nvidia_drm, nvidia
    ```

到此，更换nvidia驱动完成。