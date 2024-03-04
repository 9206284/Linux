[TOC]

# CentOS基础文档

## 一：RedHat与CentOS简介

### 1.1：RedHat简介

### 1.2：CentOS简介

### 1.3：CentOS镜像下载

```http
#中科大镜像 7.9
http://mirrors.ustc.edu.cn/centos/7.9.2009/isos/x86_64/
#中科大镜像 8-stream
http://mirrors.ustc.edu.cn/centos/8-stream/isos/x86_64/
```

## 二：CentOS安装及使用

### 2.1：CentOS安装

### 2.2：CentOS系统基础配置

#### 2.2.1：更改主机名 ☑️

```shell
hostnamectl set-hostname centos01
```

#### 2.2.2：更改网卡名称为eth* ☑️

1. 可在安装系统之后修改/etc/default/grub ，或在安装系统之前传递内核参数更改⽹卡名称为eth*

   增加参数的作用是禁用操作系统基于硬件生成网卡名称的规则

   ```shell
   vim /etc/default/grub 
   GRUB_DEFAULT=0 
   GRUB_TIMEOUT_STYLE=hidden 
   GRUB_TIMEOUT=2 
   GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian` 
   GRUB_CMDLINE_LINUX_DEFAULT=""
   GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"
   
   # 或者直接使用sed命令操作文件内容替换
   sed -i  's/^GRUB_CMDLINE_LINUX=""$/GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"/g'  /etc/default/grub
   ```

2. 修改/etc/sysconfig/network-scripts下的网卡配置文件

   ```shell
   cd /etc/sysconfig/network-scripts/
   mv ifcfg-ens160 ifcfg-eth0
   vim ifcfg-eth0
   
   ```

   编辑ifcfg-eth0文件内容，注意DEVICE、NAME两参数的值

   ```ini
   TYPE=Ethernet
   PROXY_METHOD=none
   BROWSER_ONLY=no
   DEFROUTE=yes
   IPV4_FAILURE_FATAL=no
   IPV6INIT=no
   IPV6_AUTOCONF=yes
   IPV6_DEFROUTE=yes
   IPV6_FAILURE_FATAL=no
   IPV6_ADDR_GEN_MODE=stable-privacy
   NAME=eth0
   DEVICE=eth0   
   ONBOOT=yes
   DNS1=192.168.205.160
   DNS2=114.114.114.114
   UUID=5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03
   BOOTPROTO=none
   IPADDR=192.168.229.16
   PREFIX=24
   GATEWAY=192.168.229.1
   ```

3. 根据操作系统的引导方式不同使用不同命令生成启动文件

   ```shell
   # 判断操作系统是基于UEFI模式引导还是基于BIOS引导。
   [ -d /sys/firmware/efi ] && echo UEFI || echo BIOS
   
   #区别于ubuntu的update-grub， centos 使用 grub2-mkconfig
   #基于UEFI模式引导的系统
   grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
   #基于BIOS模式引导的系统
   grub2-mkconfig -o /boot/grub2/grub.cfg
   
   #如果Centos启动项丢失，使用如下命令来安装grub2到磁盘启动区
   grub2-install /dev/sdx
   ```

4. 重启服务器，使用`ip a` 命令查看网卡名称

5. 临时修改网卡名称

   ```shell
   # 实际验证此操作不影响ssh连接
   # 将旧网卡关闭 && 临时更改网卡名称，服务器重启后网卡名称会还原 && 将新网卡打开
   ip link set ens160 down && ip link set ens160 name eth0 && ip link set eth0 up
   
   ```
   
   

#### 2.2.3：CentOS 7网络配置

##### 2.2.3.1 手工修改配置文件

vim /etc/sysconfig/network-script/ifcfg-eth0

```ini
#静态地址
TYPE="Ethernet"
PROXY_METHOD="none"
BOOTPROTO="static"
IPADDR="172.168.223.148"
NETMASK="255.255.255.0"
GATEWAY="172.168.223.1"
DNS1=61.177.7.1
DNS2=114.114.114.114
IPV4_FAILURE_FATAL="no"
NAME="eth0"
DEVICE="eth0"
ONBOOT="yes
```

##### 2.2.3.2 使用Networkmanager工具✅

NetworkManager 作为 RHEL7开始至RHEL9通用的网络管理工具，推荐使用。

https://developer-old.gnome.org/NetworkManager/stable/nmcli.html

```shell
man nmcli-examples  #查看man帮助手册

nmcli --help
Usage: nmcli [OPTIONS] OBJECT { COMMAND | help }

OPTIONS
  -a, --ask                                ask for missing parameters
  -c, --colors auto|yes|no                 whether to use colors in output
  -e, --escape yes|no                      escape columns separators in values
  -f, --fields <field,...>|all|common      specify fields to output
  -g, --get-values <field,...>|all|common  shortcut for -m tabular -t -f
  -h, --help                               print this help
  -m, --mode tabular|multiline             output mode
  -o, --overview                           overview mode
  -p, --pretty                             pretty output
  -s, --show-secrets                       allow displaying passwords
  -t, --terse                              terse output
  -v, --version                            show program version
  -w, --wait <seconds>                     set timeout waiting for finishing operations

OBJECT
  g[eneral]       NetworkManager's general status and operations
  n[etworking]    overall networking control
  r[adio]         NetworkManager radio switches
  c[onnection]    NetworkManager's connections
  d[evice]        devices managed by NetworkManager
  a[gent]         NetworkManager secret agent or polkit agent
  m[onitor]       monitor NetworkManager changes
```

```shell
nmcli device disconnect dev名称  #关闭网络设备，并暂时的停止自动连接
nmcli device connect    dev名称  #启动网络设备
nmcli networking off             #关闭所有的可管理接口
nmcli connection modify id       #修改连接

nmcli device status              #列出所有设备，dev的默认 command
nmcli connection show            #列出所有连接

#1. 添加一个网络连接的名称，指定网络接口设备名称，并配置静态IP
#
nmcli connection add con-name lei type ethernet ifname eth0 ipv4.addresses  172.168.229.144/24

#2. 配置一个网络连接的名称，指定网络接口设备名称，并动态分配ip
nmcli connection add con-name lei type ethernet ifname eth0 autoconnect yes

#3. 更改网络连接使用
nmcli connection up ID名  #激活该连接
nmcli connection down ID名  #取消该连接，如果是自动连接的网络则会重新连接

#4. 将网络连接改为动态网络
nmcli connection modify lei ipv4.method auto
#5. 将网络连接地址改为静态地址
nmcli connection modify lei ipv4.method manual ipv4.addresses 172.168.229.144 

#6. 删除网络
nmcli connection delete lei

#8. 停止接口
nmcli con down lei

