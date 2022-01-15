# softlink .vimrc

为了方便备份dotfiles, 把.vimrc 和.gvimrc 统一放在 `~/.vim/` 目录下, 然后用软链接 `$ ln -s 源文件 链接目标文件`.
```
% ln -s ~/.vim/.vimrc ~/.vimrc
% ln -s ~/.vim/.gvimrc ~/.gvimrc
```
之后运行 vim, 配置文件成功加载!

**参考** : https://www.jianshu.com/p/84d1a4bfb00c