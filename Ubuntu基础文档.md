[TOC]


# Ubuntu基础文档

## 一：Debian与Ubuntu简介
### 1.1：Debian简介
是从 1993 年由 Ian Murdock(伊恩·默多克) 发起的，受到当时 Linux 与 GNU 的⿎舞，⽬标是成为⼀个公开的发⾏ 版，经过⼆⼗⼏年的迭代更新Debian 从⼀个⼩型紧密的⾃由软件骇客（hacker）⼩组，逐渐成⻓成今⽇庞⼤且运 作良好的开发者与⽤户社群，Debian 的名字是由 Debian 的创始⼈ Ian Murdock (伊恩·默多克) 和他的爱妻 Debra(黛布拉) 两⼈的名字组合⽽成的。

Debian 是由⼤约⼀千个分布在世界各地的开发者⽆偿地利⽤他们的业余时间开发的，⽽这些开发者实际上⼤部分 都没⻅过⾯，彼此之间的通讯⼤多是通过电⼦邮件（lists.debian.org 上的邮件列表）和 IRC（irc.debian.org 上的 #debian 频道）来完成的，⽬前Debian 提供59000多个软件包的维护与更新。

```txt
Debian官⽹： https://www.debian.org/ 
官⽅镜像地址： https://www.debian.org/mirror/list 
清华⼤学下载地址： https://mirrors.tuna.tsinghua.edu.cn/debian-cd/10.1.0-live/amd64/isohybrid/
```
### 1.2：Ubuntu简介
Ubuntu（友帮拓、优般图、乌班图）早期是⼀个开源的GNU/Linux操作系统，Ubuntu 是基于Debian GNU/Linux，⽀持x86、amd64（即x64）和ppc架构，由全球化的专业开发团队（Canonical Ltd）打造的，其名 称来⾃⾮洲南部祖鲁语或豪萨语的“ubuntu”⼀词，类似儒家“仁爱”的思想，意思是“⼈性”、“我的存在是因为⼤家的 存在”，是⾮洲传统的⼀种价值观， Ubuntu基于Debian发⾏版和GNOME桌⾯环境，⽽从11.04版起，Ubuntu发⾏ 版放弃了Gnome桌⾯环境，改为Unity，与Debian的不同在于它每6个⽉会发布⼀个新版本，Ubuntu的⽬标在于 为⼀般⽤户提供⼀个最新的、同时⼜相当稳定的主要由⾃由软件构建⽽成的操作系统，Ubuntu具有庞⼤的社区⼒ 量，⽤户可以⽅便地从社区获得帮助，Ubuntu对GNU/Linux的普及特别是桌⾯普及作出了巨⼤贡献，由此使更多⼈共享开源的成果与精彩。
### 1.3：Ubuntu镜像下载
Ubuntu哪⾥可以下载的到呢？
```txt
http://cdimage.ubuntu.com/releases/ #ubuntu server(服务器版) 
http://releases.ubuntu.com/ #ubuntu desktop(桌⾯版) 
http://cdimage.ubuntu.com/daily-live/current/ #20.04每天镜像
```
#### 1.3.1 系统镜像间区别
https://packages.ubuntu.com/search?lang=zh-cn&arch=any&keywords=libfuse-dev
```txt
ubuntu-18.04.3-live-server-amd64.iso 	#带live，ISO镜像提供不安装就可以试⽤系统的功能 
ubuntu-18.04.3-server-amd64.iso 		#不带live，不可⽤试⽤，但是可以直接进⾏系统安装
```
#### 1.3.2 不同指令集的ISO镜像
```txt
CPU架构			安装包标识				备注
x86 			i386 					32位，server版本不再支持32位
x86-64			amd64 					64位
arm v7			ARM64 					arm平台
IBM s390x		s390x 					IBM System z
POWER 			ppc64el 				PowerPC
preinstalled-server-arm64+raspi3  		预安装树莓派系列
```

