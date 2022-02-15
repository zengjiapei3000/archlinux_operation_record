# using_openresolv_to_manage_dnsmasq

因为之前经历过许多服务同时抢占 `/etc/resolv.conf` 的情况, 所以尝试用 openresolv 来管理这些程序以及 `/etc/resolv.conf`.

## 参考

1. 有关 openresolv: https://wiki.archlinux.org/title/openresolv
2. 有关 dnsmasq(用 openresolv 管理的部分): https://wiki.archlinux.org/title/Dnsmasq#openresolv

## 操作步骤

1. 编辑 `/etc/resolvconf.conf` 并添加环回地址作为名称服务器, 并配置 openresolv 以写出 dnsmasq 配置:

```
...(之前的部分)

# Use the local name server
name_servers="::1 127.0.0.1"
resolv_conf_options="trust-ad"

# Write out dnsmasq extended configuration and resolv files
dnsmasq_conf=/etc/dnsmasq-conf.conf
dnsmasq_resolv=/etc/dnsmasq-resolv.conf
```

2. 运行`resolvconf -u`以创建配置文件. 如果文件不存在dnsmasq.service将无法启动.
3. 编辑 dnsmasq 的配置文件(即上面的`/etc/dnsmasq-conf.conf`)以使用 openresolv 生成的配置.

```
# Read configuration generated by openresolv
conf-file=/etc/dnsmasq-conf.conf
resolv-file=/etc/dnsmasq-resolv.conf
```

为了保险, 在 `/etc/dnsmasq.conf` 后面也加两句:

```
conf-file=/etc/dnsmasq-conf.conf
resolv-file=/etc/dnsmasq-resolv.conf
```

完成.
