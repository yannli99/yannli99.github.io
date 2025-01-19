# SSH的简单配置和使用

## SSH简介

安全外壳协议（Secure Shell Protocol，简称SSH）是一种加密的网络传输协议，SSH通过在网络中简历安全隧道来实现SSH客户端和服务器之间的连接，主要用途是远程登录系统。(详情见 [wiki](https://zh.wikipedia.org/wiki/Secure_Shell) 、[SSH教程](https://wangdoc.com/ssh/))。


### SSH下载

[OpenSSH](https://www.openssh.com/)是现在主要使用的开源SSH软件，Linux系统一般都自带OpenSSH软件，下载OpenSSH命令
```bash
# ubuntu、Debian下载, 正常情况下是自带的
$ sudo apt install openssh-client
# 单独下载ssh客户端，ssh本身和其它工具
$ sudo apt install openssh-client 
# 单独下载ssh服务端，sshd服务
$ sudo apt install openssh-server 
# CentOS 和 Fedora
$ sudo dnf install openssh-clients
```
注意: 正常启动ssh服务是sshd，ubuntu24版本里是ssh，例如重启ssh服务的命令
```bash
# 正常重启ssh命令
$ sudo systemctl restart sshd.service
# ubuntu24重启ssh命令
$ sudo systemctl restart ssh
```
安装后使用`-V`参数输出版本号验证
```bash
$ ssh -V
```
#### SSH下载（temux）
```bash
# 安装openssh
$ pkg install openssh
# 启动ssh
$ sshd
```

## SSH使用

### 基本用法

ssh最常见的用途是登录服务器，这要求服务器安装ssh服务并开发ssh端口（默认22）。

```bash
# hostname是主机名（域名、IP），不指定用户名的情况下，使用客户端的用户名作为远程服务器的登录用户名
$ ssh hostname
# 使用用户名, 使用@和主机名分开
$ ssh user@hostname
# 使用`-l`参数指定用户名
$ ssh -l username host
# 使用`-p`指定端口，默认22
$ ssh -p 2022 user@host
```

### SSH创建公钥和私钥

创建SSH公私钥，`-t`参数指定加密算法（默认rsa或dsa），`-b`指定秘钥的二进制位数，这个数越大破解难度越大，`-C`指定注释，格式为`username@host`，`-f`指定生成的私钥文件，`-N`指定私钥的密码（生成时会提示，但是我没有用过，一般都为空），`-R`参数将指定的主机公钥指纹移除`know_hosts`文件，`-p`参数可以重新制定密码。
```bash
$ ssh-keygen -t rsa -b 4096 -C 'yann.li5947@gmail.com' -f ~/.ssh/id_rsa_yann
```

SSH在没有配置公钥和私钥的时候需要密码登录，不管是密码还是公钥登录第一次连接一台服务器都会生成一个服务器指纹 `fingerprint `，这个指纹在变更时会导致SSH连接失效。

公钥指纹存在`~/.ssh/know_hosts`中。

查看某个公钥的指纹：
```bash
$ ssh-keygen -l -f /etc/ssh/ssh_host_ecdsa_key.pub
256 da:24:43:0b:2e:c1:3f:a1:84:13:92:01:52:b4:84:ff   (ECDSA)
```

### SSH上传公钥到服务器

OpenSSH自带一个`ssh-copy-id`命令，可以将公钥拷贝到服务器的`~/.ssh/authorized_keys`文件。注意不用选择`.put`后缀的公钥文件，直接选择key的名字就行。
```bash
$ ssh-copy-id -i id_rsa_yann user@host
```

#### 手动拷贝
使用ssh命令可以手动拷贝实现公钥上传
```bash
$ cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### SSH连接到服务器

#### 客户端配置

SSH 客户端的全局配置文件是/etc/ssh/ssh_config，用户个人的配置文件在~/.ssh/config，优先级高于全局配置文件。

```bash
Host remoteserver
    HostName remote.host.com
    User yann
    IdentityFile ~/.ssh/id_rsa #指定私钥文件
    Port 2222
# github的ssh配置
Host github.com
    User git
    Hostname ssh.github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_ed25519_yann
    Port 443
```
配置后连接`remoteserver`使用配置去连接
```bash
$ ssh remoteserver
# 等效与
$ ssh -p 2222 yann@remote.host.com
```

测试ssh连接
```bash
$ ssh -T git@github.com
```