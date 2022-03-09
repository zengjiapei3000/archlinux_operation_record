# add-user-in-informant-group

用 paru 重新安装 AUR 的包, 其中有 informant, 安装完成后有一句提示:
`NOTE: Add yourself to group "informant" to avoid the need for sudo`
于是着手把当前用户加入 informant 组.

## reference

1. archwiki 上的 Users and groups 页面的 Group management 一章:
https://wiki.archlinux.org/title/Users_and_groups#Group_management

2. man.archlinux.org 上的 shadow 的手册页列表:
https://man.archlinux.org/listing/core/shadow/

3. man.archlinux.org 上的 group 和 gshadow 页面:
https://man.archlinux.org/man/group.5
https://man.archlinux.org/man/gshadow.5

4. informant 的 github:
https://github.com/bradford-smith94/informant

# operation
1. 根据 参考1, 2的内容, `/etc/group` 和 `/etc/gshadow` 两个文件与本地系统的组有关.
/etc/group 文件是一个文本文件, 用于定义系统上的组. 每行有一个条目: `group_name:password:GID:user_list`.
其中 /etc/gshadow 包含组帐户的影子信息, 如果要维护密码安全性, 则普通用户不得读取此文件. 
2. 执行 `sudo -i cat /etc/group | grep -e '^informant'`, 看当前系统的组有有没有 informant.
结果确实有.
3. 根据 参考2 的列表里的 [groupmems](https://man.archlinux.org/man/core/shadow/groupmems.8.en), `sudo usermod -aG *groupname* *username*` 可以把 user 加入到组里.
4. 重启或者logout之后, 运行 `groups *username*` 检查当前用户所在的 group. 
再运行 `informant list` 检查是否可以以当前用户运行 informant.

完成.