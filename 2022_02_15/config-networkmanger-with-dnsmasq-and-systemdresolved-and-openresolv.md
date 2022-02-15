# config-networkmanger-with-dnsmasq-and-systemdresolved-and-openresolv

重新配置下 networkmanager.

## 参考
1. archwiki 上的有关 networkmanager、systemd-resolvd、dnsmasq、openresolv 的部分:
   https://wiki.archlinux.org/title/NetworkManager
   https://wiki.archlinux.org/title/Systemd-networkd
   https://wiki.archlinux.org/title/Dnsmasq
   https://wiki.archlinux.org/title/openresolv

## 操作步骤
1. 先把 iwd 设置成 networkmanager 的后端, 编辑 `/etc/NetworkManager/conf.d/wifi_backend.conf`:
```
[device]
wifi.backend=iwd
```
2. 设置 dns 管理, 编辑 `/etc/NetworkManager/conf.d/dns.conf`:
```
[main]
dns=dnsmasq
```

>**Note:**
>- 您无需启动dnsmasq.service或编辑`/etc/dnsmasq.conf`. NetworkManager 将在不使用 systemd 服务且不读取 dnsmasq 的默认配置文件的情况下启动 dnsmasq。
>- NetworkManager 启动的 dnsmasq 实例将绑定到127.0.0.1:53，您不能在同一地址和端口上运行任何其他软件（包括dnsmasq.service）。

3. 根据 [archwiki的iwd部分](https://wiki.archlinux.org/title/Iwd#Select_DNS_manager), 因为用的是 iwd, 且 iwd 的 dns管理器只能选 systemd-resolved 或 resolvconf(与openresolv冲突, 排除), 所以只能让 systemd-resolved 和 dnsmasq 共存, 否则 dns 没法解析.
先把 2. 的`/etc/NetworkManager/conf.d/dns.conf` 改了:
```
[main]
dns=dnsmasq
dns=systemd-resolved
```

再根据[archwiki的NetworkManager章Use_Openresolv节](https://wiki.archlinux.org/title/NetworkManager#Use_openresolv)的 Note, 所以编辑 `/etc/NetworkManager/conf.d/rc-manager.conf`:
```
#[main]
#rc-manager=resolvconf
```

完成.