#9. 添加或删除静态路由 ✅
# 添加
nmcli con mod eth0 +ipv4.routes "192.168.1.0/24 192.168.229.5"      
#添加的路由内容会写入/etc/sysconfig/network-scripts/route-<对应网卡名称>文件中。格式如下
#ADDRESS0=192.168.122.0
#NETMASK0=255.255.255.0
#GATEWAY0=192.168.229.1
# 删除
nmcli con mod eth0 -ipv4.routes "192.168.1.0/24 192.168.229.5" 
# 立即生效：
nmcli con up eth0
```

##### 2.2.3.3 使网卡自动协商千兆

```shell
vim /etc/sysconfig/network-scripts/ifcfg-eth0
#追加内容，声明环境变量ETHTOOL_OPTS
ETHTOOL_OPTS="speed 1000 duplex full autoneg on"

# speed 1000  将网络接口速度设置为 1000 Mbps（或 1 Gbps）。
# duplex full 将网络接口的双工模式设置为全双工，这允许同时进行双向通信。
# autoneg on  启用网络接口的自动协商，使其能够自动协商与连接设备的最佳设置。
```

##### 2.2.3.4 配置永久路由 ✅

方法1

```shell
vim /etc/sysconfig/network-scripts/route-eth0    #对应网卡名称
192.168.205.60/32 via 192.168.207.1 dev eth0
10.10.0.0/16 via 192.168.207.1 dev eth0
# 保存reboot
# route -n 测试ok
```

方法2

```shell
# 追加路由命令至 /etc/rc.d/rc.local 启动脚本中
ip route add -host 192.168.205.60/32 gw 192.168.207.1 dev eth0
# 授予可执行权限
chmod +x /etc/rc.d/rc.local

# 保存reboot
# route -n 测试ok
```



#### 2.2.4：CentOS 8网络配置

#### 2.2.5：最小化安装centos的常用软件 ☑️

```shell
yum install vim iotop bc gcc gcc-c++ glibc glibc-devel pcre pcre-devel openssl \
openssl-devel zip unzip zlib-devel net-tools lrzsz tree chrony ntpdate telnet lsof \
tcpdump wget libevent libevent-devel systemd-devel bash-completion traceroute tmux -y

```

选择安装

```shell
yum group install "Development Tools"
g++                          gcc-c++        #rhel
libgmp-dev      #debian      gmp-devel      #rhel
libglib2.0-dev  #debian      glib2-devel    #rhel
libssl-dev      #debian      openssl-devel  #rhel
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
systemctl set-default graphical.target
```

#### 2.2.7：修改时区、24小时、时间同步 ☑️

```shell
###修改时区，24小时，两种方法
# 方法1:
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 方法2:
使用命令tzselect，设置5->9->1->1->OK

###时间同步，使用计划任务
# 方法1直接添加root的计划任务
tail -n1 /var/spool/cron/root
echo '*/5 * * * * /usr/sbin/ntpdate time1.aliyun.com &> /dev/null && /usr/sbin/hwclock -w &> /dev/null' >> /var/spool/cron/root

echo '*/5 * * * * /usr/sbin/ntpdate 192.168.205.60 &> /dev/null && /usr/sbin/hwclock -w &> /dev/null' >> /var/spool/cron/root
tail -n2 /var/spool/cron/root
```

#### 2.2.8：设置HISTORY记录时间戳并清空日志 ☑️

```shell
#HISTORY记录加时间戳
echo 'export HISTFILESIZE=10000' >> /etc/profile
echo 'export HISTSIZE=10000'     >> /etc/profile
echo 'export HISTTIMEFORMAT="%F %T  "' >> /etc/profile
#echo 'export HISTTIMEFORMAT="%Y-%m-%d %H:%M:%S "' >> /etc/profile


#清空日志
 > /var/log/btmp
 > /var/log/wtmp
 > /var/log/lastlog
 > /var/log/messages
 > /var/log/secure 
 > /var/log/yum.log
 > /root/.bash_history
 history -c
```

#### 2.2.9：系统内核优化

数据库、应用操作系统优化：10c 40g  100G / 500G /data

/etc/sysctl.conf

```ini
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_keepalive_time = 30
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_sack = 0
net.ipv4.tcp_timestamps = 0
net.ipv4.tcp_keepalive_probes = 2
net.ipv4.tcp_keepalive_intvl = 15
net.core.rmem_max = 8388608
net.core.wmem_max = 8388608
vm.swappiness = 0
net.ipv4.tcp_rmem = 8192 4194304 8388608
net.ipv4.tcp_wmem = 4096 2097152 8388608
kernel.sem = 250 32000 32 10240
```

nginx操作系统优化：8c 8g     100G /  500G /data

/etc/sysctl.conf

```ini
kernel.core_uses_pid = 1
net.ipv4.tcp_syncookies = 1

kernel.msgmnb = 65536
kernel.msgmax = 65536
#kernel.shmmax 这个参数代表共享内存段的最大大小。
#kernel.shmall 这个参数与 kernel.shmmax 和系统的页大小（通常是 4KB）相关。你需要确保 kernel.shmmax * kernel.shmall 不超过系统的物理内存大小。
kernel.shmmax = 34359738368
kernel.shmall = 4294967296

#分别定义了控制 ARP（Address Resolution Protocol）缓存回收的阈值参数，最小保持时间、中间保持时间、最大保持时间，默认分别是128、512、1024，单位是秒。
net.ipv4.neigh.default.gc_thresh1 = 2048
net.ipv4.neigh.default.gc_thresh2 = 4096
net.ipv4.neigh.default.gc_thresh3 = 8192
# tcp_max_tw_buckets用于控制 TIME_WAIT 状态的 TCP 连接数量上限。TIME_WAIT 是 TCP 连接的一种状态，当一条 TCP 连接关闭时，它会进入 TIME_WAIT 状态，以确保任何延迟的数据包在网络中被丢弃，从而避免对后续连接的影响。参数指定了系统中允许的最大 TIME_WAIT 状态的 TCP 连接数量。当系统中的 TIME_WAIT 连接数量达到此限制时，新的 TIME_WAIT 连接将被直接丢弃，而不再等待处理。默认值 262144.
net.ipv4.tcp_max_tw_buckets = 300000
# net.ipv4.neigh.default.gc_interval 定义了  ARP 缓存项的垃圾回收间隔时间，单位是秒，默认是30
net.ipv4.neigh.default.gc_interval = 3600
# net.ipv4.neigh.default.gc_stale_time 定义了 ARP 缓存项的过期时间（Stale Time），单位是秒，默认是60
net.ipv4.neigh.default.gc_stale_time = 3600
# fs.file-max设置整个系统允许打开的文件句柄（file handle）的最大数量限制
# fs.file-max = 1000000
```

```shell
ipcs -l
---------- 消息限制 -----------  # Mssages Limits
系统最大队列数量 = 32000          # max queues system wide —— MSGMNI ，保持
最大消息尺寸 (字节) = 8192        # max size of message (bytes) —— MSGMAX，调整为65536个元组，即64KB
默认的队列最大尺寸 (字节) = 16384  # default max size of queue (bytes) —— MSGMNB，调整为65536个元组，即64KB

---------- 同享内存限制 ------------       # Shared Memory Limits
最大段数 = 4096                            # max number of segments —— SHMMNI
最大段大小 (千字节) = 18014398509465599     # max seg size (kbytes) —— SHMMAX，调整为68719476736千字节，65536MB即64GB。
最大总共享内存 (千字节) = 18014398442373116  # max totaol share memory (kbytes) —— SHMALL，调整为4294967296千字节，4096MB即4GB。
最小段大小 (字节) = 1                       # min seg size (bytes)