## 二：Ubuntu Server安装及使用
### 2.1: Ubuntu Server安装
#### 2.1.1 安装界面传递内核参数
<font color=blue>按 **F6** 输入 **net.ifnames=0 biosdevname=0** </font>
![安装界面传递内核参数](https://raw.githubusercontent.com/9206284/Linux/main/img/20220207223836.png)

### 2.2: Ubuntu Server系统基础配置
Ubuntu官方使用文档  
https://help.ubuntu.com
#### 2.2.1：更改主机名
```shell
hostnamectl set-hostname ubuntu01
```
#### 2.2.2：更改网卡名称为eth*:
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
update-grub

reboot
```
#### 2.2.3：配置root远程登录
默认情况下，ubuntu不允许root⽤户远程ssh，如果有实际场景需要允许root⽤户远程ssh，则需要设置root密 码，并且编辑/etc/ssh/sshd_config⽂件修改如下：

```shell
vim /etc/ssh/sshd_config

PermitRootLogin yes #改为允许Root登录
PasswordAuthentication yes #打开密码认证，其实默认就是允许通过密码认证登录
```

```shell
#切换到root用户环境
sudo su - root  

#设置root密码
passwd 
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

#重启ssh服务并测试root⽤户远程ssh连接
systemctl restart sshd 
```
#### 2.2.4: Ubuntu 18.04以上网络配置
netplan官网 https://netplan.io/  
netplan官方范例 https://netplan.io/examples/

```txt
Ubuntu 从 17.10 开始，已放弃在 /etc/network/interfaces ⾥固定IP的配置，⽽是改成 netplan ⽅式， 配置⽂件是：/etc/netplan/00-installer-config.yaml，使用netplan apply来应用相关配置。
```
ubuntu 17.04及之前的静态IP配置⽅式：
```shell
~# cat /etc/network/interfaces 
root@magedu:~# cat /etc/network/interfaces 
# interfaces(5) file used by ifup(8) and ifdown(8) 
auto lo 
iface lo inet loopback

auto eth0 #⽹卡⾃启动，写⾃⼰要配置IP的实际⽹卡名称 
iface eth0 inet static #配置静态IP，写⾃⼰要配置IP的实际⽹卡名称 
address 172.18.3.12 #IP地址 
netmask 255.255.0.0 #掩码 
gateway 172.18.0.1 #⽹关 
dns-nameservers 223.6.6.6 #DNS 
dns-nameservers 223.5.5.5

#重启⽹络服务 
~# /etc/init.d/networking restart 
~# systemctl restart networking.service
```
##### 2.2.4.1: 单网卡DHCP和静态地址
```yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        enp3s0:
            dhcp4: true
```
静态地址配置，还可以提供dns，以及默认路由定义网关。
```yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        enp3s0:
            addresses:
                - 10.10.10.2/24
            nameservers:
                search: [mydomain, otherdomain]
                addresses: [10.10.10.1, 1.1.1.1]
            routes:
                - to: default
                  via: 10.10.10.1
```
##### 2.2.4.2: 多网卡DHCP
```yaml
network:
    version: 2
    ethernets:
        enred:
            dhcp4: yes
            dhcp4-overrides:
                route-metric: 100
        engreen:
            dhcp4: yes
            dhcp4-overrides:
                route-metric: 200
```
##### 2.2.4.3: 单网卡多地址
addresses键可以对应一组地址列表
```yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        enp3s0:
         addresses:
             - 10.100.1.38/24
             - 10.100.1.39/24
         routes:
             - to: default
               via: 10.100.1.1
```
##### 2.2.4.4: 单网卡多地址多网关
```yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        enp3s0:
         addresses:
            - 10.0.0.10/24
            - 11.0.0.11/24
            routes:
            - to: default
              via: 10.0.0.1
              metric: 200
            - to: default
              via: 11.0.0.1
              metric: 300
```
dhcp的地址metric值默认100
##### 2.2.4.5: 多网卡静态
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      dhcp6: no
      addresses: [172.18.3.18/16]
      gateway4: 172.18.0.1
      nameservers:
        addresses: [223.6.6.6]
    eth1:
      dhcp4: no
      dhcp6: no
      addresses: [10.20.3.18/16]
      routes:
        - to: 172.20.0.0/16 
          via: 10.20.0.1
        - to: 10.20.0.0/16
          via: 10.20.0.1
        - to: 10.2.0.0/16
          via: 10.20.0.1
        - to: 10.8.0.0/16
          via: 10.20.0.1
```
##### 2.2.4.5: 单网卡桥接
**static**
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      dhcp6: no

  bridges:
    br0:
      dhcp4: no
      dhcp6: no
      addresses: [172.18.3.18/16]
      gateway4: 172.18.0.1
      nameservers:
        addresses: [223.6.6.6]
      interfaces:
        - eth0
```
**dhcp**
```yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        enp3s0:
            dhcp4: no
    bridges:
        br0:
            dhcp4: yes
            interfaces:
                - enp3s0
```
更复杂的vlan桥接 [详见示例](https://netplan.io/examples/#configuring-network-bridges)
##### 2.2.4.6: 多网卡桥接
将br0和br1分别桥接到eth0和eth1
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
  	eth0:
  	  dhcp4: no
  	  dhcp6: no
  	eth1:
  	  dhcp4: no
  	  dhcp6: no

  bridges:
  	br0:
  	  dhcp4: no
  	  dhcp6: no
  	  addresses: [172.18.3.18/16]
  	  gateway4: 172.18.0.1
  	  nameservers:
  	  	addresses: [61.177.7.1]
  	  interfaces:
  	  	- eth0
  	br1:
  	  dhcp4: no
  	  dhcp6: no
  	  addresses: [10.20.3.18/16]
  	  routes:
  	  	- to: 172.20.0.0/16
  	  	  via: 10.20.0.1
  	  	- to: 10.20.0.0/16
  	  	  via: 10.20.0.1
  	  interfaces:
  	  	- eth1
```
##### 2.2.4.7: 双网卡绑定
**七种bond模式说明:**
```txt
第⼀种模式：mod=0，即：(balance-rr) Round-robin policy（平衡抡循环策略） 特点：传输数据包顺序是依次传输（即：第1个包⾛eth0，下⼀个包就⾛eth1….⼀直循环下去，直到最后⼀个传输完 毕），此模式提供负载平衡和容错能⼒。

第⼆种模式：mod=1，即： (active-backup) Active-backup policy（主-备份策略） 特点：只有⼀个设备处于活动状态，当⼀个宕掉另⼀个⻢上由备份转换为主设备。mac地址是外部可⻅得，从外⾯看 来，bond的MAC地址是唯⼀的，以避免switch(交换机)发⽣混乱。此模式只提供了容错能⼒；由此可⻅此算法的优点 是可以提供⾼⽹络连接的可⽤性，但是它的资源利⽤率较低，只有⼀个接⼝处于⼯作状态，在有 N 个⽹络接⼝的情况 下，资源利⽤率为1/N。 第三种模式：mod=2，即：(balance-xor) XOR policy（平衡策略） 特点：基于指定的传输HASH策略传输数据包。缺省的策略是：(源MAC地址 XOR ⽬标MAC地址) % slave数量。其他 的传输策略可以通过xmit_hash_policy选项指定，此模式提供负载平衡和容错能⼒。

第四种模式：mod=3，即：broadcast（⼴播策略） 特点：在每个slave接⼝上传输每个数据包，此模式提供了容错能⼒。

第五种模式：mod=4，即：(802.3ad) IEEE 802.3adDynamic link aggregation（IEEE 802.3ad 动态链接 聚合） 特点：创建⼀个聚合组，它们共享同样的速率和双⼯设定。根据802.3ad规范将多个slave⼯作在同⼀个激活的聚合体 下。 必要条件： 条件1：ethtool⽀持获取每个slave的速率和双⼯设定。 条件2：switch(交换机)⽀持IEEE 802.3ad Dynamic link aggregation。 条件3：⼤多数switch(交换机)需要经过特定配置才能⽀持802.3ad模式。

第六种模式：mod=5，即：(balance-tlb) Adaptive transmit load balancing（适配器传输负载均衡） 特点：不需要任何特别的switch(交换机)⽀持的通道bonding。在每个slave上根据当前的负载（根据速度计算）分 配外出流量。如果正在接受数据的slave出故障了，另⼀个slave接管失败的slave的MAC地址。 该模式的必要条件： ethtool⽀持获取每个slave的速率

第七种模式：mod=6，即：(balance-alb) Adaptive load balancing（适配器适应性负载均衡） 特点：该模式包含了balance-tlb模式，同时加上针对IPV4流量的接收负载均衡(receive load balance, rlb)，⽽且不需要任何switch(交换机)的⽀持。
```
双网卡绑定配置
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
  	eth0:
  	  dhcp4: no
  	  dhcp6: no
  	eth1:
  	  dhcp4: no
  	  dhcp6: no

  bonds:
  	bond0:
  	  interfaces: [eth0,eth1]
  	  addresses: [172.18.3.18/16]
  	  gateway4: 172.18.0.1
  	  nameservers:
  	  	addresses: [61.177.7,1,114.114.114.114]
  	  parameters:
  	  	mode: active-backup
  	  	mii-monitor-interval: 100
```
##### 2.2.4.8: 双网卡绑定桥接
网卡绑定用于提供网卡接口冗余以及高可用和端口聚合功能，桥接网卡再给需要桥接设备的服务使用。
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
  	eth0:
  	  dhcp4: no
  	  dhcp6: no
  	eth1:
  	  dhcp4: no
  	  dhcp6: no

  bonds:
  	bond0:
  	  interfaces:
  	  	- eth0
  	  	- eth1
  	  parameters:
  	  	mode: active-backup
  	  	mii-monitor-interval: 100

  bridges:
  	br0:
  	  dhcp4: no
  	  dhcp6: no
  	  addresses: [172.18.3.18/16]
  	  gateway4: 172.18.0.1
  	  nameservers:
  	  	addresses: [61.177.7.1,114.114.114.114]
  	  interfaces:
  	  	- bond0
```
##### 2.2.4.9: 内网多网卡绑定
多网络情况下的网卡绑定
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
  	eth0:
  	  dhcp4: no
  	  dhcp6: no
  	eth1:
  	  dhcp4: no
  	  dhcp6: no
  	eth2:
  	  dhcp4: no
  	  dhcp6: no
  	eth3:
  	  dhcp4: no
  	  dhcp6: no

  bonds:
  	bond0:
  	  interfaces: [eth0,eth1]
  	  addresses: [172.18.3.18/16]
  	  gateway4: 172.18.0.1
  	  nameservers:
  	  	addresses: [61.177.7.1,114.114.114.114]
  	  parameters:
  	  	mode: active-backup
  	  	mii-monitor-interval: 100
  	bond1:
  	  interfaces: [eth2,eth3]
  	  addresses: [10.23.3.18/16]
  	  parameters:
  	  	mode: active-backup
  	  	mii-monitor-interval: 100
  	  routes:
  	  	- to: 172.20.0.0/16
  	  	  via: 10.20.0.1
  	  	- to: 10.20.0.0/16
  	  	  via: 10.20.0.1
```
##### 2.2.4.10: 内网多网卡绑定桥接

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
  	eth0:
  	  dhcp4: no
  	  dhcp6: no
  	eth1:
  	  dhcp4: no
  	  dhcp6: no
  	eth2:
  	  dhcp4: no
  	  dhcp6: no
  	eth3:
  	  dhcp4: no
  	  dhcp6: no

  bonds:
  	bond0:
  	  interfaces: [eth0,eth1]
  	  parameters:
  	  	mode: active-backup
  	  	mii-monitor-interval: 100
    bond1:
      interfaces: [eth2,eth3]
      parameters:
      	mode: active-backup
      	mii-monitor-interval: 100

  bridges:
  	br0:
  	  dhcp4: no
  	  dhcp6: no
  	  addresses: [172.18.3.18/16]
  	  gateway4: 172.18.0.1
  	  nameservers:
  	  	addresses: [61.177.7.1,114.114.114.114]
  	  interfaces:
  	  	- bond0
  	br1:
  	  dhcp4: no
  	  dhcp6: no
  	  interfaces:
  	  	- bond1
  	  addresses: [10.20.3.18/16]
  	  routes:
  	  	- to: 172.20.0.0/16
  	  	  via: 10.20.0.1
  	  	- to: 10.20.0.0/16
  	  	  via: 10.20.0.1
```

##### 2.2.4.11: 开放的无线网络连接

```yaml
network:
    version: 2
    wifis:
        wlan0:
                access-points:
                        opennetwork: {}
                dhcp4: yes
```

##### 2.2.4.12: WPA 个人无线网络

```yaml
network:
    version: 2
    renderer: networkd
    wifis:
        wlp2s0b1:
            dhcp4: no
            dhcp6: no
            addresses: [192.168.0.21/24]
            nameservers:
                addresses: [192.168.0.1, 8.8.8.8]
            access-points:
#下面network_ssid_name替换为目标ssid名称，password输入正确密码
                "network_ssid_name":
                    password: "**********"
            routes:
                - to: default
                  via: 192.168.0.1
```



### 2.3: Ubuntu软件包管理

ubuntu安装、升级、卸载软件包等常规操作。
#### 2.3.1: 可选软件仓库
阿⾥云仓库地址：https://opsx.alibaba.com/mirror 

中科⼤：http://mirrors.ustc.edu.cn/help/ubuntu.html

清华⼤学：https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/ 

华为：https://mirrors.huaweicloud.com/
#### 2.3.2: apt/apt-get
```shell
# apt list #apt列出仓库软件包，等于yum list 
# apt search NAME #搜索安装包 
# apt show apache2 #查看某个安装包的详细信息 
# apt install apache2 #在线安装软件包 
# apt remove apache2 #卸载单个软件包但是保留配置⽂件 
# apt autoremove apache2 #删除安装包并解决依赖关系 
# apt update #更新本地软件包列表索引，修改了apt仓库后必须执⾏ 
# apt purge apache2 #卸载单个软件包删除配置⽂件 
# apt upgrade #升级所有已安装且可升级到新版本的软件包 
# apt full-upgrade #升级整个系统，必要时可以移除旧软件包。 
# apt edit-sources #编辑source源⽂件

# apt-cache madison nginx #查看仓库中软件包有哪些版本可以安装 
# apt install nginx=1.18.0-0ubuntu1.2  #安装软件包的时候指定安装具体的版本
```
#### 2.3.3: 设置oracle JDK环境
```shell
# pwd 
/usr/local/src

#解压⼆进制⽂件并设置软连接：
tar xf jdk-8u212-linux-x64.tar.gz 
ln -sv /usr/local/src/jdk1.8.0_212 /usr/local/jdk

#配置环境变量：
cat <<EOF >> /etc/profile
export JAVA_HOME=/usr/local/jdk 
export PATH=\$JAVA_HOME/bin:\$JAVA_HOME/jre/bin:\$PATH 
export CLASSPATH=.\$CLASSPATH:\$JAVA_HOME/lib:\$JAVA_HOME/jre/lib:\$JAVA_HOME/lib/tools.jar
EOF

#重新导⼊环境变量并验证：
source /etc/profile 
java -version

```
#### 2.3.4: 安装OpenJDK
```shell
apt install openjdk-8-jdk
```
#### 2.3.5: 安装常用系统命令
```shell
apt purge ufw lxd lxd-client lxcfs lxc-common
apt install iproute2 ntpdate tcpdump telnet traceroute nfs-kernel-server nfscommon lrzsz tree openssl libssl-dev libpcre3 libpcre3-dev zlib1g-dev gcc openssh-server zlib1g-dev iotop unzip zip
```
#### 2.3.6: 系统资源限制优化
```shell
cat  /etc/security/limits.conf

#root账户的资源软限制和硬限制
root 		soft 	core 		unlimited
root 		hard 	core 		unlimited
root 		soft 	nproc 		1000000
root 		hard 	nproc 		1000000
root 		soft 	nofile 		1000000
root 		hard 	nofile 		1000000
root 		soft 	memlock 	32000
root 		hard 	memlock 	32000
root 		soft 	msgqueue 	8192000
root 		hard 	msgqueue 	8192000

#其他账户的资源软限制和硬限制
* 		soft 	core 		unlimited
* 		hard 	core 		unlimited
* 		soft 	nproc 		1000000
* 		hard 	nproc 		1000000
* 		soft 	nofile 		1000000
* 		hard 	nofile 		1000000
* 		soft 	memlock 	32000
* 		hard 	memlock 	32000
* 		soft 	msgqueue 	8192000
* 		hard 	msgqueue 	8192000
```
#### 2.3.7: 内核参数优化
```shell
# Controls source route verification 
net.ipv4.conf.default.rp_filter = 1 
net.ipv4.ip_nonlocal_bind = 1 
net.ipv4.ip_forward = 1

# Do not accept source routing 
net.ipv4.conf.default.accept_source_route = 0

# Controls the System Request debugging functionality of the kernel 
kernel.sysrq = 0

# Controls whether core dumps will append the PID to the core filename. 
# Useful for debugging multi-threaded applications. 
kernel.core_uses_pid = 1

# Controls the use of TCP syncookies 
net.ipv4.tcp_syncookies = 1

# Disable netfilter on bridges.
net.bridge.bridge-nf-call-ip6tables = 0 
net.bridge.bridge-nf-call-iptables = 0 
net.bridge.bridge-nf-call-arptables = 0

# Controls the default maxmimum size of a mesage queue 
kernel.msgmnb = 65536

# # Controls the maximum size of a message, in bytes 
kernel.msgmax = 65536

# Controls the maximum shared segment size, in bytes 
kernel.shmmax = 68719476736

# # Controls the maximum number of shared memory segments, in pages 
kernel.shmall = 4294967296

# TCP kernel paramater 
net.ipv4.tcp_mem = 786432 1048576 1572864 
net.ipv4.tcp_rmem = 4096 87380 4194304 
net.ipv4.tcp_wmem = 4096 16384 4194304 
net.ipv4.tcp_window_scaling = 1 
net.ipv4.tcp_sack = 1

# socket buffer 
net.core.wmem_default = 8388608 
net.core.rmem_default = 8388608 
net.core.rmem_max = 16777216 
net.core.wmem_max = 16777216 
net.core.netdev_max_backlog = 262144 
net.core.somaxconn = 20480 
net.core.optmem_max = 81920

# TCP conn 
net.ipv4.tcp_max_syn_backlog = 262144 
net.ipv4.tcp_syn_retries = 3 
net.ipv4.tcp_retries1 = 3 
net.ipv4.tcp_retries2 = 15

# tcp conn reuse 
net.ipv4.tcp_timestamps = 0 
net.ipv4.tcp_tw_reuse = 0 
net.ipv4.tcp_tw_recycle = 0 
net.ipv4.tcp_fin_timeout = 1

net.ipv4.tcp_max_tw_buckets = 20000 
net.ipv4.tcp_max_orphans = 3276800 
net.ipv4.tcp_synack_retries = 1 
net.ipv4.tcp_syncookies = 1

# keepalive conn 
net.ipv4.tcp_keepalive_time = 300 
net.ipv4.tcp_keepalive_intvl = 30 
net.ipv4.tcp_keepalive_probes = 3 
net.ipv4.ip_local_port_range = 10001 65000

# swap 
vm.overcommit_memory = 0 
vm.swappiness = 10

#net.ipv4.conf.eth1.rp_filter = 0
#net.ipv4.conf.lo.arp_ignore = 1
#net.ipv4.conf.lo.arp_announce = 2
#net.ipv4.conf.all.arp_ignore = 1
#net.ipv4.conf.all.arp_announce = 2
```
#### 2.3.8: dpkg包管理
rpm：RPM(Red Hat Package Manager)，是基于Red hat的Linux Distribution的包管理系统，同时也指rpm包本 身，RPM⽤于rpm包的管理（诸如安装、卸载、升级等）

"dpkg "是"Debian Packager "的简写,为 "Debian"专⻔开发的套件管理系统，⽅便软件的安装、更新及移除。所有 源⾃“Debian”的“Linux ”发⾏版都使⽤ “dpkg”，例如 “Ubuntu”、“Knoppix ”等。
```shell
# dpkg -i gitlab-ce_11.9.8-ce.0_amd64.deb #安装某个软件包 
# dpkg -r gitlab-ce #删除某个软件包保留配置⽂件 
# dpkg -r -P gitlab-ce #删除某个软件包不保留配置⽂件 
# dpkg -I gitlab-ce_11.9.8-ce.0_amd64.deb #查看软件包信息 
# dpkg -c gitlab-ce_11.9.8-ce.0_amd64.deb #查看软件包内的⽂件及⽬录内容 
# dpkg -l #列出本机已经安装的所有软件
```
## 三：Ubuntu Desktop安装及使用
### 3.1 环境配置
```txt
1、设置软件源及安装常⽤命令： 
https://opsx.alibaba.com/mirror

sudo apt-get install build-essential cmake pkg-config qt4-qmake libqt4-dev desktopfile-utils \ 
libavformat-dev libavcodec-dev libavutil-dev libswscale-dev libasound2-dev libpulse-dev libjack-jackd2-dev \ 
libgl1-mesa-dev libglu1-mesa-dev libx11-dev libxfixes-dev libxext-dev libxi-dev libxinerama-dev

2、系统更新及配置中⽂语⾔环境：

3、安装搜狗拼⾳输⼊法 
https://pinyin.sogou.com/linux/?r=pinyin

4、安装转码器ffmpeg： 
多媒体视频处理⼯具FFmpeg有⾮常强⼤的功能包括视频采集功能、视频格式转换、视频抓图、给视频加⽔印等。 
sudo apt-get install ffmpeg

5、安装视频播放器smplayer：
https://www.jianshu.com/p/f24252c632d0 
sudo apt-get install smplayer

6、办公软件WPS： 
https://www.wps.cn/product/wpslinux

7、单机VNC⼯具: 
x11vnc server

8、RealVNC v6.6：
⽀持多个⽤户同时连接
https://www.realvnc.com/en/connect/download/vnc/linux/

9、⽂本编辑器： 
visual studio code

10、markdown⼯具： 
https://www.typora.io/#linux 
sudo apt-get install typora=0.9.60-1

11、Ubuntu 桌⾯3D特效： 
sudo apt-get install compiz-plugins compizconfig-settings-manager

12、左侧菜单在底栏显示： 
~$ gsettings set com.canonical.Unity.Launcher launcher-position Bottom

13、VMware workstion桥接⽹卡设置 
https://kb.vmware.com/s/article/287?lang=zh_CN 
chmod a+rw /dev/vmnet0 #解决⽹卡桥接不通问题

14、远程⼯具: 
SecureCRT

15、python开发： 
pycharm

16、java开发： 
Eclipse 
Intellij IDEA

17、vmware workstation： 
#⽹卡开启混杂模式 https://kb.vmware.com/s/article/287?lang=zh_CN 
$ sudo vim /etc/init.d/vmware 
126 # Start the virtual ethernet kernel service 
127 vmwareStartVmnet() { 
128 vmwareLoadModule $vnet 
129 "$BINDIR"/vmware-networks --start >> $VNETLIB_LOG 2>&1 
130 chgrp magedu /dev/vmnet* 
131 chown magedu /dev/vmnet* 
132 chmod a+rwx /dev/vmnet* 
133 }
```