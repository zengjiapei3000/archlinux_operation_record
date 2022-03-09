# change-yay-to-paru

听 arch 群说了一个新的 AUR helper, 而且跟 yay 一样还是 pacman wrapper, 就想试一下.
结果一试发现解决了几个问题:
1. yay 经常安装到一半要求输入sudo密码, 否则直接超时取消安装. paru 虽然也有瑕疵(安装到一半需要 Y 确认), 但不会因为超时取消安装.
2. paru 在安装的第一步看到 PKGBUILD 文件, 这是 packager 打包时写的.
3. paru 安装 AUR 包时更快了.

## reference
1. paru 的 github:
https://github.com/Morganamilo/paru

2. archwiki 的 AUR helper 页面的 Pacman wrappers 章节:
https://wiki.archlinux.org/title/AUR_helpers

## operation
1. `sudo pacman -Syu paru` 安装 paru.
2. 用paru 安装一个包测试:
`paru -Syu sublime-text4`

完成.