--------- 信号量限制 ----------- # Semaphore Limits
最大数组数量 = 128               # max number of arrays 
每个数组的最大信号量数目 = 250     # max semaphores per array
系统最大信号量数 = 32000          # max semaphores system wide
每次信号量调用最大操作数 = 32      # max ops per semop call
信号量最大值 = 32767             # semaphore max value 
```



```shell
sed -i '$a net.ipv4.tcp_syncookies = 1 \' /etc/sysctl.conf
# net.ipv4.tcp_syncookies = 1 用于启用 TCP syncookie 机制。 TCP syncookie 是一种用于防范 SYN 攻击（SYN flood attack）的技术。在 SYN 攻击中，攻击者发送大量的伪造的 SYN 请求，占用服务器资源，导致服务不可用。为了缓解这种攻击，Linux 内核引入了 syncookie 机制。当启用 net.ipv4.tcp_syncookies = 1 后，Linux 内核会在接收到 SYN 请求时，不再立即分配资源创建半开连接（half-open connections），而是根据这些请求的 IP 地址、端口号和随机生成的 Cookie 值计算出一个 SYN+ACK 响应，该响应包含了生成的 Cookie 信息。只有当客户端回复的 ACK 请求中包含正确的 Cookie 信息时，才会真正创建连接并分配资源。这种方式可以在遭受 SYN 攻击时，避免资源被占用，因为服务器不会为每个 SYN 请求都分配资源，只有合法的请求才会被处理。启用 net.ipv4.tcp_syncookies = 1 可以增强服务器的抗 SYN 攻击能力，提高系统的稳定性和安全性。请注意，修改此配置项需要系统管理员权限，并且在生产环境中更改内核参数之前，应该仔细评估其影响并确保了解相关风险。
# 使用$a表示在文件的最后一行追加内容。
# net.ipv4.tcp_syncookies = 1是要追加的内容。
# \ 表示换行续行符，用于将内容分隔到下一行。
```



文件句柄数优化 /etc/security/limits.conf

```shell
echo "* hard nproc 655350" >> /etc/security/limits.conf
echo "* soft nproc 655350" >> /etc/security/limits.conf
echo "* hard nofile 655350" >> /etc/security/limits.conf
echo "* soft nofile 655350" >> /etc/security/limits.conf
```

#### 2.2.10：DNS的顺序修改

```shell
vim +42 /etc/nsswitch.conf
 41 #hosts:     db files nisplus nis dns
 42 hosts:      files dns myhostname
```

顺序默认是先hosts文件，优先级最高，然后是dnsserver服务。

#### 2.2.11：关闭selinux 和 firewalld ☑️

```shell
systemctl disable --now firewalld
systemctl mask firewalld
sed -i s/^SELINUX=.*$/SELINUX=permissive/ /etc/selinux/config
setenforce 0

```

#### 2.2.12：环境设定 ☑️

```shell
sed -i '$a \alias vi=vim\' /etc/profile
# 在这个特定的命令中，alias vi=vim是要追加的内容，它定义了一个别名，将vi命令映射到vim编辑器。为了确保sed命令正确解释这个别名定义，需要使用反斜杠\对\和空格进行转义。在这个命令中，\将\字符转义，使其成为普通字符，而不是特殊字符。同样，\后面的空格也是转义，确保vim后面的空格被正确解释为命令的一部分。因此，在这个命令中，添加反斜杠\是为了确保别名定义中的特殊字符被正确解释。如果不添加反斜杠，那么sed命令可能会将\alias解释为特殊命令或参数的一部分，vim后面的空格也可能会被错误地解释。添加反斜杠可以避免这种情况，并确保sed命令正确执行所需的操作。

sed -i '$a alias grep="grep --color=always"' /etc/profile

sed -i '$a umask=027' /etc/profile
# umask是一个用于设置默认文件权限掩码的命令，它指定了在创建新文件时应该使用的默认权限。在这个例子中，将umask设置为027意味着新创建的文件将具有权限640，即用户具有读写权限，组和其他人具有只读权限。通过在/etc/profile文件中设置umask=027，可以确保在系统启动时为所有用户设置默认的文件权限掩码。

```



### 2.3：CentOS软件包管理

#### 2.3.1：可选软件仓库

使用清华大学镜像源

```shell
# 首先备份源目录
cp -r /etc/yum.repos.d /etc/yum.repos.d.bak

# 对于 CentOS 7
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org|baseurl=https://mirrors.tuna.tsinghua.edu.cn|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo

# 对于 CentOS 8
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org/$contentdir|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo
或者
$ mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
$ sudo sed -i -e "s|mirrorlist=|#mirrorlist=|g" /etc/yum.repos.d/CentOS-*
$ sudo sed -i -e "s|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g" /etc/yum.repos.d/CentOS-*

```

注意，如果需要启用其中一些 repo，需要将其中的 `enabled=0` 改为 `enabled=1`。

最后更新软件包缓存

```shell
sudo yum makecache
```

#### 2.3.2：yum

```shell
# 安装和回滚
yum install screen              # 安装
yum history                     # 查看
#或 yum history list all
yum history undo transaction-id # 回滚指定的id
```

#### 2.3.3：rpm包管理

#### 2.3.4：安装tomcat

https://blog.csdn.net/github_38336924/article/details/82253553

#### 2.3.5：安装nodejs

```shell
# CentOS7 安装 16版本
# 1. 安装yum源
curl -fsSL https://rpm.nodesource.com/setup_16.x | bash -
# 2. 安装nodejs
yum install -y nodejs
# 3. 验证版本
node -v
npm -v
```

#### 2.3.6：jdk环境

https://www.oracle.com/tw/java/technologies/javase/javase8u211-later-archive-downloads.html

```shell
wget jdk-8u251-linux-x64.rpm && yum -y install jdk-8u251-linux-x64.rpm

wget jdk-8u251-linux-x64.tar.gz 
```

#### 2.3.7：Anaconda3

[清华源](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)

```shell
wget -P /opt/ https://mirrors.bfsu.edu.cn/anaconda/archive/Anaconda3-2022.10-Linux-x86_64.sh  #下载安装文件，这里选择了清华源


# 安装anaconda和python3
cd /opt && sh Anaconda3-2022.10-Linux-x86_64.sh --python3
#按提示进行安装(回车，yes)，安装路径可以自定定义：在>>>符号后写上目标路径/opt/anaconda3
[/opt] >>> /opt/anaconda3
PREFIX=/opt/anaconda3
# 最后初始化输入yes


# 配置环境变量PATH
#在/etc/profile文件最后追加
export PATH=/opt/anaconda3/bin:$PATH
#让配置即时生效
source /etc/profile

