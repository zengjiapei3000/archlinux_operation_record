# add-F10-to-switch-vim-between-pastemode-and-nopastemode

为了解决vim 复制粘贴可能会导致文本错乱的问题, 粘贴需要进入 pastemode.
为了简化切换 pastemode 的过程, 写了这个文.

## 操作步骤
打开 `~/.vimrc`, 加入一行:
```
:setpastetoggle=<F10>
```
F10是在insert模式按F10就行.
以后ssh的话, 按下 F10 就能切换 pastemode 和 nopastemode, 但是 gnome-terminal 下需要按 ctrl+F10 才能切换, 不知为什么.