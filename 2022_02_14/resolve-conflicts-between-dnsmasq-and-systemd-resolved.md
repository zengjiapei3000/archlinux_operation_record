# resolve-conflicts-between-dnsmasq-and-systemd-resolved

## 参考
1. https://unix.stackexchange.com/questions/304050/how-to-avoid-conflicts-between-dnsmasq-and-systemd-resolved/304798#304798
2. https://zh.opensuse.org/openSUSE:How_to_write_a_systemd_service
3. https://blog.csdn.net/hjx020/article/details/120978201

## 引用
>我最近安装了dnsmasq充当本地网络的DNS服务器。dnsmasq从systemd-resolved侦听本地DNS存根侦听器已在使用的端口53 。
>
>只需停止运行systemd-resolved，然后在dnsmasq运行后重新启动它即可解决此问题。但是它在重新引导后返回：systemd-resolved以优先级启动，并且dnsmasq将不会启动，因为端口53已被使用。
>
>我想，第一个显而易见的问题是，如何使systemd-resolved最好地理解它不应该启动本地DNS存根侦听器，并因此保留端口53供dnsmasq使用？
>
>但是，一个更有趣的问题是这两种服务通常是如何协同工作的。如果使用dnsmasq，它们甚至打算并肩工作还是被systemd解析？

**(deprecated)**
>您可以使用禁用systemd-resolved引导时的加载`sudo systemctl disable systemd-resolved`。
>
>如果要同时运行两者，则可以重定向，systemd-resolved以将localhost用作主要名称服务器。这将确保在访问外部DNS服务器之前，将所有查询定向到dnsmasq以进行解析。这可以通过`nameserver 127.0.0.1`在`/etc/resolv.conf`文件顶部添加行来完成。这也将禁用systemd的本地缓存。
>
>您可以在Arch Linux Wiki上阅读更多内容。我从那里复制了它，覆盖得很好。
>
>但是，这不能可靠地避免启动时的错误，即，如果systemd-resolved碰巧先启动，则dnsmasq仍然会失败。如果您的版本systemd足够新，请使用Malvineous的答案。如果您的版本systemd太旧，则可以通过修改dnsmasq单元来解决此问题：在[Unit]部分中，添加`Before=systemd-resolved`。
>
>此后，如果愿意，可以`/etc/dnsmasq-resolv.conf`为上游名称服务器创建一个单独的文件，然后使用`-r`或`--resolv-file`选项将其传递，或者将上游名称服务器添加到dnsmasq配置文件中并使用`-R`或`--no-resolv`选项。这样，您只有本地主机`/etc/resolv.conf`，一切都通过dnsmasq进行。
>
>— 穆尼尔

原文地址: https://unix.stackexchange.com/questions/304050/how-to-avoid-conflicts-between-dnsmasq-and-systemd-resolved/304078#304078

**(trusted)**
>从systemd联机帮助页判断，它不打算能够手动禁用存根DNS服务器。有趣的是，我仅将systemd从230升级到231后才注意到所描述的问题。
>
>禁用systemd-resolved对我来说是没有选择的，因为我需要它来处理通过DHCP接收的上游DNS服务器。
>
>我的解决方案是让dnsmasq在启动前先停止systemd-resolved，然后再启动它。
>
>我在中创建了一个嵌入式配置`/etc/systemd/system/dnsmasq.service.d/resolved-fix.conf`：
>```
>[Unit]
>After=systemd-resolved.service
>
>[Service]
>ExecStartPre=/usr/bin/systemctl stop systemd-resolved.service
>ExecStartPost=/usr/bin/systemctl start systemd-resolved.service
>```
>这似乎是一个相当棘手的解决方案，但它可以工作。
## 采取做法
(trusted)

## 结果 
没问题.
