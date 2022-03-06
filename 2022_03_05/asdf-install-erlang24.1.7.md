# asdf-install-erlang24.1.7

之前安装了 asdf, 目的是为了同时在本地管理多版本的erlang和elixir, 今天在 asdf-erlang 的基础上安装特定的 erlang/OTP 版本, 日后使用可以用 asdf-erlang 自带的 kerl 二进制文件切换 Erlang/OTP 不同的版本. Erlang 版本为 24.1.7.

## 参考

1. Erlang/OTP github 上的 HOWTO/install 文档, 主要用于寻找 必须依赖 和 可选依赖:
https://github.com/erlang/otp/blob/master/HOWTO/INSTALL.md
2. asdf-erlang 的 github 以及 github wiki:
https://github.com/asdf-vm/asdf-erlang
https://github.com/asdf-vm/asdf-erlang/wiki
3. Kerl 的github, 主要用于借鉴 asdf-erlang 的配置项设置: 
https://github.com/kerl/kerl


## 步骤

### 可选依赖

选择的可选依赖有: openssl, jdk11-openjdk, flex.

#### 构建

1. (可选依赖wxWidget) 用于 GUI 应用程序的工具包. 构建wx应用程序所必需的. 至少需要 3.0 版的 wxWidgets. 
安装 AUR包 `wxgtk2-dev wxgtk3-dev wxgtk-common-dev webkit2gtk` , 这些库将被编译并检测到 `webkit2gtk` 可用, 且因此为了提供 webview 支持编译 wxWidgets. 不需要同时安装 wxgtk2-dev 和 wxgtk3-dev 版本; 任何一个都能工作(反对同时安装):  `yay -S wxgtk2-dev wxgtk-common-dev webkit2gtk`
 其中安装 wxgtk2-dev 可能下载后程序自行配置时间较长(1小时), 是正常现象, 不必紧张.

如果不安装这些软件提供 wxWidget 支持, 之后安装会有如下信息提示, 指出未包含带有 webview 的 wxWidgets:
```
wxWidgets was not compiled with --enable-webview or wxWebView developer package is not installed, wxWebView will NOT be available
```
上述来自 [asdf-erlang 的 wiki](https://github.com/asdf-vm/asdf-erlang/wiki)

**NOTE**:最后因为安装不了 wxgtk2-dev, 尝试更换 wxgtk2-dev-opt, wxgtk3-dev, 这几个都不行, 遂放弃.
2. (可选依赖 openssl)
3. (可选依赖 Oracle Java SE JDK -- The Java Development Kit (Standard Edition)) 构建应用程序所必需的`jinterface`. 至少需要 JDK 1.6.0 版本.
这里安装的包为 `jdk11-openjdk`: `sudo pacman -S jdk11-openjdk`.
安装完之后, 在 `~/.bashrc` 里加入PATH:
```
## Add on 2022-03-05, for java-11-openjdk, the optional dependent 
# of erlang 24.1.7, managed by asdf-erlang.
JAVA_HOME=/usr/lib64/java-11-openjdk
PATH=$PATH:$JAVA_HOME/bin
## End add on 2022-03-05, for java-11-openjdk, the optional dependent 
# of erlang 24.1.7, managed by asdf-erlang.
```
之后 `source ~/.bashrc` 立即生效, 输入 `java --version` 测试一下看版本是否如自己安装.
4. (可选依赖 flex) 需要头文件和库来为 Unix/Linux 上的应用程序构建 Unix/Linux 上的应用程序`megaco` 的 flex 扫描器. `sudo pacman -S flex`

#### 文档构建
 1. (可选依赖 xsltproc) 一个命令行 XSLT 处理器. 将 XSLT 样式表应用于 XML 文档的工具. 跳过.
 2. (可选依赖 fop) Apache FOP 打印格式化程序 (需要 Java). 跳过.

### 正式安装

1. `/home/peter/.asdf/plugins/erlang/kerl update releases` 更新 release list.
(后来觉得这一步可能没必要, 我是因为看到 `asdf_24.1.7 is not a kerl-managed Erlang/OTP installation` 才加的这一步)
2. `asdf install erlang 24.1.7` 安装 erlang 24.1.7.
安装完成后, 执行 `asdf local erlang 24.1.7`, 这一步相当于加入 PATH. 之后可以运行一下 `erl --version` 查看输出信息.