[TOC]

# Linux KVM 虚拟化

## 一、虚拟化基础

### 1.1: 传统上线流程

> 服务器选型及采购 
> IDC选择 
> 服务器系统选择、系统安装、上架
> 应⽤规划及部署 
> 域名选择及注册 
> DNS映射
> 测试外⽹访问

**传统方案面临的问题**
>服务器资源利⽤率低下，CPU 、内存等不能共享 
>资源分配不合理 
>初始化成本⾼ 
>⾃动化能⼒差 
>集群环境需要⼤量的服务器主机

### 1.2: 什么是虚拟化和虚拟机

#### 1.2.1: 虚拟化：

在计算机技术中，虚拟化（Virtualization）是⼀种 <font color=violetred>**资源管理技术**</font>，是将计算机的各种实体资源（CPU、内存、磁盘空间、⽹络适配器等），予以抽 象、转换后呈现出来并可供分割、组合为⼀个或多个计算机配置环境，并重新分割、重新组合，以达到最大化合理利用物理资源的目的。

#### 1.2.2: 虚拟机：

虚拟计算机称为“虚拟机”(VM，Virtual Machine)，它是⼀种 **严密隔离且内含操作系统和应⽤的软件容器**。每个⾃包含虚拟机都是完全独⽴的。通过将多台虚拟机放置在⼀台计算机上，可仅在⼀台物理服务器或“主机” 上运⾏多个操作系统和应⽤，名为 “**hypervisor**” 的精简软件层可将虚拟机与主机分离开，并根据需要为每个虚拟机动态分配计算资源。

#### 1.2.3: 虚拟化类型：

- 1: 服务器虚拟化：

> 1. 物理资源抽象成逻辑资源
> 2. 一台服务器变成多台，相互独立的虚拟服务器
> 3. 不局限物理的界限
> 4. 让硬件变成动态管理的资源池
> 5. 提高利用率，简化系统管理

- 2: 网络虚拟化：

  通过 **软件定义⽹络**，即⽹络的创建不再依赖于物理设备，如公有云⼚商允许⽤⼾⾃⼰创建新的⽹络，在kubernetes 、openstack 中都会使⽤到⽹络虚拟化。

> 1. 一个物理网络支持多个逻辑网络
> 2. 保留了网络设计中原有的层次结构、数据通道和所能提供的服务
> 3. 使得最终用户的体验和独享物理网络一样
> 4. 提高了网络资源的利用率

- 3：桌面虚拟化：

> 1. 将计算机的终端系统进行虚拟化
> 2. 达到桌面使用的安全性和灵活性
> 3. 任何设备时间地方都能通过网络访问属于个人的桌面系统
> 4. 并非本地操作系统提供的桌面

- 4: 应用虚拟化：

> 1. 将应用程序与操作系统解耦
> 2. 为应用程序提供了一个虚拟的环境（可执行文件+运行环境）
> 3. 本质是把应用程序对底层的系统和硬件的依赖抽象出来，可以解决程序版本不兼容的问题
> 4. 也在后台的数据中心里面

- 5: 存储虚拟化：

  如SAN / NAS(NFS/Samba) / Gluster / Ceph等

> 1. 将异构的存储资源组成一个巨大的存储池
> 2. 对于用户，透明化了底层的磁盘磁带，直接使用存储资源即可
> 3. 管理变得方便，根据需要把存储资源分配给各个应用

- 6: 库虚拟化：

> 在Linux上运行windows程序使用 Wine，在 Mac上使用Windows程序使用 CrossOver等。

- 7: 容器虚技术

> 被称为下⼀代虚拟化技术，典型的就是docker 、 Linux Container(LXC) 、pouch、RKT

#### 1.2.4: 虚拟化技术类型：

##### 1.2.4.1：全虚拟化 full virtualization /准虚拟化 native virtualization:

只对CPU 和 内存 做相应的分配等操作，全虚拟化需要物理硬件支持，例如 Intel 的 Intel VT-X/EPT ，AMD的 AMD-V/RVI，分别在CPU层面和内存层面支持虚拟化技术。因此全虚拟化是基于硬件辅助的虚拟化技术。

> **全虚拟化软件有**：
> vmware workstation
> vmware esxi
> paralles desktop
> KVM
> Microsoft Hyper-V
> virtualbox

##### 1.2.4.2:  半虚拟化 para virtualization：

