# asdf-install-elixir1.11.4

之前安装了 asdf 管理的 erlang 24.1.7, 今天安装 elixir 1.11.4.

## 参考
1. 检查 elixir 和 erlang 之间的 版本兼容性:
https://hexdocs.pm/elixir/master/compatibility-and-deprecations.html#compatibility-between-elixir-and-erlang-otp 
2. asdf-elixir 的 github:
https://github.com/asdf-vm/asdf-elixir
3. asdf 的官方网站, 检查 `.tool_versions` 文件和 environment variables 的配置是否正确:
https://asdf-vm.com/manage/configuration.html#tool-versions
https://asdf-vm.com/manage/configuration.html#environment-variables

## 操作步骤

### 安装前的准备
1. 检查 elixir 和 erlang 之间的版本兼容性, elixir 1.11.4 兼容的 Erlang 版本是 21-24, 而 elixir 1.11 大版本只兼容 21-23, 通过.
2. 新建 `～/.tool_versions` 文件, 内容为:
```
erlang 24.1.7
elixir 1.11.4
```
3. 编辑 `～/.bashrc` 文件, 添加 `ASDF_DEFAULT_TOOL_VERSIONS_FILENAME` 的值:
```
## Add on 2022-02-27, for additional configure asdf-vm(AUR) package,
# refer to https://asdf-vm.com/manage/configuration.html#environment-variables
export ASDF_DEFAULT_TOOL_VERSIONS_FILENAME=".tool_versions"
## End add on 2022-02-27, for additional configure asdf-vm(AUR) package,
# refer to https://asdf-vm.com/manage/configuration.html#environment-variables
```
之后使之立即生效: `source ~/.bashrc`.
4. 执行 `asdf install`, asdf 会自动按 `。tool_versions` 文件的版本和包安装.
之后可以检查一下 elixir 版本: `elixir --version`.