# pass-cli-init-configure

pass 命令行工具用来作为密码管理器。但是并不是开箱即用的，需要配置之后才能使用。

## References

1. <https://www.passwordstore.org/>
1. <https://git.zx2c4.com/password-store/about/>

## Operations

1. 先安装好pass。
    `sudo pacman -S pass`

1. pass init {gpg-id} 指定解密gpg id，并生成文件夹。 
    `pass init 4AF1BF2D4BAE81EA`

1. 生成一个名为Email的子文件夹。
    `pass init --path=Email 4AF1BF2D4BAE81EA`

1. 设置好git --global user.name、user.email，初始化上一本生成的文件夹git。
    `pass git init`
    此时会把文件夹的东西自动生成commit。
    再添加remote git repo。
    `pass git remote add origin git@github.com:{github_username}/{repo_name}.git`
    `pass git push origin -u -all`

1. 完成。