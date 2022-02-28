[TOC]

# CentOS基础文档

## 一：RedHat与CentOS简介

### 1.1：RedHat简介

### 1.2：CentOS简介

### 1.3：CentOS镜像下载

## 二：CentOS安装及使用

### 2.1：CentOS安装

### 2.2：CentOS系统基础配置

#### 2.2.1：更改主机名

```shell
hostnamectl set-hostname centos01
```

#### 2.2.2：更改网卡名称为eth*

如果没有在安装系统之前传递内核参数将⽹卡名称更改为eth*，则可以在安装系统之后修改/etc/default/grub ：

```shell
vim /etc/default/grub 
GRUB_DEFAULT=0 
GRUB_TIMEOUT_STYLE=hidden 
GRUB_TIMEOUT=2 
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian` 
GRUB_CMDLINE_LINUX_DEFAULT=""
GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"

或者使用sed命令
sed -i  's/^GRUB_CMDLINE_LINUX=""$/GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"/g'  /etc/default/grub
```

```shell
#区别于ubuntu的update-grub， centos 使用 grub2-mkconfig
grub2-mkconfig -o /boot/grub2/grub.cfg
reboot

#如果Centos启动项丢失，使用如下命令来安装grub2到磁盘启动区
grub2-install /dev/sdx
```

#### 2.2.3：CentOS 7网络配置

#### 2.2.4：CentOS 8网络配置

#### 2.2.5：最小化安装centos安装基础命令

```shell
yum install vim iotop bc gcc gcc-c++ glibc glibc-devel pcre pcre-devel openssl \
openssl-devel zip unzip zlib-devel net-tools lrzsz tree chrony telnet lsof \
tcpdump wget libevent libevent-devel systemd-devel bash-completion traceroute -y

```

#### 2.2.6：minimal安装桌面

```shell
#安装
yum groups install "Gnome Desktop" -y
#启动桌面
startx
#设置默认命令行界面
systemctl set-default multi-user.target
#设置默认图形界面
systemctl set-default multi-user.target
```



### 2.3：CentOS软件包管理

#### 2.3.1：可选软件仓库

#### 2.3.2：yum

#### 2.3.3：rpm包管理

#### 2.3.4：安装tomcat

https://blog.csdn.net/github_38336924/article/details/82253553

