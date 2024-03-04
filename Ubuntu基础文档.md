

# Ubuntu基础文档



[Toc]

## 一：Debian与Ubuntu简介

### 1.1：Debian简介

是从 1993 年由 Ian Murdock(伊恩·默多克) 发起的，受到当时 Linux 与 GNU 的⿎舞，⽬标是成为⼀个公开的发⾏版，经过⼆⼗⼏年的迭代更新Debian 从⼀个⼩型紧密的⾃由软件骇客（hacker）⼩组，逐渐成⻓成今⽇庞⼤且运作良好的开发者与⽤户社群，Debian 的名字是由 Debian 的创始⼈ Ian Murdock (伊恩·默多克) 和他的爱妻 Debra(黛布拉) 两⼈的名字组合⽽成的。

Debian 是由⼤约⼀千个分布在世界各地的开发者⽆偿地利⽤他们的业余时间开发的，⽽这些开发者实际上⼤部分 都没⻅过⾯，彼此之间的通讯⼤多是通过电⼦邮件（lists.debian.org 上的邮件列表）和 IRC（irc.debian.org 上的 #debian 频道）来完成的，⽬前Debian 提供59000多个软件包的维护与更新。

```txt
Debian官⽹： https://www.debian.org/ 
官⽅镜像地址： https://www.debian.org/mirror/list 
清华⼤学下载地址： https://mirrors.tuna.tsinghua.edu.cn/debian-cd/10.1.0-live/amd64/isohybrid/
```
### 1.2：Ubuntu简介
Ubuntu（友帮拓、优般图、乌班图）早期是⼀个开源的GNU/Linux操作系统，Ubuntu 是基于Debian GNU/Linux，⽀持x86、amd64（即x64）和ppc架构，由全球化的专业开发团队（Canonical Ltd）打造的，其名 称来⾃⾮洲南部祖鲁语或豪萨语的“ubuntu”⼀词，类似儒家“仁爱”的思想，意思是“⼈性”、“我的存在是因为⼤家的 存在”，是⾮洲传统的⼀种价值观， Ubuntu基于Debian发⾏版和GNOME桌⾯环境，⽽从11.04版起，Ubuntu发⾏版放弃了Gnome桌⾯环境，改为Unity，与Debian的不同在于它每6个⽉会发布⼀个新版本，Ubuntu的⽬标在 为⼀般⽤户提供⼀个最新的、同时⼜相当稳定的主要由⾃由软件构建⽽成的操作系统，Ubuntu具有庞⼤的社区⼒量，⽤户可以⽅便地从社区获得帮助，Ubuntu对GNU/Linux的普及特别是桌⾯普及作出了巨⼤贡献，由此使更多⼈共享开源的成果与精彩。
### 1.3：Ubuntu镜像下载
Ubuntu哪⾥可以下载的到呢？
```txt
http://cdimage.ubuntu.com/releases/ #ubuntu server(服务器版) 
http://releases.ubuntu.com/ #ubuntu desktop(桌⾯版) 
http://cdimage.ubuntu.com/daily-live/current/ #20.04每天镜像
```
#### 1.3.1 系统镜像间区别
https://packages.ubuntu.com/search?lang=zh-cn&arch=any&keywords=libfuse-dev
```ini
#带live，ISO镜像提供不安装就可以试⽤系统的功能 
ubuntu-18.04.3-live-server-amd64.iso 	
#不带live，不可⽤试⽤，但是可以直接进⾏系统安装
ubuntu-18.04.3-server-amd64.iso 		  
```
#### 1.3.2 不同指令集的ISO镜像
```
CPU架构			安装包标识				 备注
x86 			  i386 					  32位，server版本不再支持32位
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
Ubuntu官方帮助文档  
https://help.ubuntu.com
#### 2.2.1：更改主机名
```shell
hostnamectl set-hostname ubuntu01
```
#### 2.2.2：更改网卡名称为eth*

1. 在安装系统之后修改/etc/default/grub，或在安装系统之前传递内核参数将⽹卡名称更改为eth*

   ```shell
   vim /etc/default/grub 
   GRUB_DEFAULT=0 
   GRUB_TIMEOUT_STYLE=hidden 
   GRUB_TIMEOUT=2 
   GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian` 
   GRUB_CMDLINE_LINUX_DEFAULT=""
   GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"
   
   # 或者使用sed命令
   head -n11 /etc/default/grub
   sed -i  's/^GRUB_CMDLINE_LINUX=""$/GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"/g' /etc/default/grub
   head -n11 /etc/default/grub
   ```