# 安装算法包
conda install lightgbm 
conda install xgboost
```

#### 2.3.8：stress-ng

```shell
yum install epel-release
yum install stress stress-ng -y
```

#### 2.3.9：python3

```shell
# 确保系统最新
yum update 
# 在安装 Python 3 之前，需要确保一些依赖库已经安装，以便在编译和运行过程中正确工作。
yum install yum-utils -y
yum groupinstall development -y
# 安装python3
yum install python3 -y
python3 --version
# 别名
echo 'alias python=python3' >> ~/.bashrc
source ~/.bashrc
```

```shell
# 安装python3.7
# 1. 安装依赖libffi-devel等
yum install gcc patch libffi-devel python-devel zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel -y

# 2. 下载python3.7.17 解压
# https://www.python.org/downloads/source/
wget https://www.python.org/ftp/python/3.7.17/Python-3.7.17.tar.xz
tar -Jxvf Python-3.7.17.tar.xz
cd Python-3.7.17

# 3. 编译安装
mkdir /usr/local/python3
./configure --prefix=/usr/local/python3
make && make install 

# 4. 制作软链
ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3
ln -s /usr/lcoal/python3/bin/pip3 /usr/bin/pip3
```







#### 2.3.10：snap

Snap 包是 Ubuntu 16.04 LTS 发布时引入的新应用格式包。目前已流行在 Ubuntu 且在其他如 Debian、Arch Linux、Fedora、Kaili Linux、openSUSE、Red Hat 等 Linux 发行版上通过 snapd 来安装使用 snap 应用。Snap 使用了 [squashFS](https://en.wikipedia.org/wiki/SquashFS) 文件系统，一种开源的压缩，只读文件系统，基于 GPL 协议发行。一旦 snap 被安装后，其就有一个只读的文件系统和一个可写入的区域。应用自身的执行文件、库、依赖包都被放在这个只读目录，意味着该目录不能被随意篡改和写入。它类似一个容器拥有一个应用程序所有的文件和库，各个应用程序之间完全独立。所以使用snap包的好处就是它解决了应用程序之间的依赖问题，使应用程序之间更容易管理。但是由此带来的问题就是它占用更多的磁盘空间。

```shell
snap install code #安装软件，之前安装路径统一在/snap，新版本/var/lib/snapd/snap，为了习惯上的兼容，可在安装新版本后制作一个软链至/snap
snap remove  code #移除软件，
snap find    code #搜索软件
snap info    code #查看软件信息
snap list         #查看已安装的软件
snap refresh code #保持code最新
snap refresh code channel=latest/stable  #指定来源更新code


# redhat和centos目前也支持安装snap
# 1. 安装新源
#centos7
yum install epel-release 
#centos8/9
dnf install epel-release
sudo dnf upgrade

# 2. 安装snapd
yum install snapd
systemctl enable --now snapd.socket
ln -s /var/lib/snapd/snap /snap

```







### 2.4：救援模式

```shell
### RHEL7系列适用 rd.break
开机按上下键，按e
编辑行 linux16 /vmlinuz...... 中'ro...'(一直到行末尾)，修改为'rw rd.break'
mount -o remount,rw /sysroot  # 重新挂载赋权，之后会有r,w权限
chroot /sysroot               # 改变根目录；
echo raipeng|passwd --stdin root # 变更root密码
touch /.autorelabel              # 为了selinux生效，重新扫描磁盘标签
sync
exit                             # 退出
reboot                           # 重启
```



### 2.5：登录欢迎信息  ✅

有两种登录情况，一种是ssh远程连接，一种是普通本地登录。

1. 我们先说本地登录,修改 /etc/issue

   ```shell
   # 参数的各项说明：
   \d 显示当前日期；
   \l 显示虚拟控制台号；
   \m 显示机器类型，即 CPU 架构，如 i386 或 x86_64 等（相当于 uname -m）；
   \n 显示主机的网络名（相当于 uname -n）；
   \o 显示域名；
   \r 显示 Kernel 内核版本号（相当于 uname -r）；
   \t 显示当前时间；
   \s 显示当前操作系统名称；
   \u 显示当前登录用户的编号，\U 显示当前登录用户的编号和用户；
   \v 显示当前操作系统的版本日期；
   
   # 1. 如将原始内容
   \S
   Kernel \r on an \m
   # 2. 修改为如下内容
   \S
   Kernel \r on an \m
   \t \d
   
   ```

2. 针对ssh服务的banner欢迎信息，修改/etc/ssh/sshd_config

   ```shell
   # 1. 原始内容
    # no default banner path
    # Banner none
    
   # 2. 修改为如下内容，添加banner文件路径，banner文件中的内容就是登录时的欢迎信息
   Banner /etc/ssh/banner
   
   ```

3. 同时出现在ssh登录、本地登录的配置，修改文件内容 /etc/motd ，内容如下

   ```shell
   ########################################################
   redis 6379 本机从节点 对端主节点：192.168.205.64:6379
   redis 6380 本机从节点 对端主节点：192.168.205.64:6380
   ########################################################
   ```








## 三：拾遗

### 3.1 systemd下的 system 和limits  区别

<font color=red>遇到进程报错： Too man open files 的问题</font> 首先考虑修改最大打开文件数量。

在从 CentOS 6 迁移到 CentOS 7 的过程中，可能有一些地方需要调整，最显著的地方莫过于 systemd 带来的改变。曾经配置了正确的 /etc/security/limits.conf 在 CentOS 7 中却不生效了。

即使修改limits.conf 中的 `nproc` ，查看服务 `/proc/\<pid\>/limits 发现仍然 max open files 1024。

在CentOS 7 / RHEL 7的系统中，使用Systemd替代了之前的SysV，因此 /etc/security/limits.conf 文件的配置作用域缩小了一些。<font color=brown> limits.conf的配置，只适用于通过PAM认证登录用户的资源限制，它对systemd的service的资源限制不生效</font>。登录用户的限制，与上面讲的一样，通过 /etc/security/limits.conf 和 /etc/security/limits.d/下的文件来配置即可。

<font color=brown>对于systemd service ，全局配置文件 /etc/systemd/system.conf 和 /etc/systemd/user.conf</font>。 同时，也会加载两个对应的目录/etc/systemd/system.conf.d/\*.conf 和 /etc/systemd/user.conf.d/\*.conf 中的所有.conf文件 。

