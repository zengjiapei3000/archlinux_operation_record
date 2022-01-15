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

输出暂未发现问题:
```
peter@archlinux /v/l/p/sync> sudo pacman -Syyu --disable-download-timeout
:: Synchronising package databases...
 core                  136.7 KiB   185 KiB/s 00:01 [#################] 100% extra                1557.6 KiB  2.17 MiB/s 00:01 [#################] 100% community               6.0 MiB  2.71 MiB/s 00:02 [#################] 100% archlinuxcn          1989.2 KiB   403 KiB/s 00:05 [#################] 100%:: Starting full system upgrade...
 there is nothing to do
```