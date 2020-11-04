## Shell是什么？

Shell 是计算机提供的命令行接口，为你提供更加强大的计算机能力。

## 使用shell

输入两个指令date、echo如下：

```shell
 ~  date
2020年11月 1日 星期日 09时21分07秒 CST
 ~  echo hello
hello
```

date 打印日期信息

echo 在stdin (当前为终端) 回显输入内容

```shell
 ~  echo $PATH
/Users/feng/bin:/Users/feng/Devolopment/flutter/bin:/Users/feng/.rvm/gems/ruby-2.6.0/bin:/Users/feng/.rvm/gems/ruby-2.6.0@global/bin:/Users/feng/.rvm/rubies/ruby-2.6.0/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Wireshark.app/Contents/MacOS:/Users/feng/.rvm/bin:/Users/feng/.rvm/bin
 ~  which echo
echo: shell built-in command
```

使用echo 打印预定义变量$PATH的值

使用which 查看echo安装目录，这里显示echo是shell内建的命令

## Shell中导航

```shell
 ~  pwd
/Users/feng
 ~  cd .
 ~  pwd
/Users/feng
 ~  cd ..
 /Users  pwd
/Users
```

pwd 显示当前目录

cd . 进入当前目录

cd .. 进入父级目录

```shell
 /Users  ls
Guest  Shared feng
 /Users  cd feng
 ~  ls
Applications       Downloads          Music              Sites
Desktop            Library            Pictures           Webmeeting_Records
Documents          Movies             Public
 ~  ls -l
total 0
drwx------@  6 feng  staff   192  6 21 23:21 Applications
drwx------@ 21 feng  staff   672 10  9 19:56 Desktop
drwx------+ 11 feng  staff   352 11  1 09:24 Documents
drwx------@ 11 feng  staff   352 10 29 22:54 Downloads
drwx------@ 87 feng  staff  2784 10 29 14:20 Library
drwx------+  9 feng  staff   288  6 23 03:36 Movies
drwx------+  8 feng  staff   256  6 23 03:36 Music
drwx------+  9 feng  staff   288  9 25 18:19 Pictures
drwxr-xr-x+  4 feng  staff   128  4  8  2019 Public
drwxrwxrwx   5 root  staff   160  3 20  2020 Sites
drwxr-xr-x   2 feng  staff    64 10 21 07:56 Webmeeting_Records
 ~  ls -l /home
lrwxr-xr-x  1 root  wheel  25  8 27 17:34 /home -> /System/Volumes/Data/home
```

ls 列举当前目录文件

ls -l 列举当前文件，并附上详细信息

drwx------ 第一个d代表文件夹，rwx代表当前用户feng对此文件拥有读写执行权限，接着的三位代表同组用户对此文件没有任何权限，最后三位代表其他用户对该文件没有任何权限



其他常用命令：

`mv`（用于重命名或移动文件）、`cp`（拷贝文件）以及 `mkdir`（新建文件夹）



查询命令手册：man ls

```shell
LS(1)                     BSD General Commands Manual                    LS(1)

NAME
     ls -- list directory contents

SYNOPSIS
     ls [-ABCFGHLOPRSTUW@abcdefghiklmnopqrstuwx1%] [file ...]

DESCRIPTION
     For each operand that names a file of a type other than directory, ls
     displays its name as well as any requested, associated information.  For
     each operand that names a file of type directory, ls displays the names
     of files contained within that directory, as well as any requested, asso-
     ciated information.
     ...
```

## 连接多个命令

命令的输入、输出及中间结果都可以使用流操作进行传递

```shell
// 使用 > file 和 < file 进行重定向
~  echo hello > hello.txt
 ~  cat hello.txt
hello
 ~  cat < hello.txt
hello
 ~  cat < hello.txt > hello2.txt
 ~  cat hello2.txt
hello
```

```shell
 // 使用管道 | 进行重定向
 ~  ls -l / | tail -n1
lrwxr-xr-x@  1 root  admin    11 11  9  2019 var -> private/var
 ~  curl --head --silent google.com | grep --ignore-case content-length | cut -d ' ' -f2
219
```

```
// 使用tee进行文件的重定向，tee可已将内容分别输入到指定文件和stdin(终端)
 ~  cat hello.txt
hello
 ~  echo world | tee -a hello.txt
world
 ~  cat hello.txt
hello
world
```

## 超级用户权限管理

```shell
 ~  echo "#哈哈哈" >> /etc/hosts
zsh: permission denied: /etc/hosts
 ✘  ~  sudo echo "#哈哈哈" >> /etc/hosts
zsh: permission denied: /etc/hosts
 ✘  ~  echo "#哈哈哈" | sudo tee -a /etc/hosts
Password:
#哈哈哈
 ~  cat /etc/hosts
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
127.0.0.1 mmilua.com
127.0.0.1 mkliao.com

#想加快github.com下载速度，没什么作用
#199.232.69.194 github.global.ssl.fastly.net
#114.253.234.54 github.com

#哈哈哈
```

使用sudo进行权限提升, 可以对根目录/etc/hosts文件进行修改