其中，system.conf 是系统实例使用的，user.conf 是用户实例使用的。一般的service，使用system.conf中的配置即可。systemd.conf.d/*.conf中配置会覆盖全局配置system.conf。

接下来如何生效呢？拿nginx.service举例

```shell
# Reexecute the systemd manager. This will serialize the manager state, reexecute the process and deserialize the state again. This command is of little use except for debugging and package
# 重新执行系统管理器。这将序列化管理器状态，重新执行进程并再次反序列化状态。除了调试和打包之外，此命令几乎没有用处
# 手册页说 daemon-reexec 对软件包升级很有用时，这在很大程度上意味着此命令执行任何新的二进制文件并重新处理其配置。但是，我们用于升级 systemd 的 RPM 已经包含一个脚本来执行此操作，因此在正常升级的情况下通常不需要它。或者您可以重新启动。两者都可以。

# daemon-reexec 会重新执行systemd管理器，重新读取系统配置文件，而daemon-reload只会去读service部分的配置，不包含全局配置/systemd/system.conf
systemctl daemon-reexec
systemctl restart nginx

```

### 3.2 net.core.somaxconn 和 net.ipv4.tcp_max_syn_backlog

<font color=red>遇到进程报错： ConnectTimeout </font> ：首先考虑修改net.core.somaxconn 和 net.ipv4.tcp_max_syn_backlog。

<font color=red>遇到进程报错：Connection reset by peer</font> ：在客户端，大量的tcp连接被reset了，这里我们需要检测被测试系统的tcp backlog值是否足够，如果不够，服务连接达到瓶颈时，可能会出现该问题。考虑修改 net.core.somaxconn 和 listen backlog。

<font color=red>dmesg -T 或者 查看 /var/log/message 看到报错信息 TCP</font> ：request_sock_TCP: Possible SYN flooding on port xxx。意味着大量SYN消息收到了，存入了SYN-ACK队列，但是没有被处理。这可能是因为tcp的backlog设置过小，或者服务器处理性能不足导致的；对于前者考虑修改net.core.somaxconn 和 net.ipv4.tcp_max_syn_backlog。

- **net.core.somaxconn**

  控制ESTABLISHED的接连数量；

- **net.ipv4.tcp_max_syn_backlog**   

  tcp协议栈在收到客户端发送的SYN消息后会回复SYNACK同时将此消息放入队列，等待客户端发送ACK确认； 需要同时开启参数 net.ipv4.tcp_syncookies = 1

- **netdev_max_backlog**  还有个参数供考虑

  /proc/sys/net/core/netdev_max_backlog，主要控制当kernel无法及时处理时接收到的packets的队列大小；

举个例子，请客吃饭，来了大量宾客。**net.core.somaxconn** 相当于有多个座位提供服务，**tcp_max_syn_backlog** 相当于现场面积允许容纳多少人进来。

net.core.somaxconn 的最大值并不是固定的，它取决于特定系统的内存和资源限制。在一些 Linux 发行版中，比如centos7.9 最大值可以设置为 65535。



```shell
# 查看系统中net.core.somaxconn的当前值
sysctl net.core.somaxconn

# 临时更改参数值
sysctl -w net.core.somaxconn=<new_value>

# 永久更改还是编辑 /etc/sysctl.conf 

```



### 3.3 tcp_abort_on_overflow

tcp_abort_on_overflow 变量的值是个布尔值，默认值为0（FALSE关闭）。如果开启，当服务端接收新连接的速度变慢时，服务端会发送RST包（reset包）给客户端，令客户端 重新连接。这意味着如果突然发生溢出，将重获连接。仅当你真的确定不能通过调整监听进程使接收连接的速度变快，可以启用该选项。该选项会影响到客户的连接。



### 3.4 rmem_default 和 rmem_max 套接字缓冲区

```shell
net.core.rmem_default = 8388608
net.core.wmem_default = 8388608
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
```

- rmem_default指定了套接字接收缓冲区的初始大小。如果套接字创建时没有指定SO_RCVBUF选项，就会使用rmem_default的值。默认值通常是87380字节。
- rmem_max指定了套接字接收缓冲区的最大大小。如果套接字接收缓冲区的大小超过rmem_max的值，内核将拒绝TCP连接的数据。默认值通常是131071字节。

通常情况下，rmem_default应该设置为较小的值，因为较大的缓冲区会占用过多的内存资源。而rmem_max的值应该设置为较大的值，以确保在网络拥塞或高延迟情况下，能够接收到足够多的数据，从而避免TCP连接的性能问题。



### 3.5 tcp/ip

**拥塞窗口概念**

当两台计算机之间通过 TCP 协议进行数据传输时，发送方会根据网络情况来决定每次发送的数据量，这个数据量被称为拥塞窗口（Congestion Window，简称 cwnd）。拥塞窗口的大小是动态变化的，会随着网络情况的变化而调整。

假设现在网络情况非常好，数据传输很快，发送方可以增加拥塞窗口的大小，以便在更短的时间内传输更多的数据。拥塞窗口增大后，发送方就可以一次性发送更多的数据，这样可以更好地利用网络带宽。但是如果网络情况不好，比如网络拥塞，数据传输会变得很慢，此时拥塞窗口需要缩小，以减少网络拥塞的程度，避免数据丢失和超时等问题。

因此，拥塞窗口的大小对网络传输的效率和可靠性有着非常重要的影响。根据实际情况，可以通过调整 TCP 协议中的一些参数，来优化拥塞窗口的大小和变化规律，以达到更好的网络传输效果。

**滑动窗口概念**

当发送方和接收方进行通信时，会采用滑动窗口协议来控制数据的传输。滑动窗口是指发送方在发送数据之前需要接收到接收方的确认信息，以确定接收方可以处理多少数据。接收方将它可以接收的数据量告诉发送方，这个告知的值就称为窗口大小。发送方发送数据时，必须保证发送数据量不超过接收方窗口大小，否则会导致数据丢失或者拥塞。当发送方发送数据后，会等待接收方的确认信息，确认信息中会告知发送方，接收方已经成功接收了多少数据，发送方可以根据这个确认信息，移动滑动窗口的位置，继续发送更多的数据。

因此，滑动窗口就像是一个移动的窗口，它的大小会根据接收方的情况进行调整，不断地滑动，控制数据的发送和接收，确保数据的传输顺畅和可靠。



**TCP超时时间**

```shell
sysctl -w net.ipv4.tcp_keepalive_time=<time in seconds>
sysctl -w net.ipv4.tcp_syn_retries=<number of retries>
sysctl -w net.ipv4.tcp_synack_retries=<number of retries>
sysctl -w net.ipv4.tcp_retries1=<number of retries>
sysctl -w net.ipv4.tcp_retries2=<number of retries>

```

将 net.ipv4.tcp_keepalive_time 参数值调小可以使TCP连接更快地检测到连接中断，从而更快地释放被中断的连接和占用的资源。

TCP keep-alive是指在长时间没有数据传输的情况下，TCP连接为了保持活动状态而周期性地发送空的数据包。当连接的一端未能及时响应TCP keep-alive数据包时，TCP连接将被认为已经中断，从而进行连接释放。

默认情况下，net.ipv4.tcp_keepalive_time 参数值为7200秒（2小时），这意味着如果连接中断，可能需要等待2个小时才能释放连接并释放占用的资源。如果您将该参数值调小，例如设置为60秒，将使TCP连接更快地检测到连接中断并释放连接和占用的资源。这样可以使您的系统更快地处理中断的连接，从而提高系统的可用性和性能。

但是，将 net.ipv4.tcp_keepalive_time 参数值设置得太小可能会对系统和网络产生不必要的负载和开销，因此需要根据具体情况进行适当的调整。

**TCP缓冲区大小**

```shell
sysctl -w net.core.rmem_max=<buffer size in bytes>
sysctl -w net.core.wmem_max=<buffer size in bytes>
sysctl -w net.ipv4.tcp_rmem=<minimum receive buffer size> <default receive buffer size> <maximum receive buffer size>
sysctl -w net.ipv4.tcp_wmem=<minimum send buffer size> <default send buffer size> <maximum send buffer size>

```

`net.core.rmem_max` 是内核中<u>所有套接字接收缓冲区</u>大小的上限，单位为字节。

 `net.ipv4.tcp_rmem` 则是仅针对 <u>TCP 套接字接收缓冲区</u>大小的配置，包含了三个值，分别为 `min`, `default` 和 `max`，它们表示：

- `min`: 接收缓冲区的最小大小。
- `default`: 接收缓冲区的默认大小，也是 `recv` 函数未指定缓冲区大小时所使用的大小。
- `max`: 接收缓冲区的最大大小。

如果 `tcp_rmem` 中的 `max` 比 `rmem_max` 小，那么 TCP 套接字的接收缓冲区大小就不能超过 `tcp_rmem` 中的 `max` 值。

如果需要调整 TCP 套接字的接收缓冲区大小，应该修改 `tcp_rmem` 中的 `max` 值。如果需要调整所有套接字的接收缓冲区大小，应该修改 `rmem_max` 值。如果两者的值不同，则以 `tcp_rmem` 中的 `max` 值为准。通常情况下，建议将 `tcp_rmem` 和 `rmem_max` 的值都设置得足够大，以满足高速网络环境下的数据传输需求。

一般来说，推荐将  `tcp_rmem`和`rmem_max`  设置为相同的值，表示TCP接收缓冲区的最大值。可以根据网络带宽和延迟的情况来设置该值，通常建议将其设置为带宽和延迟的乘积再乘以2的幂次方。例如，如果带宽是10Gbps，网络延迟为100ms，则可以将`tcp_rmem`和`rmem_max`设置为

```shell
net.ipv4.tcp_rmem = 1048576 4194304 8388608
net.core.rmem_max = 8388608

```



**tcp抓包 我方访问对端 报错信息 TCP zerowindow**

![](https://raw.githubusercontent.com/9206284/Linux/main/img/tcp-zero-window.png)

TCP Zero Window通常表示TCP接收方已经接收了数据，但由于缓冲区已满，暂时无法接收更多数据，因此发送TCP Zero Window通知，告诉发送方停止发送数据，等待接收方缓冲区空闲。

当您看到TCP Zero Window通知时，这意味着TCP连接的接收方已经满了，没有更多的缓冲区可以接收更多数据，因此发送方应该暂停发送数据，直到接收方缓冲区有足够的空间来接收更多数据。如果发送方继续发送数据，将导致数据包被丢弃，因为接收方已经没有足够的空间来接收这些数据。

TCP Zero Window通常是由于接收方无法及时处理数据，或者接收方缓冲区过小导致的。要解决TCP Zero Window问题，您可以尝试以下一些方法：

1. 扩大接收方缓冲区：可以通过修改接收方操作系统的TCP接收缓冲区大小来增加缓冲区大小，以便能够接收更多的数据。对于Linux系统，可以使用sysctl命令修改以下参数：

   ```shell
   sysctl -w net.ipv4.tcp_rmem="<minimum receive buffer size> <default receive buffer size> <maximum receive buffer size>"
   
   ```

2. 优化接收方应用程序：可以通过优化接收方应用程序来提高数据处理能力，从而更快地从接收方缓冲区中读取数据，以便释放缓冲区。

3. 减少发送方发送速度：可以通过降低发送方发送速度来避免发送方在接收方没有足够缓冲区的情况下发送过多的数据，从而避免TCP Zero Window问题。您可以通过使用TCP拥塞控制算法，例如TCP BBR来自动调整发送速率。



**tcp抓包 对端回包 报错信息 TCP Previous segment not captured**

![](https://raw.githubusercontent.com/9206284/Linux/main/img/tcp-previous-segment-not-captured.png)

"TCP Previous segment not captured"是Wireshark抓包工具中的一条警告信息，表示在抓包过程中，Wireshark没有捕获到前一个数据包，因此无法完整地重建TCP流。

这种情况通常发生在网络拥塞或数据包延迟等情况下，导致部分数据包没有被成功捕获。这会影响Wireshark分析TCP流的完整性和准确性。

解决这种问题的方法通常是重新捕获数据包，或者使用更高级的抓包工具，例如tcpdump或tshark，这些工具可能能够更好地处理丢失的数据包。

同时，如果您在排查网络问题时发现"TCP Previous segment not captured"警告信息，您需要注意，这可能是导致网络问题的原因之一。在排查问题时，您需要结合其他网络信息进行综合分析，以确定导致问题的根本原因，并采取相应的措施进行解决。



**tcp抓包 我方访问对端 报错TCP Dup ACK**

![](https://raw.githubusercontent.com/9206284/Linux/main/img/tcp-dup-ack.png)

TCP Dup ACK（Duplicate Acknowledgment）是TCP协议中的一种流量控制机制，表示接收方在接收到失序的TCP数据包后，发送重复的TCP确认包给发送方，**通知其重传**被跳过的TCP数据包。

当TCP发送方发送一个数据包时，会在数据包头部的序列号字段中包含该数据包的序列号。接收方收到数据包后，会将其确认号字段设置为已经成功接收的最后一个字节的序号+1。如果接收方发现有数据包丢失，它将发送一个带有上次成功接收的最后一个字节的序列号的TCP ACK，以通知发送方重传丢失的数据包。如果接收方接收到了一个乱序的数据包，它将发送一个TCP Dup ACK，通知发送方需要重新发送数据包。

TCP Dup ACK通常是由于网络拥塞导致的数据包重传而发生。当发送方发送一个数据包时，它会等待一段时间来等待接收方发送确认包。如果在这段时间内没有收到确认包，则发送方将重新发送数据包。当接收方接收到重复的数据包时，它将发送一个TCP Dup ACK，告诉发送方需要重新发送丢失的数据包。这个过程将持续，直到发送方成功接收到数据包为止。

要解决TCP Dup ACK问题，您可以尝试以下方法：

1. 确认是否存在网络拥塞：通过观察TCP重传情况，可以确定网络是否存在拥塞。如果存在拥塞，您可以通过调整拥塞控制算法或减少网络流量来缓解拥塞。
2. 检查网络链路质量：如果网络链路质量不佳，例如存在延迟、丢包或者抖动等情况，也可能导致TCP Dup ACK的发生。您可以通过优化网络链路、调整MTU大小等方法来改善网络质量。
3. 调整TCP参数：可以通过调整TCP参数，例如调整TCP重传定时器、拥塞窗口等参数来优化TCP协议的性能，从而减少TCP Dup ACK的发生。

需要注意的是，TCP Dup ACK通常是TCP协议中常见的现象之一，而且并不总是需要解决。在大多数情况下，TCP Dup ACK只是网络拥塞的一种表现形式，只有在网络质量明显下降或者出现其他问题时才需要进一步进行排查和优化。



**tcp抓包 对端回包 报错TCP Fast Retransmission**

![](https://raw.githubusercontent.com/9206284/Linux/main/img/tcp-fast-retransmission.png)

TCP Fast Retransmission是TCP协议中的一种快速重传机制，它通常在网络出现拥塞或丢包的情况下启动。当发送方发现一些数据包已经超时或者没有收到确认包时，它将**快速重传**这些数据包，以尽快地恢复数据传输。

TCP Fast Retransmission是TCP协议中一种常见的机制，其通常在以下情况下发生：

1. 网络拥塞：网络拥塞是TCP Fast Retransmission最常见的原因。当网络出现拥塞时，数据包将被延迟或丢失，从而导致发送方没有收到确认包。发送方在超时时间内没有收到确认包，就会启动快速重传机制，尽快恢复数据传输。
2. 接收方滑动窗口变化：如果接收方的滑动窗口变化导致某些数据包无法接收，则发送方将快速重传这些数据包。

处理TCP Fast Retransmission问题的方法包括：

1. 检查网络拥塞：如果TCP Fast Retransmission是由网络拥塞导致的，您可以通过增加带宽、调整拥塞控制算法或者减少网络流量等方法来缓解拥塞。
2. 检查网络质量：如果网络链路质量不佳，例如存在延迟、丢包或者抖动等情况，也可能导致TCP Fast Retransmission的发生。您可以通过优化网络链路、调整MTU大小等方法来改善网络质量。
3. 调整TCP参数：可以通过调整TCP参数，例如调整TCP重传定时器、拥塞窗口等参数来优化TCP协议的性能，从而减少TCP Fast Retransmission的发生。

需要注意的是，TCP Fast Retransmission通常是TCP协议中常见的现象之一，而且并不总是需要解决。在大多数情况下，TCP Fast Retransmission只是网络拥塞的一种表现形式，只有在网络质量明显下降或者出现其他问题时才需要进一步进行排查和优化。



当网络质量不变的情况下，可以通过调整TCP拥塞控制算法和TCP参数来优化TCP性能，主要包括以下几个方面： 

1. **调整拥塞控制算法**

   TCP协议使用的拥塞控制算法有多种，其中最常用的是Reno和Cubic。这些算法基于不同的拥塞窗口计算公式，可以在网络拥塞时减少拥塞窗口大小以减少用塞。您可以通过修改内核参数来选择使用不同的拥塞控制算法。例如，可以使用以下命令将拥塞控制算法设置为Cubic：

   ```shell
   sysctl -w net.ipv4.tcp_congestion_control=cubic
   ```

   TCP拥塞控制算法的计算方法主要基于拥塞窗口，即TCP连接可以发送的数据量，这是通过将拥塞窗口大小和传输时延和丢包率等参数相结合计算得出的。不同的算法有不同的拥塞窗口计算方法，因此在网络拥塞时，它们会采取不同的减少拥塞窗口的策略，以减少数据包的丢失。

   <font color=green>Cubic算法</font>是一种拥塞控制算法，它基于拥塞窗口的立方函数计算拥塞窗口大小，适合高带宽、高延迟的网络环境，如卫星网络、长距离传输等。Cubic算法在网络拥塞时采用类似S形曲线的方式减少拥塞窗口的大小，以避免过多的数据包丢失。

   <font color=green>Reno算法</font>也是一种常用的TCP拥塞控制算法，它基于拥塞窗口的线性函数计算拥塞窗口大小，适合低延迟、小带宽的网络环境，如局域网、广域网等。Reno算法在网络拥塞时采用指数增长的方式减少拥塞窗口的大小。

   如果只设置一个TCP拥塞控制算法，意味着TCP连接将只使用该算法来计算拥塞窗口大小，这可能会影响TCP连接的性能和可靠性。同时设置多个拥塞控制算法，表示TCP连接将使用多种算法来计算拥塞窗口大小，以更好地适应不同的网络环境和应用场景。但是，需要注意的是，多个拥塞控制算法的同时使用也可能会影响TCP连接的性能，因为不同的算法在计算拥塞窗口大小时可能存在冲突，从而影响TCP连接的传输速度和可靠性。因此，应该根据具体的网络环境和应用场景选择适合的TCP拥塞控制算法，避免不必要的影响。

2. **调整TCP参数**

   TCP协议还有许多与拥塞控制相关的参数，包括TCP窗口大小、拥塞窗口大小、重传定时器等。以下是一些常用的TCP参数和它们的作用：

   - TCP窗口大小：控制TCP连接的数据流量大小。可以通过以下命令设置：

     ```shell
     sysctl -w net.ipv4.tcp_window_scaling=2
     sysctl -w net.ipv4.tcp_rmem='4096 65536 16777216'
     sysctl -w net.ipv4.tcp_wmem='4096 16384 16777216'
     ```

     **TCP窗口缩放（TCP window scaling）**是一种TCP协议的机制，用于增加TCP连接的发送和接收缓冲区大小，以支持更高速的数据传输。TCP窗口缩放的主要思想是通过增加TCP连接的窗口大小，使得TCP连接可以传输更多的数据，从而提高TCP连接的传输速度和效率。TCP window scaling就是用来<font color=red>支持滑动窗口扩大的一个内核参数配置</font>。如果值为0，则表示TCP window scaling机制未启用；如果值为1，则表示TCP window scaling机制已启用。TCP window scaling的值，一般可以设置为2或者3，表示扩大窗口的比例分别为2的2次方或者2的3次方。

     TCP窗口缩放的原理是通过TCP选项字段中的窗口缩放因子来对TCP连接的窗口大小进行扩展。在TCP连接的握手过程中，客户端和服务器可以协商窗口缩放因子的值，用于扩展TCP连接的窗口大小。窗口缩放因子的值是一个正整数，表示窗口大小需要左移的位数，即窗口大小为2的N次方，其中N等于窗口缩放因子的值。

     启用TCP窗口缩放机制可以显著提高TCP连接的传输速度和效率，特别是在高速、高延迟的网络环境下。在Linux系统中，可以通过设置net.ipv4.tcp_window_scaling内核参数来启用TCP窗口缩放机制。默认情况下，该参数已经启用，并且通常不需要进行手动配置。如果需要禁用TCP窗口缩放机制，可以将该参数的值设置为0。

     **tcp_rmem**和**tcp_wmem**是Linux内核中用于控制 TCP连接接收和发送缓冲区大小的参数。tcp_rmem控制接收缓冲区大小，而tcp_wmem控制发送缓冲区大小。这两个参数的值由三个部分组成：最小值、默认值和最大值。在Linux内核中，每个TCP连接都会有自己的接收和发送缓冲区。

     在网络质量不佳的情况下，应该适当调整tcp_rmem和tcp_wmem参数以提高TCP连接的性能和可靠性。具体而言，可以增加TCP连接的接收和发送缓冲区大小，以避免因网络传输延迟而导致的丢包和重传现象。通常情况下，可以适当增加tcp_rmem和tcp_wmem的最大值来扩大TCP连接的接收和发送缓冲区大小。

     在网络拥塞的情况下，应该适当调整tcp_rmem和tcp_wmem参数以避免因TCP连接发送数据过多而导致的网络拥塞现象。具体而言，<u>可以减小TCP连接的发送缓冲区大小，以降低TCP连接发送数据的速度</u>。这样可以减少TCP连接发送数据的数量，避免因网络拥塞而导致的丢包和重传现象。同时，<u>可以增加TCP连接的接收缓冲区大小，以避免因网络传输延迟而导致的丢包和重传现象</u>。

     一般来说，对于较好的网络质量，可以适当增加tcp_rmem和tcp_wmem的默认和最大值，以提高TCP连接的性能和可靠性。对于网络质量较差或者网络拥塞的情况，应该根据具体情况适当调整tcp_rmem和tcp_wmem参数的值，以达到最佳的TCP连接性能和可靠性。

   - 拥塞窗口大小：控制TCP连接的传输速度。可以通过以下命令设置：

     ```shell
     sysctl -w net.ipv4.tcp_slow_start_after_idle=0
     sysctl -w net.ipv4.tcp_max_syn_backlog=65536
     ```

     tcp_slow_start_after_idle是TCP协议的一个参数，用于控制TCP连接空闲一段时间后的重启行为。当一个TCP连接在一段时间内没有传输数据时，操作系统会将该连接的状态设置为idle状态。在这种情况下，TCP协议会根据tcp_slow_start_after_idle参数的值来确定连接重启时的初始拥塞窗口大小。

     tcp_slow_start_after_idle的默认值为1，表示连接空闲一段时间后，TCP协议会将拥塞窗口大小重新设置为MSS（最大报文段长度）。如果将tcp_slow_start_after_idle设置为0，则表示连接空闲一段时间后，TCP协议会将拥塞窗口大小设置为当前拥塞窗口的一半。

     通过调整tcp_slow_start_after_idle参数的值，可以控制TCP连接空闲后重新启动时的行为，从而提高连接的性能和可靠性。<u>如果**连接空闲时间较长**，可以将tcp_slow_start_after_idle设置为较大的值，以避免重新启动时出现拥塞窗口的快速增大，从而导致网络拥塞</u>。<u>**如果连接空闲时间较短**，可以将tcp_slow_start_after_idle设置为较小的值，以提高连接的响应速度</u>。

     <font color=brown>如何判断TCP连接空闲时间是长还是短呢？</font>

     要判断TCP连接的空闲时间长短，可以通过观察TCP连接的通信模式和传输数据的频率来进行判断。如果TCP连接需要长时间传输大量数据，则认为连接空闲时间比较短。如果TCP连接只是偶尔传输一些小数据包，或者只在特定时间段内进行传输，则认为连接空闲时间比较长。

     另外，可以通过查看操作系统的TCP连接状态来获取TCP连接的空闲时间。在Linux系统中，可以通过运行以下命令来列出当前TCP连接的状态信息

     ```shell
     ss -anp | grep ESTAB
     ```

     这个命令可以列出当前所有处于ESTABLISHED状态的TCP连接，包括连接的本地IP地址和端口、对端IP地址和端口、连接的状态以及连接的持续时间。通过观察连接的持续时间，可以大致了解连接的空闲时间长短。<u>**如果连接的持续时间比较长，说明连接空闲时间比较短；如果连接的持续时间比较短，则说明连接空闲时间比较长**</u>。

     在判断TCP连接空闲时间的同时，还需要考虑网络的实际情况和负载状况，以确定tcp_slow_start_after_idle参数的最佳值。如果网络拥塞较为严重，建议将tcp_slow_start_after_idle设置为较大的值，以避免连接重新启动时出现拥塞窗口的快速增大，从而导致网络拥塞。如果网络负载较轻，可以将tcp_slow_start_after_idle设置为较小的值，以提高连接的响应速度。

   - 重传定时器：控制TCP连接的重传时间间隔。可以通过以下命令设置：

     ```shell
     sysctl -w net.ipv4.tcp_syn_retries=3
     sysctl -w net.ipv4.tcp_synack_retries=3
     sysctl -w net.ipv4.tcp_retries2=15
     sysctl -w net.ipv4.tcp_fin_timeout=30
     ```

     TCP连接的重传时间间隔，TCP连接的重传时间间隔是指在TCP协议中，当一方发送数据包后未收到对方的确认包时，会触发数据包的重传机制，此时重传的时间间隔称为重传时间间隔。

     在TCP协议中，每个数据包都有一个序号和一个确认号，当发送方发送一个数据包时，接收方必须对其进行确认，以确保数据包已经正确地到达。如果发送方在一定时间内未收到确认包，就会触发重传机制，重新发送该数据包。这个时间间隔被称为重传时间间隔。

     TCP连接的重传时间间隔通常由TCP协议栈自动计算并调整，以适应当前网络状况。在网络质量较好的情况下，重传时间间隔较长，可以减少网络负载；而在网络质量较差的情况下，重传时间间隔较短，可以加快数据传输速度和提高数据可靠性。同时，系统管理员也可以根据实际情况手动设置TCP连接的重传时间间隔，以优化网络性能。
   
   

### 3.6 ls /dev/mapper  查看硬盘设备映射

```shell
ls /dev/dm* -l
brw-rw---- 1 root disk 253, 0 2月  15 2022 /dev/dm-0
brw-rw---- 1 root disk 253, 1 2月  15 2022 /dev/dm-1

ls /dev/mapper/ -l
总用量 0
lrwxrwxrwx 1 root root       7 2月  15 2022 centos-root -> ../dm-0
lrwxrwxrwx 1 root root       7 2月  15 2022 centos-swap -> ../dm-1
crw------- 1 root root 10, 236 2月  15 2022 control
```



### 3.7 tw_reuse、tw_recycle和max_tw_buckets 

tw_reuse：对客户端有效，对服务端无效。客户端选tw_reuse，基本可实现并发请求6w/s

tw_recyle：对客户端、服务端均有效，但有时间戳一致的要求。线上环境因为会在nat负载后面，负载会将timestamp清空，配置之后会影响连接成功。一般处理服务器的TIME_WAIT，使用参数  tcp_max_tw_buckets，如设置为net.ipv4.tcp_max_tw_buckets=1000000 这样。

客户端

```shell
sysctl -w net.ipv4.tcp_tw_reuse = 1        #优化
sysctl -w net.ipv4.tcp_tw_recycle = 0      #默认值
sysctl -w net.ipv4.tcp_timestamps = 1      #默认值
```

服务端

```shell
sysctl -w net.ipv4.tcp_tw_reuse = 0        #默认值
sysctl -w net.ipv4.tcp_tw_recycle = 0      #默认值
sysctl -w net.ipv4.tcp_timestamps = 1      #默认值
sysctl -w net.ipv4.tcp_max_tw_buckets=1000000      #优化
```



### 3.8 使用yum命令报错File "/usr/bin/yum", line 30 except KeyboardInterrupt,e

问题出现原因：
yum包管理是使用python2.x写的，将python2.x升级到python3 或者安装了python3 以后，由于python版本语法兼容性导致问题出现。

解决办法：
修改yum脚本，将python版本指向python2

```shell
vim /usr/bin/yum
#!/usr/bin/python2.7
```

修改/usr/libexec/urlgrabber-ext-down脚本，将python版本指向python2

```shell
vim /usr/libexec/urlgrabber-ext-down
#!/usr/bin/python2.7
```

