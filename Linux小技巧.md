# Linux小技巧

[toc]

## 一：SHELL

### 1.1：快速清空文件

```shell
#最简单，使用shell重定向null到文件来清空
> filename 
#/dev/null设备是一个特殊设备，会吞噬任何给它的输入内容。仍然是基于shell重定向
cat /dev/null > filename 
#使用dd命令，if指的输入，of指的输出
dd if=/dev/null of=filename 
```

### 1.2：! 快速执行相关命令

```shell
#比如之前执行了一条 ssh user@host 命令，然后又执行了其他若干命令，此时又想再次使用 ssh user@host，使用如下命令
$ !ssh

#如果想执行当前登陆操作命令的最后一条，可使用
$ !!

# !$ 表示最近一次命令的最后一个参数
$ vim /root/sniffer/src/main.c
$ mv !$ !$.bak
# 相当于
$ mv /root/sniffer/src/main.c /root/sniffer/src/main.c.bak
```

### 1.3：CTRL+R 

```shell
#快速查找相关命令
#有些时候，执行了命令，只记得相关部分参数。使用 Ctrl+R 快速查找该命令，然后按 Enter执行。
ssh uer@centos
#……若干其他命令
#使用 Ctrl+R 查找 centos 或者 ssh 等能唯一定位的内容，快速查找该命令
(reverse-i-search)`centos': ssh root@centos
```

### 1.4：使用push，popd，cd-，cd~在目录中移动

### 1.5：计算程序运行时间

```shell
#一些程序，我们希望能知道它的运行时间。利用time命令帮我们计算，例如：
$ time ./fobar 30
the 30 result is 832040
real 0m0.088s
user 0m0.084s
sys 0m0.004s
```

`real`、`user`  和  `sys`  三个参数的含义

- `real`：表示的钟表时间，也就是从程序执行到结束花费的时间；
- `user`：表示运行期间，cpu 在用户空间所消耗的时间；
- `sys`：表示运行期间，cpu 在内核空间所消耗的时间；

由于 `user` 和 `sys` 只统计 cpu 消耗的时间，程序运行期间会调用 sleep 发生阻塞，也可能会等待网络或磁盘 IO，都会消耗大量时间。因此对于类似情况，<u>`real` 的值就会大于其它两项之和</u>。

如果程序在多个 cpu 上并行，那么 `user` 和 `sys` 统计时间是多个 cpu 时间，实际消耗时间 `real` 很可能就比其它两个之和要小了。

### 1.6：删除乱码文件

Linux 系统中，会经常碰到名称乱码的文件。想要删除它，却无法通过键盘输入名字，有时候复制粘贴乱码名称，终端可能识别不了，该怎么办？

```shell
# -i 显示 inode 号
$ ls  -i
138957 a.txt  138959 T.txt  132395 ڹ��.txt

# -inum 指定文件 inode 号
$ find . -inum 132395 -exec rm {} \;
```

### 1.7：查找ASCII码

```shell
$ man ascii
```

### 1.8：动态实时查看日志

如果想在日志中出现 `Failed` 等信息时立刻停止 tail 监控，可以通过如下命令来实现：

```shell
$ tail -f test.log | sed '/Failed/ q'
```

### 1.9：查看某个进程的特定字段

```shell
#通过 etime 获取该进程的运行时间，可以很直观地看到，进程运行了 13 个小时 38分钟
$ps -p 5293 -o etimes, etime
ELAPSED     ELAPSED
  49098    13:38:18

关于字段名称格式，可以通过 man ps 查看，大概在601行，或者 /^STANDARD FORMAT SPECIFIERS$ 快速查找。
```

### 1.10：快速生成大文件、制作系统盘、安全擦除硬盘数据

```shell
# 生成一个 1G 的文件
$ dd if=/dev/zero of=file.img bs=1M count=1024

# 使用 /dev/urandom 生成随机数据，将生成的数据写入 sda 硬盘中，相当于安全的擦除了硬盘数据。
$ dd if=/dev/urandom of=/dev/sda

# sdb 可以是 U 盘，也可以是普通硬盘
$ dd if=ubuntu-server-amd64.iso if=/dev/sdb

```

### 1.11: 命令不保存进history

有时候，执行的命令中包含敏感信息。在所要执行的命令前，加一个**空格**，那这条命令就不会被 `history` 保存到历史记录。



## 二：Vim

### 2.1：打开文件的同时快速定位到某行

```shell
vim filename +行号
例如，使用了grep 查找指定字符串在文件中的行数，然后使用vim打开文件并快速定位到第7行
grep -n SELINUX /etc/selinux/config
3:# SELINUX= can take one of these three values:
7:SELINUX=enforcing
8:# SELINUXTYPE= can take one of three values:
12:SELINUXTYPE=targeted

vim /etc/selinux/config +7
```

