# Modify .bashrc to drop into fish

因为眼馋 fish 的 feature, 之前尝试过给 zsh 配置来模仿 fish 效果, 但还是不理想. 但是由于 login shell 设置成 fish 可能会 broken system[^1], 所以参考 [archwiki 上的 fish 页](https://wiki.archlinux.org/title/Fish#Modify_.bashrc_to_drop_into_fish) 推荐的做法, bash 作 login shell, 然后 `drop into fish`.
[^1]: https://wiki.gentoo.org/wiki/Fish 和 https://wiki.archlinux.org/title/Fish

```
# Add manually on 2022-01-22 19:25
# Use fish as default shell, but keep bash to login shell
# keep this line at the bottom of ~/.bashrc

# first, To have commands such as bash -c 'echo test' run the command in Bash instead of starting fish.
# this section must above the next section.
if [ -z "$BASH_EXECUTION_STRING" ]; then exec fish; fi

# second, Drop in to fish only if the parent process is not fish.
# This allows to quickly enter in to bash by invoking `bash` command without losing `~/.bashrc` configuration:
if [[ $(ps --no-header --pid=$PPID --format=cmd) != "fish" && -z ${BASH_EXECUTION_STRING} ]]
then
  exec fish
fi
## End manually on 2022-01-22 19:25
```

改完`~/.bashrc`之后, 记得 `$ source ~/.bashrc` 以立即生效.

---
## 2022/02/11 更新:
注释掉了 `if [ -z "$BASH_EXECUTION_STRING" ]; then exec fish; fi`, 这样可以在fish 输入 `bash`时切换到 bash.