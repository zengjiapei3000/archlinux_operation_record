# gen-gpg-key

初次使用gpg，需要先生成一对gpgkey。但是一看gpg的cli option有200多条，遂找到网络上的参考blog，自行走一遍流程。

## References

1. <https://musigma.blog/2021/05/09/gpg-ssh-ed25519.html>
1. <https://blog.mozcp.com/use-gnupg-agent-as-ssh-agent/>
1. （主要）<https://wiki.archlinux.org/title/GnuPG>
1. <https://ulyc.github.io/2021/01/13/2021%E5%B9%B4-%E7%94%A8%E6%9B%B4%E7%8E%B0%E4%BB%A3%E7%9A%84%E6%96%B9%E6%B3%95%E4%BD%BF%E7%94%A8PGP-%E4%B8%8A/>
1. （主要）<https://blog.mozcp.com/use-gnupg-agent-as-ssh-agent/>
1. （主要）<https://wiki.gentoo.org/wiki/GnuPG>
## Operations

### 先备份和创建配置文件
1. 备份`/etc/skel/`文件夹: `sudo cp -r /etc/skel /etc/skel_240613_backup/`
1. 创建好2个默认文件，位于`/etc/skel/.gnupg`, 这个文件夹:
    ```/etc/skel.gnupg/dirmngr.conf
    # manually create at 2024/06/13, refering to archwiki:https://wiki.archlinux.org/title/GnuPG,
    # section2.2, 2.3.
    # This file is using to configure `dirmngr`,
    # that is a program internally invoked by `gpg` to access
    # PGP keyservers.
    # See <https://www.gnupg.org/documentation/manuals/gnupg/Dirmngr-Options.html>
    # for possible options.
    # See also `/etc/gnupg/dirmngr.conf`.
    ```

    ```/etc/skel/.gnupg/gpg.conf
    # manually create at 2024/06/13, refering to archwiki:https://wiki.archlinux.org/title/GnuPG,
    # section2.2, 2.3.
    # This file is using to
    # For arguments you would like to be the default.

    ## copy from https://raw.githubusercontent.com/drduh/config/master/gpg.conf,
    ## refering to <https://musigma.blog/2021/05/09/gpg-ssh-ed25519.html>:
    ```
1. 把gpg.conf丰富一下内容，复制自<https://raw.githubusercontent.com/drduh/config/master/gpg.conf>。

## 首先生成相应的公私钥
### 生成主密钥：
1. 选择主密钥类型：
    ```bash
    gpg --full-gen-key --expert
    ```
1. 密钥种类：

        >Please select what kind of key you want:
        (1) RSA and RSA (default)
        (2) DSA and Elgamal
        (3) DSA (sign only)
        (4) RSA (sign only)
        (7) DSA (set your own capabilities)
        (8) RSA (set your own capabilities)
        (9) ECC and ECC
        (10) ECC (sign only)
        (11) ECC (set your own capabilities)
        (13) Existing key
        Your selection? 10
        
        (10) ECC (sign only)

1. 选择 EC 曲线:

        >Please select which elliptic curve you want:
        (1) Curve 25519
        (3) NIST P-256
        (4) NIST P-384
        (5) NIST P-521
        (6) Brainpool P-256
        (7) Brainpool P-384
        (8) Brainpool P-512
        (9) secp256k1
        Your selection? 1

        (1) Curve 25519

1. 选择有效期：

        >Please specify how long the key should be valid.
        0 = key does not expire
        = key expires in n days
        w = key expires in n weeks
        m = key expires in n months
        y = key expires in n years
        Key is valid for? (0)

    默认永不过期。
1. 确认有效期：

        >Key does not expire at all
        Is this correct? (y/N)

    y

1. 一些个人信息，根据实际情况来写：

        >GnuPG needs to construct a user ID to identify your key.
        >
        >Real name:
        Email address:
        Comment:

    再次确认个人信息：

        >Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O

    O

