# reinstall-nix-singleusermode-after-full-uninstall

之前在 arch 上装过 nix, 但可能是删除不够彻底(nix 官方删除教程的锅), 之后不管单用户模式还是多用户模式都会有问题, 单用户模式错误信息一般是 404not found 或者 can't resolved host name, 多用户模式错误信息是 nixbld 用户已存在.

## reference
1. https://nixos.org/manual/nix/stable/installation/installing-binary.html

## operation
1. 先 `sudo rm -rf /nix`
2. 再 `sudo rm -rf /etc/profile/nix.sh /etc/nix /nix ~root/.nix-profile ~root/.nix-defexpr ~root/.nix-channels ~/.nix-profile ~/.nix-defexpr ~/.nix-channels`
(来源是参考1的 ’多用户模式卸载‘ 部分)
3. `sudo userdel nixbld01` 从 nixbld01 到 nixbld10 全部删除(单用户模式),
或者`sudo userdel nixbld1` 从 nixbld1 到 nixbld32 全部删除(多用户模式).
4. `sudo groupdel nixbld` 删掉group.
5. 再安装单用户模式， 问题解决了:
```
% sh <(curl https://mirrors.bfsu.edu.cn/nix/latest/install) --no-daemon
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4048  100  4048    0     0  16184      0 --:--:-- --:--:-- --:--:-- 16192
downloading Nix 2.6.1 binary tarball for x86_64-linux from 'https://mirrors.bfsu.edu.cn/nix//nix-2.6.1/nix-2.6.1-x86_64-linux.tar.xz' to '/tmp/nix-binary-tarball-unpack.UUCqf2mnue'...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 27.3M  100 27.3M    0     0   982k      0  0:00:28  0:00:28 --:--:-- 1177k
Note: a multi-user installation is possible. See https://nixos.org/manual/nix/stable/installation/installing-binary.html#multi-user-installation
performing a single-user installation of Nix...
directory /nix does not exist; creating it by running 'mkdir -m 0755 /nix && chown peter /nix' using sudo
copying Nix to /nix/store...........................................
installing 'nix-2.6.1'
building '/nix/store/gzyhl5kzycbw25km6raj1l5vgzil3iq7-user-environment.drv'...
unpacking channels...

Installation finished!  To ensure that the necessary environment
variables are set, either log in again, or type

  . /home/peter/.nix-profile/etc/profile.d/nix.sh

in your shell.
```
6. 最后 `. /home/peter/.nix-profile/etc/profile.d/nix.sh` 使 nix 的环境变量立即生效.