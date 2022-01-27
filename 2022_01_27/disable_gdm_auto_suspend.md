# Disable GDM auto-suspend

## 原因
因为一般都是用 ssh 连接 gnome 的 Archlinux, gdm 的自动休眠就很讨厌, 需要人去手动在机器上login, 否则 ssh 会休眠.

## 参考
参考了 Arch Wiki 的 GDM 一章 GDM_auto-suspend_(GNOME_3.28) 一节: https://wiki.archlinux.org/title/GDM#GDM_auto-suspend_(GNOME_3.28)

> **GDM auto-suspend (GNOME 3.28)**
>
>GDM uses a separate dconf database to control power management. To apply your user's power settings, copy them to GDM's dconf database:
>
<pre>
$ IFS=$'\n'; for x in $(sudo -u <em>username</em> gsettings list-recursively org.gnome.settings-daemon.plugins.power); do eval "sudo -u gdm dbus-launch gsettings set $x"; done; unset IFS
</pre>

## 我的操作记录
先切换到bash,
```
[peter@archlinux ~]$ source ~/.bashrc
[peter@archlinux ~]$ IFS=$'\n'; for x in $(sudo -u peter gsettings list-recursively org.gnome.settings-daemon.plugins.power); do eval "sudo -u gdm dbus-launch gsettings set $x"; done; unset IFS 
[sudo] password for peter:
```

完成.