1. 之后会提示你创建 passphrase。

        >Please enter the passphrase to protect your new key
    
    安全性强的passphrase要求：
    - 至少8位。
    - 至少有1个数字或特殊字符。
    
    如果输入安全性不强的，它会提示你。
    这时候重新输入，或者就使用之前输入的。

1. 之后会提示，表明创建完成了：

        >We need to generate a lot of random bytes. It is a good idea to perform
        some other action (type on the keyboard, move the mouse, utilize the
        disks) during the prime generation; this gives the random number
        generator a better chance to gain enough entropy.
        gpg: /home/username/.gnupg/trustdb.gpg: trustdb created
        gpg: directory '/home/username/.gnupg/openpgp-revocs.d' created
        gpg: revocation certificate stored as '/home/peter/.gnupg/openpgp-revocs.d/1234ABCD1234ABCD1234ABCD1234ABCD1234ABCD.rev'
        public and secret key created and signed.
        >
        >pub   ed25519/0x1234ABCD1234ABCD 2024-06-13 [SC]
              Key fingerprint = XXXX XXXX XXXX XXXX XXXX  XXXX XXXX XXXX XXXX XXXX
        uid                              Real name (Comment) email@example.com

    创建完成。
1. 完成后，使用 `gpg --list-keys`来查看上面创建的密钥，可以加上参数 `--keyid-format <FORMAT>` 来查看密钥 ID，`<FORMAT>` 可以是 `{none|short|0xshort|long|0xlong}`，后面的步骤会用到。

### 创建 ssh 登录密钥
上面操作创建了主密钥，接下来，我们可以在主密钥下创建用于 ssh 登录的子密钥。
从上面的 gpg --list-keys 列出了刚创建的主钥密的 fingerprint 为 `1234ABCD1234ABCD1234ABCD1234ABCD1234ABCD`，
1. 使用以下命令进入交互模式来添加子密钥：
    `gpg --expert --edit-key 1234ABCD1234ABCD1234ABCD1234ABCD1234ABCD`
1. 执行上面的命令后，将进入交互模式：
    >gpg>

    键入 `addkey`，来创建子密钥。
1. 选择类型：
    >Please select what kind of key you want:
       (3) DSA (sign only)
       (4) RSA (sign only)
       (5) Elgamal (encrypt only)
       (6) RSA (encrypt only)
       (7) DSA (set your own capabilities)
       (8) RSA (set your own capabilities)
      (10) ECC (sign only)
      (11) ECC (set your own capabilities)
      (12) ECC (encrypt only)
      (13) Existing key
      (14) Existing key from card
    Your selection?

    由于我们需要创建的是用于 ssh 登录的密钥，因此这里选择 `11`，来创建具有 `Authenticate` 功能的密钥。
1. 选择子密钥功能：
    >Possible actions for a ECDSA/EdDSA key: Sign Authenticate
    >Current allowed actions: Sign
    >
    >(S) Toggle the sign capability
    >(A) Toggle the authenticate capability
    >(Q) Finished
    >
    >Your selection? S

    这里我们不需要这个密钥用于 `Sign`，因此可以键入 `S` 取消 Sign，然后再键入 A 添加 Authenticate，最后键入 `Q` 进入下一步。
1. 选择子密钥椭圆曲率：
    >Please select which elliptic curve you want:
       (1) Curve 25519 *default*
       (2) Curve 448
       (3) NIST P-256
       (4) NIST P-384
       (5) NIST P-521
       (6) Brainpool P-256
       (7) Brainpool P-384
       (8) Brainpool P-512
       (9) secp256k1
    Your selection?

    (1) Curve 25519

1. 选择有效期：
    >Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)

    0
1. 之后是两次确认：
    >Key does not expire at all
