# "pacman -Syu" error2
今天滚动更新系统, `$ sudo pacman -Syu --disable-download-timeout`, 更新途中ssh断掉, 再连上ssh更新提示:
```
peter@archlinux ~> sudo pacman -Syu --disable-download-timeout
[sudo] password for peter: 
:: Synchronising package databases...
error: failed to synchronize all databases (unable to lock database)
``` 

按之前的处理办法, 没能找到`/var/lib/pacman/db.lck`.
过一段时间再次执行`$ sudo pacman -Syu --disable-download-time`, 竟然提示系统更新完成, 可能是ssh断掉不影响系统更新进程吧...

为以防万一, 之后执行: `$ sudo pacman -Syyu --disable-download-time`进行强制更新.