2. 修改网卡配置文件： /etc/netplan/00-installer-config.yaml，将网卡名称修改为eth0之类

3. 生成启动文件

   ```shell
   # 更新grub
   update-grub
   
   # 重启
   reboot
   ```

4. 重启系统，可以看到新网卡后面跟着altname字样指向原设备名称

   ```shell
   # ip a
   eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
       link/ether 52:54:00:19:0c:33 brd ff:ff:ff:ff:ff:ff
       altname enp1s0
       inet 192.168.223.250/24 brd 192.168.223.255 scope global eth0
          valid_lft forever preferred_lft forever
       inet6 fe80::5054:ff:fe19:c33/64 scope link
          valid_lft forever preferred_lft forever
   ```

5. 临时修改网卡名称

   ```shell
   # 实际验证此操作不影响ssh连接
   # 将旧网卡关闭 && 临时更改网卡名称，服务器重启后网卡名称会还原 && 将新网卡打开
   ip link set ens160 down && ip link set ens160 name eth0 && ip link set eth0 up
   
   ```



#### 2.2.3：配置root远程登录

默认情况下，ubuntu不允许root⽤户远程ssh，如果有实际场景需要允许root⽤户远程ssh，则需要设置root密 码，并且编辑/etc/ssh/sshd_config⽂件修改如下：

```shell
vim /etc/ssh/sshd_config

PermitRootLogin yes #添加允许Root登录

PasswordAuthentication yes #打开密码认证，默认就是允许通过密码认证登录
#或者
PubkeyAuthentication yes #启用密钥认证
PasswordAuthentication no #关闭密码认证

###免交互方式更改配置
sed -i '$aPermitRootLogin yes' /etc/ssh/sshd_config        #文件末尾添加允许root登录
sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g' /etc/ssh/sshd_config     #启用密钥认证
sed -i 's/^PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config  #关闭密码认证
```

```shell
#切换到root用户环境
sudo su - root  

#交互式设置root密码
passwd 
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
#或者免交互设置root密码
echo root | passwd --stdin root

#重启ssh服务并测试root⽤户远程ssh连接
systemctl restart sshd 
```
#### 2.2.4: Ubuntu 18.04以上网络配置

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


**netplan 与 networkd、NetworkManager 之间的联系**

netplan是一个配置文件管理和生成工具，而networkd（即systemd-networkd）、NetworkManager是具体与内核交互的网络管理工具。从前需要根据不同的管理工具编写网络配置，现在 `Netplan` 将管理工具差异性给屏蔽了。 只需按照 `Netplan` 规范编写 `YAML` 配置，不管底层管理工具是啥，一份配置走天下！

**systemd-networkd**

该服务的配置文件 分别位于： 优先级 最低的 /usr/lib/systemd/network 目录、 优先级居中的 /run/systemd/network 目录、 <font color=orange>优先级最高</font>的 /etc/systemd/network 目录。

