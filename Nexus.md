这里使用的ubuntu 20.04，分区时可以单独建立/data目录。



### 1. 安装OpenJDK 8

```shell
java -version
apt install openjdk-8-jre-headless -y
```

### 2. 安装Maven

[[ 官方下载地址 ]](https://maven.apache.org/download.cgi)

```shell
mkdir -p /data/maven

# 在 /usr/local/src 目录下下载解压，并做好软链
cd /usr/local/src/
wget https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.8.5/binaries/apache-maven-3.8.5-bin.tar.gz
tar -zxvf apache-maven-3.8.1-bin.tar.gz
ln -sv /usr/local/src/apache-maven-3.8.5  /usr/local/maven

# 配置系统环境变量
cat << EOF > /etc/profile.d/maven.sh
export MAVEN_HOME=/usr/local/maven/
export PATH=\$PATH:\$MAVEN_HOME/bin
EOF

# 立即生效环境变量
source /etc/profile

# 验证maven是否正常工作
root@nexus:~# mvn -v
Apache Maven 3.8.5 (3599d3414f046de2324203b78ddcf9b5e4388aa0)
Maven home: /usr/local/maven
Java version: 1.8.0_312, vendor: Private Build, runtime: /usr/lib/jvm/java-8-openjdk-amd64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "5.4.0-107-generic", arch: "amd64", family: "unix"
```



### 3. 安装Nexus

[[ 官方下载地址 ]](https://www.sonatype.com/products/repository-oss-download)

#### 3.1 下载

```shell
cd /usr/local/src

## 实际下载的时候由web下载，需要登记，提供自己公司邮箱。
# sonatype-work为数据目录
wget https://sonatype-download.global.ssl.fastly.net/nexus/3/nexus-3.38.1-01-unix.tar.gz
tar -zxvf nexus-3.11.0-01-unix.tar.gz

# 将安装目录挪至需要的位置，或者使用软链
mv nexus-3.18.1-01 /usr/local/nexus
ln -sv /usr/local/src/nexus-3.38.1-01 /usr/local/nexus
# 将数据目录挪至需要的位置，启动前需要在配置文件中做好相应修改
mv sonatype-work/ /data/

## 此时安装已完成，可通过 /usr/local/nexus/bin/nexus start 命令进行启动
# 如果直接使用root用户启动，会有告警信息如下
# WARNING: ************************************************************
# WARNING: Detected execution as "root" user.  This is NOT recommended!
# WARNING: ************************************************************
```

#### 3.2 修改配置

jvm相关的配置在/usr/local/nexus/bin/nexus.vmoptions

nexus相关配置在/usr/local/nexus/etc/nexus-default.properties

```ini
#内存少，可以调整VM参数
-Xms500M -Xmx500M -XX:MaxDirectMemorySize=800M

#存储目录的修改
-Dkaraf.data=../sonatype-work/nexus3
-Dkaraf.log=../sonatype-work/nexus3/log
-Djava.io.tmpdir=../sonatype-work/nexus3/tmp

#服务端口的修改，默认8081端口
vim /usr/local/nexus/etc/nexus-default.properties
application-port=8081
```



#### 3.3 使用指定用户启动

```shell
## 使用其他用户启动
#创建用户组 nexus
groupadd nexus

#添加nexus到nexus组并创建用户目录（要先创建nexus组）
useradd -g nexus -m  nexus

#修改nexus用户登录密码
passwd  nexus

#nexus用户需可登录
usermod -s /bin/bash nexus

#将nexus用户的登录目录改成/usr/local/nexus，并加入nexus组，注意这里是大G。
usermod -d /usr/local/nexus -G nexus nexus

#将目录/usr/local/nexus 及其下面的所有文件、子目录的文件主改成 nexus
chown -R nexus:nexus /usr/local/nexus

#修改 nexus安装目录/bin/nexus 脚本文件，大约在485行
vim +485 /usr/local/nexus/bin/nexus
run_as_user='nexus'  #修改后的内容

#Ubuntu2004可使用如下命令创建用户
adduser --no-create-home --disabled-login --disabled-password nexus

```



### 4. 首次登录Nexus

```ini
默认账户 admin
密码保存在 usr/local/src/sonatype-work/nexus3/admin.password 文件中，查看使用。
默认端口 8081
```



### 5. 配置YUM仓库

阿里镜像仓库 http://mirrors.aliyun.com/centos/

1. 首先配置 **Blob Stores**，点击 `Create Blob Store`  ：选择 Type 为 `File`；自定义名称 NAME，可以明确知道和什么仓库有关，比如centos7；Path 为存储的绝对路径或者相对于数据目录的路径，最后保存。

   ![nexus-blobstores](https://raw.githubusercontent.com/9206284/Linux/main/img/nexus-bolbstores.png)

2. 然后配置 **Repositories**，点击 `Create repository`，会要选择 Recipe。接下来依次选择 yum(hosted)、yum(proxy)、yum(group)。

   <img src="https://raw.githubusercontent.com/9206284/Linux/main/img/nexus-yum-hosted.png" style="zoom:100%;" />

- yum(hosted) 配置项，依次填入Name（仓库的唯一标识)，repodata的深度，Blob store存储，Deploymment Policy选择 Allow redeploy。

  ![](https://raw.githubusercontent.com/9206284/Linux/main/img/nexus-yumhosted-config.png)

  

- yum(proxy)配置项，依次填入Name（仓库唯一标识），Remote storage（远程仓库地址，这里使用的阿里云http://mirrors.aliyun.com/centos/），以及Blob Store存储。

  ![image-20220412132017015](/Users/saw/Library/Application Support/typora-user-images/image-20220412132017015.png)



- yum(group)配置项，依次写入 Name，选择Blob store，以及将Group左侧中 之前新增的仓库添加到 右侧 Members中，做成组合仓库。

  ![](https://raw.githubusercontent.com/9206284/Linux/main/img/nexus-yum-group.png)

  

3. **仓库的引用**：

- 到仓库主界面，点击 `copy`，弹出窗口，`cmd+c`复制，回车关闭窗口。此时已复制好仓库URL

  ![](https://raw.githubusercontent.com/9206284/Linux/main/img/yum-repo.png)

- 登录需要引用nexus仓库的服务器
  1. 备份 /etc/yum.repos.d 目录 ，然后清空文件

      ```shell
      cp -r /etc/yum.repos.d/ /etc/yum.repos.d.bak
      rm -f /etc/yum.repos.d/*
      ```

  2. 手工编辑一个/etc/yum.repos.d/Nexus.repo，内容如下。本内容可用vim编辑的替换功能将 源http内容做替换生成，替换的内容是刚刚复制的`http://192.168.229.156:8081/repository/centos7-group/`

      ```ini
      # Nexus.repo
      #
      
      [base]
      name=CentOS-$releasever - Base - mirrors.aliyun.com
      failovermethod=priority
      baseurl=http://192.168.229.156:8081/repository/centos7-group/$releasever/os/$basearch/
      gpgcheck=1
      gpgkey=http://192.168.229.156:8081/repository/centos7-group/RPM-GPG-KEY-CentOS-7
      
      #released updates
      [updates]
      name=CentOS-$releasever - Updates - mirrors.aliyun.com
      failovermethod=priority
      baseurl=http://192.168.229.156:8081/repository/centos7-group/$releasever/updates/$basearch/
      gpgcheck=1
      gpgkey=http://192.168.229.156:8081/repository/centos7-group/RPM-GPG-KEY-CentOS-7
      
      #additional packages that may be useful
      [extras]
      name=CentOS-$releasever - Extras - mirrors.aliyun.com
      failovermethod=priority
      baseurl=http://192.168.229.156:8081/repository/centos7-group/$releasever/extras/$basearch/
      gpgcheck=1
      gpgkey=http://192.168.229.156:8081/repository/centos7-group/RPM-GPG-KEY-CentOS-7
      
      #additional packages that extend functionality of existing packages
      [centosplus]
      name=CentOS-$releasever - Plus - mirrors.aliyun.com
      failovermethod=priority
      baseurl=http://192.168.229.156:8081/repository/centos7-group/$releasever/centosplus/$basearch/
      gpgcheck=1
      enabled=0
      gpgkey=http://192.168.229.156:8081/repository/centos7-group/RPM-GPG-KEY-CentOS-7
      
      #contrib - packages by Centos Users
      [contrib]
      name=CentOS-$releasever - Contrib - mirrors.aliyun.com
      failovermethod=priority
      baseurl=http://192.168.229.156:8081/repository/centos7-group/$releasever/contrib/$basearch/
      gpgcheck=1
      enabled=0
      gpgkey=http://192.168.229.156:8081/repository/centos7-group/RPM-GPG-KEY-CentOS-7
      ```

- 重新生成yum仓库缓存，并尝试使用Nexus仓库下载安装软件

  ```shell
  yum clean all
  yum makecache
  yum -y install vim
  ```

- 查看仓库的缓存情况。所有经过proxy代理的文件都会被缓存下来

  ![](https://raw.githubusercontent.com/9206284/Linux/main/img/nexus-yum-cache.png)

<font color=brown>CentOs8的配置套路与CentOs7基本相同，也是下载阿里云的Centos8 repo配置文件，修改url地址即可。 但是这里注意一点就是不能使用Nexus的group 仓库， 只能使用Proxy仓库，否则就会出现`No available modular metadata for modular package`的问题导致包安装不成功。</font>

### 6. 配置APT仓库

阿里镜像仓库 http://mirrors.aliyun.com/ubuntu/

1. **新增 Blob Stores**
2. **配置 Repositories**。这里区别于centos的是，只能配置hosted和proxy两种仓库。
3. 配置hosted仓库时必须要填写**signing key**

生成signing key，[ 官方指导 ](https://help.sonatype.com/repomanager3/nexus-repository-administration/formats/apt-repositories)

Generate a key pair in a Linux system with the following commands:

```shell
apt-get update
apt-get install gpg
gpg --gen-key
gpg --list-keys
cd <path to the foolder to import the key pair>
gpg --armor --output public.gpg.key --export <gpg key Id>
gpg --armor --output private.gpg.key --export-secret-key <gpg key Id>
```

A key ID looks like: '515F58C16D58E682E91ACEFF17B5C97F9A816AD7'



需要使用Nexus仓库的服务器 源文件配置格式如下 

cat /etc/apt/sources.list

```ini
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal main restricted
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal-updates main restricted
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal universe
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal-updates universe
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal multiverse
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal-updates multiverse
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal-backports main restricted universe multiverse
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal-security main restricted
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal-security universe
deb http://192.168.229.156:8081/repository/ubuntu2004-aliyun focal-security multiverse
```



### 7. 配置Maven仓库



### 8. Nexus-401错误

默认访问nexus需要认证，因此打开允许匿名访问。

![](https://raw.githubusercontent.com/9206284/Linux/main/img/nexus-401.png)

