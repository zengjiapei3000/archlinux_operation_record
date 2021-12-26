**更新**: 之后会无法登录.

```
peter@archlinux ~> systemctl --failed
  UNIT                    LOAD   ACTIVE SUB    DESCRIPTION     
● accounts-daemon.service loaded failed failed Accounts Service

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.
1 loaded units listed.
```
`$ sudo pacman -R --assume-installed polkit polkit`
```
$sudo pacman -Syu
:: Synchronising package databases...
 core is up to date
 extra                                                                        1567.3 KiB  1141 KiB/s 00:01 [################################################################] 100% community                                                                       5.9 MiB  1462 KiB/s 00:04 [################################################################] 100% archlinuxcn                                                                  1979.8 KiB  1119 KiB/s 00:02 [################################################################] 100% sublime-text is up to date
:: Starting full system upgrade...
resolving dependencies...
looking for conflicting packages...

Packages (1) hwloc-2.7.0-1

Total Download Size:   0.51 MiB
Total Installed Size:  1.53 MiB
Net Upgrade Size:      0.02 MiB

:: Proceed with installation? [Y/n] Y
:: Retrieving packages...
 hwloc-2.7.0-1-x86_64                                                          522.1 KiB   488 KiB/s 00:01 [################################################################] 100%(1/1) checking keys in keyring                                                                             [################################################################] 100%(1/1) checking package integrity                                                                           [################################################################] 100%(1/1) loading package files                                                                                [################################################################] 100%(1/1) checking for file conflicts                                                                          [################################################################] 100%(1/1) checking available disk space                                                                        [################################################################] 100%:: Processing package changes...
(1/1) upgrading hwloc                                                                                      [################################################################] 100%:: Running post-transaction hooks...
(1/2) Arming ConditionNeedsUpdate...
(2/2) Updating the desktop file MIME type cache...
```
`$ reboot`