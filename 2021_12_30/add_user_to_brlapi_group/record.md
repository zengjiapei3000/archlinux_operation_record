# Add user to brlapi group
今天例行`$ sudo pacman -Syu --disable-download-timeout` 有一行输出:
```
(3/9) upgrading brltty                                    [###############################] 100%
Please add your user to the brlapi group.
New optional dependencies for brltty
    tcl: tcl support [installed]
```
去了解一下这个 brltty, 结果是一个守护进程, 和盲文有关的...
>BRLTTY是一个后台进程（守护进程），它为使用可刷新盲文显示器的盲人提供对 Linux/Unix 控制台（在文本模式下）的访问。它驱动盲文显示器，并提供完整的屏幕查看功能。还加入了一些语音功能。
遂无视之...