半虚拟化要求 Guest OS的内核是知道自己运行在虚拟化环境中，因此 Guest OS的系统架构必须和宿主机的系统架构相同，并且要求对 Guest OS的内核做相应的修改，因此半虚拟化只支持开源内核的系统，不支持闭源的系统。早起的Xen是半虚拟化，但从3.0版本开始（http://www-archive.xenproject.org/files/xen_3.0_datasheet.pdf，可以支持硬件虚拟化技术，实现了全虚拟化，可在其平台上不加修改的直接运行如Linux /Windows 等操作系统。

>  **半虚拟化软件有**：
> Xen

#### 1.2.5: Hypervisor类型：

#### 1.2.6: 虚拟化技术厂商:

#### 1.2.7: 云计算

云计算以虚拟化为基础，以网络为中心，为用户提供数据存储和网络计算服务，包括所需要的硬件、平台、软件及服务等资源。

虚拟化与云计算的对比:

| 项目     | 虚拟化                              | 云                                                    |
| -------- | ----------------------------------- | ----------------------------------------------------- |
| 定义     | 技术                                | 方法                                                  |
| 目的     | 从1个物理硬件系统创建多个模拟环境   | 汇聚并自动化分配虚拟资源以供按需使用                  |
| 用途     | 针对具体用途为特定用户提供打包资源  | 针对多种用途为用户群组提供不同的资源                  |
| 配置     | 基于镜像                            | 基于模版                                              |
| 成本     | 资本支出(CAPEX)高、运营支出(OPEX)低 | 私有云:CAPEX高、OPEX低，<br />公有云：CAPEX低、OPEX高 |
| 可扩展性 | 纵向扩展                            | 横向扩展                                              |
| 使用场景 | 少量服务器的环境                    | 众多服务器的大环境                                    |



##### 1.2.7.1： 云计算分类

> 公有云：aws、azure、阿里云、腾讯云等，不需要关心底层硬件，但数据安全需要考虑。
>
> 私有云：自建openstack、vmware等环境。
>
> 混合云：既使用公有云，又使用私有云。

##### 1.2.7.2： 云计算分层

> 传统IDC：直接在物理机上运行服务，业务不能快速横向扩容。
>
> Iaas：基础设施服务，Infrastructure as a service，自建机房的openstack，阿里云ecs
>
> Paas：平台服务，Platform as a service 公有云上的Redis，RDS、Docker等服务
>
> Saas：软件服务，Software as a service 企业邮箱，OA系统等可以通过浏览器直接访问

## 二、虚拟化技术之KVM

KVM 是 Kernel-based Virtual Machine的简称，是一个开源的系统虚拟化模块，自Linux内核 2.6.20之后的各主要发行版中，KVM目前已成为学术界的主流 **VMM(virtual machine monitor，虚拟监视器，也称 hypervisor)** 之一。

KVM 是针对Linux的完全虚拟化解决方案，基于内核的虚拟机。它在 X86硬件上包含虚拟化扩展(Intel VT或 AMD-V)。它由提供核心虚拟化基础架构的可加载内核模块 **kvm.ko** 和 **处理器特定模块**  kvm-intel.ko 或 kvm-amd.ko 组成。

### 2.1: 宿主机环境准备

2.1.1: CPU 指令集

- X86/X86_64架构，Intel 和 AMD之间相互授权，CISC复杂指令集代表
- ARM架构，aarch64
- POWER架构
- RISC-V架构，RISC精简指令集代表

```shell
ubuntu@ip-172-31-25-4:~$  lscpu
Architecture:                    x86_64
CPU op-mode(s):                  32-bit, 64-bit
Byte Order:                      Little Endian
Address sizes:                   46 bits physical, 48 bits virtual
CPU(s):                          1
On-line CPU(s) list:             0
Thread(s) per core:              1
Core(s) per socket:              1
Socket(s):                       1
NUMA node(s):                    1
Vendor ID:                       GenuineIntel
CPU family:                      6
Model:                           63
Model name:                      Intel(R) Xeon(R) CPU E5-2676 v3 @ 2.40GHz
Stepping:                        2
CPU MHz:                         2399.844
BogoMIPS:                        4800.00
Hypervisor vendor:               Xen
Virtualization type:             full
```


#### 2.1.2: 开启CPU虚拟化

