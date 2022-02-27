# using-asdf-vm-tool-version-manager

因为archlinux 是滚动发行版, 有些时候有锁版本的需求, 所以安装了 asdf.

## 参考
1. https://asdf-vm.com/guide/getting-started.html#_3-install-asdf 的 "bash&Pacman" 和 "zsh&Pacman".
2. https://github.com/asdf-vm/asdf-erlang#install 和 https://github.com/asdf-vm/asdf-erlang#before-asdf-install
3. https://github.com/asdf-vm/asdf-elixir#install 和 https://github.com/asdf-vm/asdf-elixir##before-asdf-install
## 操作步骤

分为三大步, 安装 asdf 并配置环境变量, 安装 asdf-erlang, 和安装 asdf-elixir.
**tip**: 只安装asdf和插件, 而不安装具体erlang和elixir版本, 这两个**本文后面**记录.
### 安装 asdf 并配置环境变量
1. `yay -S asdf-vm`
2. 上一步安装完成之后在 `~/.bashrc` 和 `~/.zshrc` 里加一行:
```
. /opt/asdf-vm/asdf.sh
```
3. `source ~/.bashrc`, `source ~/.zshrc`.使其立即生效.

### 安装 asdf-erlang 
asdf-erlang 是用于构建依赖于 kerl 的 asdf 版本管理器的 Erlang 插件.
是能结合 kerl 的版本管理工具, 且无需下载 kerl.
kerl 的兼容性和构建脚本，以及 asdf 的简单版本切换和对 `.tool-versions` 文件的支持。您无需安装 kerl 即可使用它。该插件将自动安装它自己的kerl版本.

1. 首先阅读 https://github.com/asdf-vm/asdf-erlang#before-asdf-install, 把 build tools 先准备好:
>Provides most of the needed build tools. `pacman -S --needed base-devel`
>
>Needed for terminal handling `pacman -S ncurses`
>
>For building with wxWidgets (start observer or debugger!) `pacman -S glu mesa wxgtk2 libpng`
>
>For building ssl `pacman -S libssh`
>
>ODBC support sudo `pacman -S unixodbc`
>
>For building documentation and elixir reference builds: `sudo pacman -S libxslt fop`
2. 跳到 https://github.com/asdf-vm/asdf-erlang#install, 运行:
`asdf plugin add erlang https://github.com/asdf-vm/asdf-erlang.git`.
### 安装 asdf-elixir 
asdf 版本管理器的 Elixir 插件.

1. 首先, asdf-elixir 需要 `unzip`: `sudo pacman -S unzip`.
2. 注意, Elixir 需要安装 Erlang. 可使用 asdf-erlang 来安装 Erlang 版本.
2. 安装 asdf-elixir: `asdf plugin-add elixir https://github.com/asdf-vm/asdf-elixir.git`.

到此完成本次配置.
## 后续注意事项

[asdf-elixir](https://github.com/asdf-vm/asdf-elixir) 中提到的:
1. 检查 elixir 和 erlang 之间的兼容性：
https://hexdocs.pm/elixir/master/compatibility-and-deprecations.html#compatibility-between-elixir-and-erlang-otp
2. Elixir 预编译版本. 
除了 Elixir 版本外, 还要注意 Erlang/OTP 版本, 如 Elixir 1.11.4 有多个 Erlang/OTP 版本(21,22,23,24), Elixir 1.11大版本与 1.11.4 不完全相同(1.11 有 21,22,23 多个版本):
>**Note**: 你不需要安装 Erlang 来安装 Elixir 的预编译版本，但你需要在运行时让它可用，否则你的 Elixir 命令将失败。尽管可以先安装 Elixir，但还是建议先安装 Erlang.
https://github.com/asdf-vm/asdf-elixir#elixir-precompiled-versions
3. 从 Git reference 或源代码编译.
(另还包括 `.tool-versions` 文件):
https://github.com/asdf-vm/asdf-elixir#compiling-from-a-git-reference-or-from-source
4. Elixir escripts 支持.
这个插件支持 elixir 脚本，就像任何其他 elixir 二进制文件一样将它们添加到您的路径中。每当您用 `mix escript.install` 安装新的 escript 时，您都需要 `asdf reshim elixir` 为它创建 shims:
https://github.com/asdf-vm/asdf-elixir#elixir-escripts-support

[asdf-erlang](https://github.com/asdf-vm/asdf-erlang) 中提到的:
1. 获取 Erlang 文档:
https://github.com/asdf-vm/asdf-erlang#getting-erlang-documentation