[systemd-networkd.service 中文手册](http://www.jinbuguo.com/systemd/systemd-networkd.service.html)

[Ubuntu Manpage: systemd.network - Network configuration](http://manpages.ubuntu.com/manpages/bionic/man5/systemd.network.5.html)

**NetworkManager**

**NetworkManager 由两部分组成：**

- 以超级用户运行的守护进程（network-manager ）；
- 前端管理程序（network-manager-gnome, network-manager-kde 或者 cnetworkmanager ）；



netplan官网 https://netplan.io/ 
netplan官方范例 https://netplan.io/examples/

##### 2.2.4.1: 单网卡DHCP和静态地址

```yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        enp3s0:
            dhcp4: true
```
静态地址配置，还可以提供dns，以及<font color=blue>通过默认路由来定义网关</font>。<font color=red>这里注意原来gateway4的写法已经废弃</font>。
```yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        enp3s0:
            addresses:
                - 10.10.10.2/24
            routes:
                - to: default
                  via: 10.10.10.1
            nameservers:
                search: [mydomain, otherdomain]
                addresses: [10.10.10.1, 1.1.1.1]
```
静态地址示例
```yaml
# This is the network config written by 'subiquity'
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: false
      dhcp6: false
      addresses:
        - 192.168.223.250/24
      routes:
        - to: default
          via: 192.168.223.1
      nameservers:
        addresses:
          - 61.177.7.1
          - 114.114.114.114
```

##### 2.2.4.2: 多网卡DHCP

```yaml
network:
    version: 2
    ethernets:
        enred:
            dhcp4: true
            dhcp4-overrides:
                route-metric: 100
        engreen:
            dhcp4: true
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
      dhcp4: false
      dhcp6: false
      addresses: [172.18.3.18/16]
      gateway4: 172.18.0.1
      nameservers:
        addresses: [223.6.6.6]
    eth1:
      dhcp4: false
      dhcp6: false
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
      dhcp4: false
      dhcp6: false
  bridges:
    br0:
      dhcp4: false
      dhcp6: false
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
        eth0:
            dhcp4: false
    bridges:
        br0:
            dhcp4: true
            interfaces:
                - eth0
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
  	  dhcp4: false
  	  dhcp6: false
  	eth1:
  	  dhcp4: false
  	  dhcp6: false
  bridges:
  	br0:
  	  dhcp4: false
  	  dhcp6: false
  	  addresses: [172.18.3.18/16]
  	  gateway4: 172.18.0.1
  	  nameservers:
  	  	addresses: [61.177.7.1]
  	  interfaces:
  	  	- eth0
  	br1:
  	  dhcp4: false
  	  dhcp6: false
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
第⼀种模式：mod=0，即：(balance-rr) Round-robin policy（平衡抡循环策略） 特点：传输数据包顺序是依次传输（即：第1个包⾛eth0，下⼀个包就⾛eth1….⼀直循环下去，直到最后⼀个传输完毕），此模式提供负载平衡和容错能⼒。

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
  	  dhcp4: false
  	  dhcp6: false
  	eth1:
  	  dhcp4: false
  	  dhcp6: false

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
  	  dhcp4: false
  	  dhcp6: false
  	eth1:
  	  dhcp4: false
  	  dhcp6: false

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
  	  dhcp4: false
  	  dhcp6: false
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
  	  dhcp4: false
  	  dhcp6: false
  	eth1:
  	  dhcp4: false
  	  dhcp6: false
  	eth2:
  	  dhcp4: false
  	  dhcp6: false
  	eth3:
  	  dhcp4: false
  	  dhcp6: false

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
  	  dhcp4: false
  	  dhcp6: false
  	eth1:
  	  dhcp4: false
  	  dhcp6: false
  	eth2:
  	  dhcp4: false
  	  dhcp6: false
  	eth3:
  	  dhcp4: false
  	  dhcp6: false

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
  	  dhcp4: false
  	  dhcp6: false
  	  addresses: [172.18.3.18/16]
  	  gateway4: 172.18.0.1
  	  nameservers:
  	  	addresses: [61.177.7.1,114.114.114.114]
  	  interfaces:
  	  	- bond0
  	br1:
  	  dhcp4: false
  	  dhcp6: false
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
                dhcp4: true
```

##### 2.2.4.12: WPA 个人无线网络

```yaml
network:
    version: 2
    renderer: networkd
    wifis:
        wlp2s0b1:
            dhcp4: false
            dhcp6: false
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

#### 2.2.5：VNC访问图形界面

##### 2.2.5.1：安装软件包

```shell
#更新软件源
apt update

#安装桌面环境所需的软件包。软件包包括系统面板、窗口管理器、文件浏览器、终端等桌面应用程序。
apt install gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal ubuntu-desktop -y 

#Ubuntu18.04运行如下命令安装vnc
apt-get install vnc4server -y

#Ubuntu20.04运行如下命令安装vnc
apt-get install tightvncserver -y

#命令启动vnc服务
vncserver
```

##### 2.2.5.2：初始化VNC并配置

第一次启动需要设置VNC的登录密码，输入VNC登录密码和确认密码，并在以下提示中输入n，并按Enter。**注意：** `如果您自定义的密码位数大于8位，系统默认只截取前8位作为您的VNC登录密码。`

<img src="https://raw.githubusercontent.com/9206284/Linux/main/img/vncserver-init.png" alt="vncserver-init" style="zoom:50%;" />

命令行回显如下图所示的信息时，表示VNC启动成功。

<img src="https://raw.githubusercontent.com/9206284/Linux/main/img/vncserver-init-success.png" alt="vncserver-init-success" style="zoom:50%;" />

备份vnc的xstartup配置文件

```shell
cp ~/.vnc/xstartup{,.bak}

```

修改vnc的xstartup配置文件并保存

```shell
export XKL_XMODMAP_DISABLE=1
export XDG_CURRENT_DESKTOP="GNOME-Flashback:GNOME"
export XDG_MENU_PREFIX="gnome-flashback-"
gnome-session --session=gnome-flashback-metacity --disable-acceleration-check &
```

##### 2.2.5.3：重启vnc服务

- 关闭已启动的VNC

  ```bash
  vncserver -kill :1
  ```

- 启动一个新的VNC，端口号仍然是1

  ```shell
  vncserver -geometry 1024X768 :1
  ```

##### 2.2.5.4：使用vnc viewer工具访问

[[ realvnc客户端下载 ]](https://www.realvnc.com/en/connect/download/viewer/)

##### 2.2.5.5：通过SSH隧道访问VNC桌面（有用）

- 远程服务器开启VNC服务

  SSH 登录远程服务器，在命令行执行 `vncserver -geometry 1024x768` 来创建会话，输出的最下面的 `:n` (n为一个整数) 就是你创建的会话 ID，初次执行 vncserver 需要创建一个 VNC 登录密码。

  示例如下，此时会话 ID 为 `1`，则 VNC 实际端口为 `5901`。

  ```bash
  root@kvm-223-69:~# vncserver -geometry 1024x768 :1
  
  New 'X' desktop is kvm-223-69:1
  
  Starting applications specified in /root/.vnc/xstartup
  Log file is /root/.vnc/kvm-223-69:1.log
  ```

- 建立SSH隧道

  远程开启 VNC 桌面后，若为外网访问，且内网服务器只开放了 SSH 端口，可通过 SSH 隧道进行端口转发登录。若 VNC 桌面会话 ID 为 `1`，则其实际端口为 `5901`，于是将远程的 `5901` 端口映射到本地的 `8888` 端口如下。

  ```bash
  # ssh -N -L <local port>:localhost:<remote port> <SSH hostname>
  # SSH hostname: -p SSH_Port username@Host_IP
  # -N  仅仅只用来转发,而不执行远程命令。
  # -L  进行本地端口转发
  ssh -N -L 8888:localhost:5901 -p 59783 saw@58.211.18.122
  ```

- VNC客户端通过 <u>本地端口</u> 访问远程桌面 

  在 VNC Viewer 里面，填入`本地服务器地址:本地端口`，如 `localhost:8888`，输入第一步创建的密码即可登陆图像界面，实现通过访问本地 `8888` 端口登录 VNC 远程桌面的目的。

#### 2.2.6：普通用户sudo免密码

```bash
#以下ubuntu两处均需修改，否则无效

# User privilege specification
root    ALL=(ALL:ALL) ALL
saw     ALL=(ALL:ALL) NOPASSWD: ALL      #针对单独用户修改这里
# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL       						#针对所有sudo用户修改这里，这里未做修改为默认配置
saw  ALL=(ALL:ALL) NOPASSWD: ALL      		#针对单独用户修改这里
```

#### 2.2.7：启用crontab计划日志

ubuntu默认没有开启crontab的计划日志，在/var/log/目录下是不存在cron.log。

修改rsyslog配置文件 /etc/rsyslog.d/50-default.conf

```bash
#将cron前面的注释符去掉 
cron.*              /var/log/cron.log 

#sed命令一行搞定
sed -i 's/^#cron.\*/cron.*/g' /etc/rsyslog.d/50-default.conf  && systemctl restart rsyslog && systemctl status rsyslog
```

systemctl  restart  rsyslog

#### 2.2.8：修改时区、24小时、时间同步

```shell
###修改时区，两种方法
# 方法1:
timedatectl set-timezone Asia/Shanghai
# 方法2:
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

###修改24小时 /etc/default/locale
cat /etc/default/locale 
cat <<EOF >>/etc/default/locale 
LC_TIME=C.UTF-8
EOF
cat /etc/default/locale 

###时间同步，使用计划任务
# 方法1直接添加root的计划任务，
tail -n1 /var/spool/cron/crontabs/root
echo '*/5 *  *   *   *  /usr/sbin/ntpdate time1.aliyun.com &> /dev/null && /sbin/hwclock -w &> /dev/null' >>/var/spool/cron/crontabs/root
tail -n2 /var/spool/cron/crontabs/root
# 方法2使用命令 crontab -e，编辑计划任务
*/5 *  *   *   *  /usr/sbin/ntpdate time1.aliyun.com &> /dev/null && /sbin/hwclock -w &> /dev/null
```

#### 2.2.9：配置pppoe拨号

```shell
#安装pppoe套件
apt install pppoeconf

#PPPOE设定，执行此程序会扫描服务器上网卡线路，此时应保证已连接上PPPOE线路。
pppoeconf

#接着选择“YES”——是否使用常用PPPoe拨号选项，然后输入账号，输入密码，再次选择“YES”——将网络供应商的DNS加入 DNS resolv列表中，“YES”——使用预设的MSS值，“YES”——开机是否直接進行拨号上网，“YES”——现在是否拨号上网。
```

```shell
#手动拨号
pon dsl-provider

#中断连线
poff -a

#确认拨号状态及IP
plog #查看状态
ip address show ppp0 #查看IP
```

#### 2.2.10：设置HISTORY记录时间戳并清空日志

```shell
#设置HISTORY记录时间戳
echo 'export HISTTIMEFORMAT="%F %T  "' >> /etc/profile

#清空日志
 rm -rf /var/log/journal/*
 rm -f  /var/log/dmesg.*
 rm -f /var/log/apt/*
 > /var/log/btmp
 > /var/log/wtmp
 > /var/log/lastlog
 > /var/log/syslog
 > /var/log/auth.log
 > /var/log/faillog
 > /var/log/dmesg
 > /var/log/dpkg.log
 > /root/.bash_history
 > /root/.bash_logout
 > /home/saw/.bash_history
 history -c
```

#### 2.2.11：关闭swap

```shell
# 临时关闭swap
swapoff -a

# 永久关闭swap分区
sed -i 's@\/swap.img@#/swap.img@g' /etc/fstab

# 检查swap关闭情况
free -g

```



### 2.3: Ubuntu软件包管理

ubuntu安装、升级、卸载软件包等常规操作。

#### 2.3.1: 可选软件仓库

中科⼤：http://mirrors.ustc.edu.cn/help/ubuntu.html

```shell
#备份原仓库源
cp /etc/apt/sources.list{,.bak}


#下面替换命令根据实际情况archive.ubuntu.com还是cn.archive.ubuntu.com进行命令替换，这里替换后使用的中科大源。
sed -i 's/cn.archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list && apt update
# sudo sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list


# 下面使用清华镜像源仓库，适用于 Ubuntu18.04
cat <<EOF > /etc/apt/sources.list
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
EOF


# 下面使用清华镜像源仓库，适用于 Ubuntu20.04
cat <<EOF > /etc/apt/sources.list
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
EOF

# 下面使用清华镜像源仓库，适用于 Ubuntu22.04
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse


# 下面使用阿里云源仓库，适用于 Ubuntu20.04
cat <<EOF > /etc/apt/sources.list
#  阿里源 ubuntu 20.04(focal)
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
EOF
```

阿⾥云仓库地址：https://opsx.alibaba.com/mirror 

清华⼤学：https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/ 

华为：https://mirrors.huaweicloud.com/

#### 2.3.2: Apt包管理工具

```shell
# apt list #apt列出仓库软件包，等于yum list 
# apt search NAME #搜索安装包 等于 yum search 
# apt show apache2 #查看某个安装包的详细信息  等于 yum info
# apt install apache2 #在线安装软件包  等于 yum install
# apt remove apache2 #卸载单个软件包但是保留配置⽂件 等于 yum remove
# apt autoremove apache2 #删除安装包并解决依赖关系 等于 yum autoremove
# apt update #更新本地软件包列表索引，修改了apt仓库后必须执⾏ 等于 yum update
# apt purge apache2 #卸载单个软件包并且删除配置⽂件 
# apt upgrade #升级所有已安装且可升级到新版本的软件包 
# apt full-upgrade #升级整个系统，必要时可以移除旧软件包。 
# apt edit-sources #编辑source源⽂件 等于 vim /etc/apt/sources.list

# apt-cache madison nginx #查看仓库中软件包有哪些版本可以安装  yumm list --showduplicates
# apt install nginx=1.18.0-0ubuntu1.2  #安装软件包的时候指定安装具体的版本

# apt-file 等于 yum whatprovides，需要使用命令：apt install apt-file 安装之后才可使用
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
#jdk所在目录
export JAVA_HOME=/usr/local/jdk
#jre所在目录 
export PATH=\$JAVA_HOME/bin:\$JAVA_HOME/jre/bin:\$PATH
export CLASSPATH=.\$CLASSPATH:\$JAVA_HOME/lib:\$JAVA_HOME/jre/lib:\$JAVA_HOME/lib/tools.jar
EOF

#重新导⼊环境变量
source /etc/profile 

#查看当前系统正在使用的java版本
java -version

# 发现并不是新安装的jdk版本，使用命令更改当前系统使用的jdk版本
update-alternatives --config java

# 如没有新安装的1.8.0版本jdk，使用命令将新安装的jdk放入到java bin中
# --install <link> <name> <path> <priority>
update-alternatives --install /usr/bin/java java /usr/local/jdk/bin/java 2

# 移除
update-alternatives --remove java /usr/local/jdk/bin/java

```
#### 2.3.4: 安装JDK
```shell
# 1. oracle jdk
# oracle下载 jdk的二进制包
# https://www.oracle.com/java/technologies/downloads/
sudo mkdir -p /usr/lib/jvm  #为 JDK 建立一个目录
sudo tar zxvf jdk-version-linux-x64.tar.gz -C /usr/lib/jvm   #解压安装JDK
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.8.0_version/bin/java" 1 #告知系统有一个可用的新版本
sudo update-alternatives --set java /usr/lib/jvm/jdk1.8.0_version/bin/java #设置新jdk为默认
java -version  #验证版本


# 2. openjdk
apt install openjdk-8-jdk
```
#### 2.3.5: 安装常用软件
```shell
#卸载软件（20.04版本）
apt update && apt purge ufw lxd lxd-client lxcfs lxc-common -y

#安装软件(20.04版本)
apt update && apt-get install iproute2 ntpdate tcpdump telnet traceroute lrzsz tree openssl libssl-dev libpcre3 libpcre3-dev gcc openssh-server zlib1g-dev iotop unzip zip net-tools chrony nfs-common -y && apt autoremove -y

# nfs-kernel-server nfs-common

#卸载软件（22.04版本）
apt-get purge ufw lxcfs liblxc-common -y
#安装软件（22.04版本）
apt-get install iproute2 ntpdate tcpdump telnet traceroute lrzsz tree openssl libssl-dev libpcre3 libpcre3-dev gcc openssh-server zlib1g-dev iotop unzip zip net-tools chrony nfs-common -y && apt autoremove -y
安装时  systemd-timesyncd 安装包将被移除，应该是与ntpdate冲突了
```
#### 2.3.6: 系统资源限制优化 ✅
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
#### 2.3.7: 内核参数优化 ✅
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
#### 2.3.8: dpkg包管理器
rpm：RPM(Red Hat Package Manager)，是基于Red hat的Linux Distribution的包管理系统，同时也指rpm包本 身，RPM⽤于rpm包的管理（诸如安装、卸载、升级等）

"dpkg "是"Debian Packager "的简写,为 "Debian"专⻔开发的套件管理系统，⽅便软件的安装、更新及移除。所有 源⾃“Debian”的“Linux ”发⾏版都使⽤ “dpkg”，例如 “Ubuntu”、“Knoppix ”等。
```shell
# dpkg -i gitlab-ce_11.9.8-ce.0_amd64.deb #安装某个软件包 
# dpkg -r gitlab-ce #删除某个软件包保留配置⽂件 
# dpkg -r -P gitlab-ce #删除某个软件包不保留配置⽂件 
# dpkg -I gitlab-ce_11.9.8-ce.0_amd64.deb #查看软件包信息 
# dpkg -c gitlab-ce_11.9.8-ce.0_amd64.deb #查看软件包内的⽂件及⽬录内容 
# dpkg -l #列出本机已经安装的所有软件
# dpkg -L #列出已安装的指定包名的所有文件

```
#### 2.3.9: 安装python2和python3并设置默认 ✅

以下以20.04版本为例

```shell
# 1.查看当前系统可用python版本
ls /usr/bin/python* 

# 2. 安装python2
apt update
apt install python2

# 3. 查看是否已有一些python的供选方案
update-alternatives --list python
# 在这一步中，将设置两个 Python 版本方案，分别由 Python2 和 Python3 执行。命令：
update-alternatives --install /usr/bin/python python /usr/bin/python2 1
update-alternatives --install /usr/bin/python python /usr/bin/python3 2
# 查看python的可选方案
update-alternatives --list python
/usr/bin/python2
/usr/bin/python3

# 更改python 版本。例如要更改为 Python2，请执行以下命令，将选择通过上下键移动或者直接输入数字
update-alternatives --config python
There are 2 choices for the alternative python (providing /usr/bin/python).

  Selection    Path              Priority   Status
------------------------------------------------------------
* 0            /usr/bin/python3   2         auto mode
  1            /usr/bin/python2   1         manual mode
  2            /usr/bin/python3   2         manual mode

Press  to keep the current choice[*], or type selection number: 1

# 验证python版本
python -V
```

#### 2.3.10：安装pip2和pip3

```shell
# 1.下载与python版本相同的pip脚本
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip2.7.py #python2.7
curl https://bootstrap.pypa.io/pip/3.6/get-pip.py -o get-pip3.8.py  #python3.8

# 2.运行安装脚本
/usr/bin/python2  get-pip2.7.py
/usr/bin/python3  get-pip3.8.py

# 3. 验证：手动切换python版本之后，对应pip版本也会改变
python --version
pip --version

# 4. pip仓库加速
mkdir ~/.pip
cat<<EOF > ~/.pip/pip.conf 
[global]
timeout = 600
index.url = http://mirrors.aliyun.com/pypi/simple
trusted-host = mirrors.aliyun.com
EOF
```

#### 2.3.11：iptables 持久化

Debian系发行版默认不开启iptables。正常情况，写入的 iptables规则将在系统重启时消失，即使使用iptables-save命令将iptables规则存储到文件，在系统重启后也需要执行iptables-restore操作来恢复原有规则。

这里有一个更好的iptables持久化方案，即`netfilter-persistent`工具，<font color=brown>`netfilter-persistent`</font> 加载 `ipset-persistent` 和 `iptables-persistent` 保存的规则。

- **静默安装**：  ipset-persistent 和 iptables-persistent 安装过程中会弹出交互窗，选择是否自动保存规则。在安装前，用 `debconf-set-selections` 在 debconf 数据库中插入对应的内容，即可实现无交互静默安装。

  ```shell
  # debconf-set-selections 配置语法
  {包名} {配置项key} {配置项类型} {配置项value}
  
  # 1. 生成的配置如下
  debconf-set-selections <<EOF
  ipset-persistent ipset-persistent/autosave boolean true
  iptables-persistent iptables-persistent/autosave_v4 boolean true
  iptables-persistent iptables-persistent/autosave_v6 boolean true
  EOF
  
  # 2. 执行安装
  apt install netfilter-persistent ipset-persistent iptables-persistent -y 
  
  # 3. 开机自启动
  systemctl enable netfilter-persistent
  systemctl start netfilter-persistent
  
  # 4. 手动保存规则
  netfilter-persistent save
  #iptables持久化保持的文件在  /etc/iptables/rules.v4 和  /etc/iptables/rules.v6
  #
  ```

- Ubuntu 上软件包安装配置项归 `debconf` 管理，在一台机器上交互安装完成后，用 `debconf-show` 查看软件包的配置项。

  ```shell
  debconf-show ipset-persistent
  * ipset-persistent/autosave: true
  
  debconf-show iptables-persistent
  * iptables-persistent/autosave_v4: true
  * iptables-persistent/autosave_v6: true
  
  ```

clst自定义规则

```shell
#目标是限制SSH来源

iptables -F INPUT
iptables -N SSH-IN 
iptables -A SSH-IN -s 221.224.170.222/32 -m comment --comment "from office 7-8" -j ACCEPT 
iptables -A SSH-IN -s 192.168.1.0/24 -m comment --comment "from lan access" -j ACCEPT 


iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -i lo -m comment --comment "Allow loopback" -j ACCEPT
iptables -A INPUT -p tcp -m tcp --dport 22 -m state --state NEW -m comment --comment "SSH Control" -j SSH-IN
iptables -A INPUT -p icmp -m comment --comment "allow ICMP" -j ACCEPT

iptables -A SSH-IN -m comment --comment "Default reject rule" -j REJECT --reject-with icmp-port-unreachable 
iptables -A INPUT -m comment --comment "Default reject" -j REJECT --reject-with icmp-port-unreachable 

netfilter-persistent save
```

DNAT规则

```shell
# 宿主机有外网br0口，内网br1口
# 虚拟机部署了kubefate服务，桥接内网br1口，地址192.168.1.100，需要向几个固定来源地址暴露 9370/8080/8350/20000 端口，即DNAT
# 以下命令均在宿主机执行，首先是DNAT
iptables -t nat -A PREROUTING -s 36.111.134.13/32 -p tcp -m tcp --dport 9370 -j DNAT --to-destination 192.168.1.100:9370
iptables -t nat -A PREROUTING -s 221.224.170.222/32 -p tcp -m tcp --dport 9370 -j DNAT --to-destination 192.168.1.100:9370
iptables -t nat -A PREROUTING -s 122.193.30.34/32 -p tcp -m tcp --dport 8080 -j DNAT --to-destination 192.168.1.100:8080
iptables -t nat -A PREROUTING -s 122.193.30.34/32 -p tcp -m tcp --dport 8350 -j DNAT --to-destination 192.168.1.100:8350
iptables -t nat -A PREROUTING -s 122.193.30.34/32 -p tcp -m tcp --dport 20000 -j DNAT --to-destination 192.168.1.100:20000

# 同时虚拟机还需要借用宿主机的br0口能上网，需要做SNAT
# 所有来源地址192.168.1.0/24的上网做地址伪装
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o br0 -j MASQUERADE

# 查看验证nat表
iptables -L -n --line -t nat
iptables -t nat -vnL
```





一键清除iptables规则

```shell
iptables -F
iptables -X
iptables -Z
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
```

清除docker的规则和链，<font color=red>清理之前需要停止相关docker服务</font>。

```shell
# iptables -t nat -F DOCKER: 清除 DOCKER 链中的所有规则。
# iptables -t nat -X DOCKER: 删除 DOCKER 链。
iptables -t nat -F DOCKER
iptables -t nat -X DOCKER

# 单独处理NAT表中PREROUTING、OUTPUT、POSTROUTING的规则链
iptables -t nat -D PREROUTING -m addrtype --dst-type LOCAL -j DOCKER
iptables -t nat -D OUTPUT ! -d 127.0.0.0/8 -m addrtype --dst-type LOCAL -j DOCKER
iptables -t nat -D POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE

```



#### 2.3.12：tftp服务器安装

```shell
# 安装
apt update &&  apt install tftpd-hpa

# 配置，编辑配置文件 /etc/default/tftpd-hpa
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/srv/tftp"
TFTP_ADDRESS="0.0.0.0:69"
#TFTP_OPTIONS="--secure"
TFTP_OPTIONS="-l -c -s"

# 修改目录权限
chmod -R 777 /var/lib/tftpboot

# 重启服务生效配置,需要知道的是tftp工作在udp 69端口
systemctl restart tftpd-hpa

```





### 2.4：关闭自动更新与版本滚动升级

```shell
apt update -y && apt upgrade -y && apt autoremove -y
do-release-upgrade
```

```shell
#查看已有内核
dpkg --list|grep linux-image
dpkg --list|grep linux-headers
#查看当前使用内核版本
uname -r

#卸载内核
apt purge linux-image-xxx #xxx 表示版本数字
apt purge linux-image-5.4.0-100-generic
apt purge linux-headers-xxx
apt autoremove #自动删除不用的软件包
update-grub    #卸载完内核后需要执行下列命令更新gurb

#关闭内核的自动更新
---命令方式
apt-mark hold linux-image-5.15.0-56-generic
apt-mark hold linux-image-generic
apt-mark hold linux-headers-generic
apt-mark hold linux-headers-5.15.0-56-generic
apt-mark hold linux-headers-5.15.0-56
---修改文件方式
vim /etc/apt/apt.conf.d/10periodic
APT::Periodic::Update-Package-Lists "0";
APT::Periodic::Download-Upgradeable-Packages "0";
APT::Periodic::AutocleanInterval "0";
vim /etc/apt/apt.conf.d/20auto-upgrades
APT::Periodic::Update-Package-Lists "0";
APT::Periodic::Unattended-Upgrade "0";
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

18、安装桌面
sudo apt install xinit ubuntu-desktop
#输入命令 startx 进入图形界面, 图形切回命令行 ctrl + alt + F7
#开机默认进入命令行模式 	sudo systemctl set-default multi-user.target 

#开机默认进入图形模式  sudo systemctl set-default graphical.target 
#进入命令行模式 ctrl + alt + F2，从命令行切换到图形界面：ctrl + alt + F7。

```