# 记一次 pacman error
执行 `$ sudo pacman -Syu --disable-download-timeout`
然后输出:
```
:: Synchronising package databases...
error: failed to synchronize all databases (unable to lock database)
```
需要删除 `/var/lib/pacman/db.lck`
```
$ sudo rm /var/lib/pacman/db.lck`
```

删掉之后, 解决问题.