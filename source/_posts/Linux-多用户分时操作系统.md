---
title: Linux —— 多用户分时操作系统
date: 2018-03-22 09:20:38
categories:
- 运维
tags:
- Linux
- 操作系统
- 权限
---

![文件](https://gitee.com/uploads/images/2018/0322/135330_85d7daad_1623141.png "屏幕截图.png")

# 多用户分时操作系统

## 概念

随着科技的发展，电脑配置越来越高。周围人买电脑，动不动就 1 个 T 的硬盘大小，16 个 G 的内存。实际使用只会用到其中一部分，比如我的硬盘就是 1 个 T，现在还有 600 G 的空余，这部分资源在我这就浪费了。我时常在想，如果能将我空余的资源分享给其它人，再收取廉价的费用，岂不是一举两得。

Linux 在几十年前就考虑到了这一点，可以让多个人同时使用一台电脑（并不是实体机器，而是同一台电脑的资源），并且不能互相窥探对方的秘密。要做到这个，有两个问题需要解决。

- 怎么让多个人使用一台电脑？多用户。
- 怎么让多个人不能互相窥探对方的秘密？权限。

<!-- more -->

**多用户**

Linux 操作系统安装完成之后默认会有一个用户：`root 用户`。

Linux 系统里的所有资源你都可以认为是文件，root 用户也可以说是管理员用户，拥有查看、修改、新增所有文件的权限。

root 用户可以创建普通用户，每个普通用户拥有一个自己的目录：/home/用户名，称为 `用户目录`。普通用户在自己的目录里可以随意的查看、修改、新增文件。除此之外，对于其它文件的权限受到限制，如不能查看或不能修改。如果想要查看除 `用户目录` 之外的其它文件，需要 root 用户给权限。

这就解决了不同用户同时使用一台电脑的问题。使用普通用户的用户名和密码登录系统，然后获取 `用户目录`，在这个目录里操作，就好像我们拥有这整台计算机一样。

**权限**

使用普通用户的用户名和密码登录进来之后，为确保各自的隐私性，需要有一定的权限设置。

前面有说到 Linux 系统里的资源就是文件，文件的权限无非有三点：能不能看（读），能不能写，能不能执行。

有两种方式可以处理权限问题：

- 一种是对于 A 普通用户，把它可以访问的文件记录起来。如果文件数量很多，就得记录很多条数据。

- 另一种方式是针对文件，在每个文件里记录两条数据：创建它的用户拥有操作该文件的权限、其它用户拥有操作该文件的权限。

有一种情况比较特殊，多个普通用户对同一个文件有相同的权限，怎么办？

Linux 提出了用户组的概念，将对同一个文件有相同权限的用户都划分为一个用户组，然后设置用户组对该文件的权限即可。

如此一来，每个文件中记录三条信息来说明权限问题：

- 创建该文件的用户拥有对该文件的操作权限。
- 创建该文件的用户所属用户组对该文件的操作权限。
- 其它用户对该文件的操作权限。

# 用户和组

## 查看用户

**/etc/passwd**：存放用户的文件。

查看该文件即可知道当前系统有哪些用户。大多数用户都是系统用户，最后几行是我们自己新建的用户。

```
# cat /etc/passwd

root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:997:User for polkitd:/:/sbin/nologin
libstoragemgmt:x:998:996:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:997:995::/var/lib/chrony:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
syslog:x:1000:1000::/home/syslog:/bin/bash
centos:x:1001:1001:Cloud User:/home/centos:/bin/bash
nginx:x:996:994:Nginx web server:/var/lib/nginx:/sbin/nologin
dk:x:1002:1002::/home/dk:/bin/bash
dockerroot:x:995:992:Docker User:/var/lib/docker:/sbin/nologin
dkvirus:x:1003:1003::/home/dkvirus:/bin/bash
bob:x:1004:1004::/home/bob:/bin/bash
```

每一行表示一个用户，每个用户都有若干部分组成，分别用 `:` 隔开，关于每部分具体含义，见下图。

![用户文件说明](https://gitee.com/uploads/images/2018/0322/092642_a8256cc2_1623141.png "屏幕截图.png")

## 查看用户组

**/etc/group**：存放用户组的文件。

查看用户组与查看用户类似，不再赘述。每个用户组信息具体含义，见下图。

![用户组说明](https://gitee.com/uploads/images/2018/0322/092811_3e734941_1623141.png "屏幕截图.png")

## 查看密码

**/etc/shadow**：存放密码的文件。

虽然这个文件存放用户密码，但实际查看并看不到明文密码是啥。权当知道有这么个东东在这吧。

对于用户和用户组，我们希望可以对它们进行增删改查的操作。关于查看用户/用户组，上面已经赘述。

## 管理用户

- `useradd dkvirus` 新增 dkvirus 用户。新增的用户还没有密码哦，使用 `passwd dkvirus` 设置密码。 
- `usermod` 修改用户属性。
- `userdel dkvirus` 删除 dkvirus 用户。

## 管理用户组

管理用户组和管理用户类似，不再赘述。

- groupadd
- groupmod
- groupdel

# 常见关于用户的操作

## 创建用户

只有 root 用户能创建其它用户，请切换到 root 用户。

```
# useradd dkvirus
```

新建的用户密码是空的，还需要手动给它修改一下密码。

passwd 后面接要修改密码的用户，如果 passwd 后面不接参数，表示修改当前登录用户的密码。

```
# passwd dkvirus
```

## 切换用户 su

当前在 root 用户，想要切换到 dkvirus 用户。因为 root 用户是所有用户的老大，它切换到其它用户不需要输入密码。

```
# su dkvirus
```

当前是 dkvirus 用户，想要切换到 root。还需要输入 root 用户的密码。

为什么需要切换 root 用户呢？linux 中不同的用户拥有不同的权限，root 用户可以在所有的文件夹里增删改查，但是 dkvirus 普通用户只能在 /home/dkvirus 目录里增删改查，如果 dkvirus 用户需要在其它目录如 /etc 里新增一个文件，是不会成功的，因为它没有权限。此时切换到最牛逼的用户 root 就可以了。

```
$ su root
```

## 假借权限 sudo

现在切换到了 dkvirus 用户，想要在创建一个新用户。终端会打印下面内容，意思是你个普通用户，没权限去创建。

```
$ bash: /usr/sbin/useradd: Permission denied
```

root 用户是所有用户的老大，只有它才能创建用户。前面介绍的 su 可以切换普通用户和 root 用户，还有种方法不用切换到 root 用户也可以用它的权利，这种狐假虎威的方式叫 sudo 指令。记住，现在是在 dkvirus 用户下进行操作，前面加上 sudo 指令就可以使用 root 用户才有的权限，比如创建用户。

```
$ sudo useradd bob

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.
```

如果看到上面输出，不要意外，意思是 dkvirus 普通用户没有 sudo 权限。root 用户就像古代的帝王一样，对权力掌控欲望及其强烈。新建的普通用户是没有 sudo 权限的，你得去跟 root 用户申请：“大佬，每次要新增用户都来找你很麻烦，拜托拜托，给我个 sudo 权限，以后就不用再来麻烦你了”。root 用户被你感动了，于是给你添加了 sudo 权限。现在切换到了 root 用户。

编辑 sudoers 文件

```
vi /etc/sudoers
```

`Ctrl + D` 往下翻页，找到下面这部分内容，在 `root	ALL=(ALL) 	ALL` 下面添加 `dkvirus	ALL=(ALL) 	ALL`，之后保存即可。

如果你不熟悉 vi 编辑器，我建议你先去练习一下 vi 基本操作。

```
## Allow root to run any commands anywhere 
root	ALL=(ALL) 	ALL

## Allows members of the 'sys' group to run networking, software, 
## service management apps and more.
# %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS
```

如果你发现 root 用户也没有写权限，给 root 用户添加上写的权限：`# chmod u+w sudoers`。关于文件权限更多操作，看下面。

# 文件权限

## 查看目录子文件 ll

查看 /etc 目录下面的子文件：

```
# cd /etc
# ll
drwxr-xr-x.  2 root root     4096 Aug  5  2017 tmpfiles.d
-rw-r--r--.  1 root root      750 Aug  4  2017 trusted-key.key
drwxr-xr-x.  2 root root     4096 Jan  9 18:22 tuned
```

可以看到一串东东，这么直接看可能不明白，但是可以发现每一行都有固定结构，含义如下图。

![文件属性展现结构](https://gitee.com/uploads/images/2018/0322/104919_34ea743c_1623141.png "屏幕截图.png")

其它的不用看，都是些文件名，创建日期啥的，我们关注最前面的部分 **drwxr-xr-x**：

- 第一个字符为 **d** 表示这是目录，为 **-** 表示这是文件。
- **rwxr-xr-x** 这部分以三个字符为单位，可以拆分为三部分：**rwx** 为 **当前登录用户** 拥有的文件权限，、**r-x** 为 **当前登录用户所属用户组** 拥有的文件权限、**r-x** 为 **其它用户** 拥有的文件权限。具体如下图所示：

![文件类型和权限](https://gitee.com/uploads/images/2018/0322/105335_70fff8ed_1623141.png "屏幕截图.png")

## 文件的权限

文件/目录都有三个基本权限。

- **r** 读权限。目录没读权限，使用 ll 查看子文件之后做不了任何事（如删除子文件）；文件没有读权限，使用 vi 就不能编辑文件。
- **w** 写权限。目录没写权限，就无法创建子文件；文件没有写权限，就只能看不能修改和保存。
- **x** 执行权限。目录没执行权限，使用 ll 也无法查看子目录。

每个文件/目录都有上面三个权限随机组合。下面要做的就是能随意修改文件/目录的权限。

## 修改文件权限 chmod

对一个文件的权限进行操作。

### 数字法

rwx 分别对应一个数字。**r=4 w=2 x=1**。

```
# chmod 775 filename
```

- 第一个数字 7 表示 **当前登录用户** 对该文件拥有的权限。7 表示拥有 rwx 三个权限。
- 第二个数字 7 表示 **当前登录用户所属组** 对该文件拥有的权限。7 表示拥有 rwx 三个权限。
- 第三个数字 5 表示 **其它用户** 对该文件拥有的权限。5 表示拥有 r 和 x 两个权限，没有 w 写权限。

### 字母法

- **当前登录用户** 用 `u` 表示
- **当前登录用户所属组** 用 `g` 表示
- **其它用户** 用 `o` 表示
- **全部用户** 用 `a` 表示

1）其它用户，文件 filename 移除写权限 w

```
# chmod o-w filename
```

2）当前登录用户，文件 filename 设置 rx 权限

```
# chmod u=rx filename
```

3）所有用户都加上写权限 w

```
# chmod a+w filename
```

## 修改文件所属用户/用户组 chown 

适用场景：

下面是 mzadmin 目录下的子文件，每个文件的都是属于 mzadmin 用户的，mzadmin 用户（rw-rw-r-- 前三个字符 rw-）拥有 rw 两个权限，而其它用户（rw-rw-r-- 最后三个字符 r--）只有 r 权限。

现在需要将这个目录全部复制到 /home/dkvirus 目录下，dkvirus 用户对这些文件只有 r 权限，但他也想有 w 权限。

其中一种方式是依次修改每个文件的权限：`$ chmod o+w filename`，我相信没人会这么做。

另一种方式是改变这些文件所属用户即可。让文件属性变成这样子：`-rw-rw-r-- 1 dkvirus dkvirus 1410103 Mar 19 09:14 0.11261133.async.js`，这样 dkvirus 用户就变成了这些文件的所属用户，自动就拥有了 rw（rw-rw-r-- 前三个字符 rw-）权限。

```
total 5264
-rw-rw-r-- 1 mzadmin mzadmin 1410103 Mar 19 09:14 0.11261133.async.js
-rw-rw-r-- 1 mzadmin mzadmin    2550 Mar 19 09:14 10.a02f4f4e.async.js
-rw-rw-r-- 1 mzadmin mzadmin    2192 Mar 19 09:14 11.fac2e172.async.js
-rw-rw-r-- 1 mzadmin mzadmin    3095 Mar 19 09:14 12.621caa91.async.js
-rw-rw-r-- 1 mzadmin mzadmin 1410225 Mar 19 09:14 1.2ad951aa.async.js
-rw-rw-r-- 1 mzadmin mzadmin    3772 Mar 19 09:14 13.fcf1bc79.async.js
-rw-rw-r-- 1 mzadmin mzadmin    2831 Mar 19 09:14 14.963d5254.async.js
-rw-rw-r-- 1 mzadmin mzadmin    9650 Mar 19 09:14 15.18ca14fc.async.js
-rw-rw-r-- 1 mzadmin mzadmin    4663 Mar 19 09:14 16.5e76c7dd.async.js
-rw-rw-r-- 1 mzadmin mzadmin   10863 Mar 19 09:14 17.815c1f42.async.js
-rw-rw-r-- 1 mzadmin mzadmin    6654 Mar 19 09:14 18.40edd1d9.async.js
-rw-rw-r-- 1 mzadmin mzadmin    7647 Mar 19 09:14 19.28f5e8ba.async.js
-rw-rw-r-- 1 mzadmin mzadmin    4752 Mar 19 09:14 20.cfa5a926.async.js
-rw-rw-r-- 1 mzadmin mzadmin    9707 Mar 19 09:14 21.693a3087.async.js
-rw-rw-r-- 1 mzadmin mzadmin    8007 Mar 19 09:14 22.462c198a.async.js
-rw-rw-r-- 1 mzadmin mzadmin    5923 Mar 19 09:14 23.166cd9e3.async.js
-rw-rw-r-- 1 mzadmin mzadmin    3366 Mar 19 09:14 24.bca11da2.async.js
-rw-rw-r-- 1 mzadmin mzadmin    9578 Mar 19 09:14 25.3d8f827c.async.js
-rw-rw-r-- 1 mzadmin mzadmin   61639 Mar 19 09:14 2.5f2d7078.async.js
-rw-rw-r-- 1 mzadmin mzadmin    6959 Mar 19 09:14 26.576de80d.async.js
-rw-rw-r-- 1 mzadmin mzadmin    6560 Mar 19 09:14 27.94c1e1bb.async.js
-rw-rw-r-- 1 mzadmin mzadmin    1911 Mar 19 09:14 28.b172d0ff.async.js
```

修改文件所属用户/用户组方法：

```
# chown dkvirus:dkvirus filename
```

如果要修改一个目录下所有文件的用户/用户组，可以这么写：

```
# chown -R dkvirus:dkvirus *
```

其中 `-R` 表示递归修改，`*` 表示所有的，你就不需要一个个写文件名那么麻烦了。




