# change openresolv config to use nix

因为要装 nix 来管理一些锁版本的包, 但是运行 `nix-channel --update` 时, 会有error:
```
warning: error: unable to download 'https://mirrors.bfsu.edu.cn/nix-channels/nixpkgs-unstable': Couldn't resolve host name (6); retrying in 2075 ms        
error: unable to download 'https://mirrors.bfsu.edu.cn/nix-channels/nixpkgs-unstable': Couldn't resolve host name (6)
```
初步认为是 dnsmasq 的问题, 后来一看 [archwiki的iwd章](https://wiki.archlinux.org/title/Iwd#Select_DNS_manager), 当 NetworkManager 选 iwd 作为 wifi_backend 的时候, dns manager 只能选 systemd-resolved 或者 resolvconf.
---
其实后来证明跟 dnsmasq 无关, 但是单独改了这些配置文件之后就好了.

## 参考
1. archwiki 上 iwd 一章: https://wiki.archlinux.org/title/Iwd#Select_DNS_manager
2. archwiki 上 openresolv 一章: https://wiki.archlinux.org/title/Openresolv

# 操作步骤
1. 把 `/etc/dnsmasq.conf` 最后两行之前加进去的两行用 openresolv 的命令`$ sudo resolvconf -u` 生成的两行删掉(这步其实不关键, 只是为以前的废操作擦屁股).
2. 把 openresolv 的配置文件 `/etc/resolvconf.conf` 的 `# Use the local name server` 小部分的注释掉:
```
# Configuration for resolvconf(8)
# See resolvconf.conf(5) for details

resolv_conf=/etc/resolv.conf
# If you run a local name server, you should uncomment the below line and
# configure your subscribers configuration files below.
#name_servers=127.0.0.1
#
# Use the local name server
#name_servers="::1 127.0.0.1"
#resolv_conf_options="trust-ad"

# Write out dnsmasq extended configuration and resolv files
#dnsmasq_conf=/etc/dnsmasq-conf.conf
#dnsmasq_resolv=/etc/dnsmasq-resolv.conf
```
3. 使用 openresolv 的命令重新生成 `/etc/resolv.conf`:
`sudo resolvconf -u`
4. 重新启动, 以使 systemd 重新把网络服务reload(这一步其实可以换成 `systemctl daemon-reload` 之后再
`systemctl restart NetworkManager`).

完成.