Is this correct? (y/N) y
Really create? (y/N) y

    y y
    然后输入passphrase，就能生成子密钥：
        
        >We need to generate a lot of random bytes. It is a good idea to perform
        some other action (type on the keyboard, move the mouse, utilize the
        disks) during the prime generation; this gives the random number
        generator a better chance to gain enough entropy.

        sec ed25519/0x1234ABCD1234ABCD
            created: 2024-06-13  expires: never       usage: SC
            trust: ultimate      validity: ultimate
        ssb ed25519/0x5678EFGH5678EFGH
            created: 2024-06-13  expires: never       usage: A
        [ultimate] (1). Real name (Comment)email@example.com
1. 输入 `save`保存并退出。
    这时，如果使用 `gpg --list-keys` 命令，
    会看到多了一行：

    >sub   ed25519/0x5678EFGH5678EFGH 2024-06-13 [A]
    
    这就是用于 ssh 的登录的子密钥，其中 `sub` 表示是 subkey，`A` 表示是 Authenticate。
    类似的标志：
    ```
    C: Certify
    S: Sign
    A: Authenticate
    E: Encrypt
    ```
### 后续gpg设置
在生成用于 ssh 登录的密钥后，还不能直接使用，需要先进行一些设置。
1. 首先，为了让ssh使用gpg-agent，需要设置 `SSH_AGENT_PID`和`SSH_AUTH_SOCK` 环境变量（位于`~/.bashrc.d/gpg_and_ssh.bashrc`）。（共2步都是为了让ssh使用gpg-agent。）参考自gentoo wiki。
    
    ```~/bashrc.d/gpg_and_ssh.bashrc
    ## Add at 2024-06-15 13:47
    ## Refer from gentoo wiki:<https://wiki.gentoo.org/wiki/GnuPG#Using_gpg-agent_for_SSH>
    unset SSH_AGENT_PID
    if [ "${gnupg_SSH_AUTH_SOCK_by:-0}" -ne $$ ]; then
       export SSH_AUTH_SOCK="$(gpgconf --list-dirs agent-ssh-socket)"
    fi
    ## End add at 2024-06-15 13:47
    ```

1. 再根据gpg-agent版本大于v2.3.7，参考自gentoo wiki(两种方法都可以)。

    ```
    echo "Use-for-ssh: true" >> ~/.gnupg/private-keys-v1.d/0CA0F1710A5837577FB10177355BE575A425D76D.key
    ```
    
    `gpg-connect-agent 'KEYATTR 0CA0F1710A5837577FB10177355BE575A425D76D Use-for-ssh: true' /bye`
    把这里的key是subkey，换成自己的。
    此时，“让ssh使用gpg-agent”设置完成。

1. 再设置ssh登录时pinentry，如果不设置，则通过 SSH 使用 gpg-agent，登录 shell 中不会出现图形密码提示，这会导致所有需要密码的操作失败。以下代码片段可以添加到 `~/.bash_profile` 中，这告诉 gpg-agent 在当前 shell 中使用 curses 提示符。使用本地 shell 时，该代码段不会影响 pinentry 设置。参考自gentoo wiki。

    ```~/.bash_profile.d/curses_pinentry_for_SSH_logins.bash_profile
    export GPG_TTY=$(tty)
    if [[ -n "$SSH_CONNECTION" ]] ;then
        export PINENTRY_USER_DATA="USE_CURSES=1"
    fi
    ```

1. 导出公钥放到服务器/GitHub上。
    - 先`gpg --list-keys --keyid-format long`找到 ssh 的 {subkey id}，
    - 然后 `gpg --armor --export-ssh-key {subkey id}`。
    **tips**: [参考里](https://blog.mozcp.com/use-gnupg-agent-as-ssh-agent/)写的是 `gpg --armor --export-ssh-key {subkey id}`，这种导出的是ssh格式的pubkey，github不能使用这种导出的格式，换成`gpg --armor --export {subkey id}`，生成的pubkey格式开头为`-----BEGIN PGP PUBLIC KEY BLOCK-----`，结尾为`-----END PGP PUBLIC KEY BLOCK-----`。

1. Github 网页显示成功，则完成。