KVM 需要宿主机CPU支持虚拟化，打开VT-x/EPT或者 AMD-V/RVI(v) 功能。

```shell
#验证 Linux系统开启kvm虚拟化
grep -E "vmx | svm" /proc/cpuinfo | wc -l 
```



#### 2.1.3 安装KVM工具包

**Ubuntu 20.04**

```shell
# 安装kvm工具包
saw@ubuntu-kvm:~$ sudo  apt install qemu-kvm virt-manager libvirt-daemon-system -y

# 验证是否已支持kvm
saw@ubuntu-kvm:~$ kvm-ok
INFO: /dev/kvm exists
KVM acceleration can be used

# 通过查看网卡设备是否生成NAT虚拟网卡验证
saw@ubuntu-kvm:~$  ifconfig 

# 查看 libvirtd 服务的配置文件，可以通过修改 /etc/libvirt/qemu/networks/default.xml 修改虚拟机 IP范围
saw@ubuntu-kvm:~$  sudo systemctl status libvirtd
saw@ubuntu-kvm:~$ sudo grep "192.122.1.1" /etc/libvirt/ -R
/etc/libvirt/qemu/networks/default.xml:  <ip address='192.122.1.1' netmask='255.255.255.0'>
/etc/libvirt/qemu/networks/autostart/default.xml:  <ip address='192.122.1.1' netmask='255.255.255.0'>
```

**CentOS**

```shell
# yum install qemu-kvm qemu-kvm-tools libvirt libvirtclient virt-manager virt-install 
# systemctl start libvirtd 
# systemctl enable libvirtd
```

**KVM管理工具**

> **libvirt**：
> 使用得最多的 KVM管理工具和应用程序接口，通过 libvirt 调用KVM创建虚拟机，libvirt 是KVM通用的访问API，不但能管理KVM，还能管理VMware、Xen、Hyper-V、virtualBox等虚拟化方案。
>
> **virsh**：
> virsh 是一个 **管理KVM的命令行工具**，底层调用 **libvirt API**。
>
> **virt-manager**：
> virt-manager是一个 **管理KVM的图形工具**，底层调用 **libvirt API** 来完成对虚拟机的操作，包括虚拟机的创建、删除、启动、停止以及一些简单的监控功能等。
>
> **openstack**：
> openstack是一个开源的 **虚拟化编排工具**，常用于构建大规模的虚拟化环境，用于管理成千上万虚拟机的创建、启动、删除等整个生命周期。

## 三、KVM实战案例

qemu-img  create [--object objectdef] [-q] [-f fmt] [-b backing_file] [-F backing_fmt] [-u] [-o options] filename [size]

```shell
# libvirtd 默认保存虚拟机磁盘的路径
root@ubuntu-kvm:~# ll /var/lib/libvirt/images/ 

# 创建一个格式为 raw 大小10G 的裸磁盘，大小即为 10G
root@ubuntu-kvm:~# qemu-img create -f raw /var/lib/libvirt/images/CentOS-7-x86_64.raw 10G
Formatting '/var/lib/libvirt/images/CentOS-7-x86_64.raw', fmt=raw size=10737418240

# 创建一个格式为 qcow2 大小10G 的稀疏格式磁盘，大小为 193K
root@ubuntu-kvm:~# qemu-img create -f qcow2 /var/lib/libvirt/images/centos.qcow2 10G
Formatting '/var/lib/libvirt/images/centos.qcow2', fmt=qcow2 size=10737418240 cluster_size=65536 lazy_refcounts=off refcount_bits=16
root@ubuntu-kvm:~# ll -h /var/lib/libvirt/images/centos.qcow2
-rw-r--r-- 1 root root 193K Feb 14 09:52 /var/lib/libvirt/images/centos.qcow2
```

```shell
root@ubuntu-kvm:~#    virt-install --virt-type kvm --name centos7 --ram 1024 --vcpus 2 --cdrom=/usr/local/src/CentOS-7.5-x86_64-Minimal-1804.iso  --disk path=/var/lib/libvirt/images/CentOS-7-x86_64.raw --network network=default --graphics vnc,listen=0.0.0.0 -noautoconsole
```

一堆qumu-kvm程序运行的虚拟机，莫要kill 

### 3.1： 创建 NAT 网络虚拟机

### 3.2：创建 Bridge 网络虚拟机

