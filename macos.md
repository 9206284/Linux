[Toc]
# MacOS基础

## 1：环境配置

### 1.1：科学上网

#### 1.1.1：macos使用

访问 https://www.yahaha.plus/user 购买代理，推荐使用ssr方式，下载ShadowsocksX [[ github链接 ]](https://github.com/shadowsocks/ShadowsocksX-NG)。

安装后打开，菜单栏的纸飞机图标-服务器-服务器订阅填入订阅地址(一定要https开头)，更新后出现您的节点。菜单栏的纸飞机图标-选择PAC模式。订阅地址：

```txt
https://yahahasub.com/link/R3bRV0U0TNN4zryX?mu=0
```

#### 1.1.2：终端使用shadowsocks代理

这里使用的shadowsocks代理工具，编辑 ~/.zshrc文件，末尾追加内容

```shell
#根据shadowsocks高级设置中本地Socks5监听地址与端口填写
#使用proxy打开代理，使用unproxy关闭代理
#通过命令开关避免全局通过代理访问互联网
alias proxy='export all_proxy=socks5://127.0.0.1:1086'
alias unproxy='unset all_proxy'
```

#### 1.1.3：终端使用vray2代理

这里使用的clashX代理工具，协议vray2，购买代理：https://2000w.xyz

安装完成并可使用代理之后，编辑 ~/.zshrc文件，末尾追加内容以给终端配置代理并设置开关。

```shell
#根据clashX高级设置中本地Socks5监听地址与端口填写
#使用proxy打开代理，使用unproxy关闭代理
#clashX相对智能，即时代理后，curl cip.cc获取的出口地址为真实地址.
alias proxy='export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890'
alias unproxy='unset all_proxy'
```

### 1.2：终端工具

#### 1.2.1：Homebrew

 [[ Home-brew 官方地址 ]](https://brew.sh/index_zh-cn)

```shell
#执行下列命令安装 Homebrew，需要翻墙
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

#执行以下两条命令将 Homebrew 加入 PATH
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/saw/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

常用软件

```shell
brew install vim tree lrzsz watch
```

#### 1.2.2：iTerm2

  [[ iTerm2官方网站 ]](https://iterm2.com)

**iTerm2** 是 Terminal 的替代品，也是 iTerm 的继任者。它适用于装有 macOS 10.14 或更高版本的 Mac。 iTerm2 将终端带入现代，是macOS的终端模拟器。

```shell
#homebrew安装
brew install iterm2

#或者官网下载安装
```

#### 1.2.3：zsh

**Zsh** 是一款强大的虚拟终端，既是一个系统的虚拟终端，也可以作为一个脚本语言的交互解析器。新的MacOS默认使用zsh，使用命令  `echo $SHELL` 查看当前shell，使用命令  `chsh -s /bin/zsh`  修改shell。

#### 1.2.4：oh-my-zsh

**Oh My Zsh** 是一款社区驱动的命令行工具，正如它的主页上说的，Oh My Zsh 是一种生活方式。它基于 zsh 命令行，提供了主题配置，插件机制，已经内置的便捷操作。给我们一种全新的方式使用命令行。

```shell
#安装oh-my-zsh，需要翻墙
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

```

#### 1.2.5：Console 工具 minicom

**Minicom** 是类unix环境下的一款常用的命令行串口调试工具。其功能与 Windows 下的超级终端相似，可以通过串口控制外部的硬件设备，通常用于对嵌入式设备进行管理。

```shell
brew install minicom

# minicom 的配置 minicom -s

# minicom 指定远程终端字符集 mimincom -R UTF-8 

```

#### 1.2.6：ftp、telnet工具 inetutils

inetutils 是一个包含 dnsdomainname、ftp、rcp、rexec、rlogin、rsh、telnet 的工具包。

```shell
brew install inetutils
```

另外如果想使用ftp的图形工具，直接使用Filezilla：https://filezilla-project.org/  。

#### 1.2.7：fig 命令行自动补全工具

```shell
brew install fig
```

### 1.3：L2TP VPN

打开系统偏好设置—网络，新增接口 `VPN`，VPN类型  `L2TP/IPSec`  ，服务名称自定义。

VPN接口的高级选项中勾选 “通过VPN连接发送所有流量”，配置DNS。

<img src="https://raw.githubusercontent.com/9206284/Linux/main/img/mac-vpn.png" alt="mac-pvn" style="zoom:40%;" />

如果这时VPN连接后一直显示连接错误

<img src="https://raw.githubusercontent.com/9206284/Linux/main/img/vpn-error.png" alt="vpn-error" style="zoom:50%;" />

对于这个问题，有说要删除下面的文件，有是创建此文件，所以介绍一下文件路径和内容。

```bash
~]# vim /etc/ppp/options ，内容如下

plugin L2TP.ppp
l2tpnoipsec
```

## 2：使用 Xquartz 支持 X11 实现远程访问Linux图形化

已改用vnc访问

(https://wsgzao.github.io/post/x11/)

Linux 本身是没有图形化界面的，所谓的图形化界面系统只不过中 Linux 下的应用程序。这一点和 Windows 不一样。Windows 从 Windows 95 开始，图形界面就直接在系统内核中实现了，是操作系统不可或缺的一部分。<font color=red>Linux 的图形化界面，底层都是基于 X 协议</font>。

X 协议由 X server 和 X client 组成：

- X server 管理主机上与显示相关的硬件设置（如显卡、硬盘、鼠标等），它负责屏幕画面的绘制与显示，以及将输入设置（如键盘、鼠标）的动作告知 X client。

- X client (即 X 应用程序) 则主要负责事件的处理（即程序的逻辑）。

举个例子，如果用户点击了鼠标左键，因为鼠标归 X server 管理，于是 X server 就捕捉到了鼠标点击这个动作，然后它将这个动作告诉 X client，因为 X client 负责程序逻辑，于是 X client 就根据程序预先设定的逻辑（例如画一个圆），告诉 X server 说：“请在鼠标点击的位置，画一个圆”。最后，X server 就响应 X client 的请求，在鼠标点击的位置，绘制并显示出一个圆。

![x11-forwarding](https://raw.githubusercontent.com/9206284/Linux/main/img/x11-forwarding.png)

许多时候 X server 和 X client 在同一台主机上，这看起来没什么。但是， <u>X server 和 X client 完全可以运行在不同的机器上</u>，只要彼此通过 X 协议通信即可。于是，我们就可以做一些 “神奇” 的事情，比如像本文开头谈到的，在本地显示 (X server)，运行在服务器上的 GUI 程序 (X client)。<u>这样的操作可以通过 SSH X11 Forwarding (转发) 来实现</u>。

X11 中的 X 指的就是 X 协议，11 指的是采用 X 协议的第 11 个版本。

如何使用X11协议来实现远程访问Linux图形化内容呢，步骤如下:

1. 安装`Xquartz` 获取 X11支持

   ```shell
   brew install xquartz
   ```

2. 打开`Xquartz`。访问`偏好设置` ->`安全性`，勾选`允许从网络客户端连接`，<font color=violetred>必须</font>。

3. 打开 `terminal` 或者 `iterm2`。在 `~/.bashrc` 或者 `~/.zshrc ` 文件内添加环境变量 `DISPLAY`。

   ```ini
   #声明变量DISPLAY
   export DISPLAY=:0
   ```

   ```shell
   #source 引入环境变量
   source ~/.zshrc
   #或者
   source ~/.bashrc
   ```

4. 打开 `terminal` 或者 `iterm2` ，ssh连接时加入参数 `-Y ` 以支持 X11 forwarding。

   ```shell
   ssh -Y user@address
   ```

5. 使用时遇到 `X11 forwarding request failed on channel 0` 如何处理？

   ```shell
   #安装 X authority file utility
   sudo yum install xauth
   ```

   

## 3：软件安装

### 3.1：sublime

[[ sublime 官方地址 ]](https://www.sublimetext.com/)

点击菜单栏的：Help -> Enter License，弹出激活窗口，输入上面的注册码然后点击「Use License」

**注册码（全部需要复制粘贴）**

```ini
—– BEGIN LICENSE —–
Mifeng User
Single User License
EA7E-1184812
C0DAA9CD 6BE825B5 FF935692 1750523A
EDF59D3F A3BD6C96 F8D33866 3F1CCCEA
1C25BE4D 25B1C4CC 5110C20E 5246CC42
D232C83B C99CCC42 0E32890C B6CBF018
B1D4C178 2F9DDB16 ABAA74E5 95304BEF
9D0CCFA9 8AF8F8E2 1E0A955E 4771A576
50737C65 325B6C32 817DCB83 A7394DFA
27B7E747 736A1198 B3865734 0B434AA5
—— END LICENSE ——
```

**菜单汉化**

1. 点击 Tools—Install Package Control，（安装包控件比较慢，并且没有反应，等待数分钟后会有弹窗）
2. 点击确定按钮
3. 菜单点击Preferences – Package Control，选择 Install Package
4. 输入 ChineseLocalzations 可见中文包！选中即可安装！

### 3.2：wireshark

[[ wireshark官方地址  ]](https://www.wireshark.org/) 使用如下命令直接安装wireshark软件

```shell
#使用brew安装
brew install --cask wireshark #UI版本
brew install wireshark #CLI版本
```

### 3.3：Another Redis Desktop Manager

Another Redis DeskTop Manager是一款全新的，稳定，快速的redis桌面连接工具；支持Linux, windows, mac;当加载大量的key时不会发生奔溃现象。

[[ 官方github地址 ]](https://github.com/qishibo/AnotherRedisDesktopManager)

```shell
#使用brew安装
brew install --cask another-redis-desktop-manager

#使用m1芯片的时候，提示文件已损坏无法使用，是否移至垃圾箱。解决方法如下
sudo spctl --master-disable
sudo xattr -rd com.apple.quarantine /Applications/Another\ Redis\ Desktop\ Manager.app

```

### 3.4：已损坏，无法打开。 您应该将它移到废纸篓

终端输入如下内容：

```shell
sudo xattr -r -d com.apple.quarantine 
```

打开 “访达”（Finder）进入 “应用程序” 目录，找到该软件图标，将图标拖到刚才的终端窗口里面得到

```shell
sudo xattr -r -d com.apple.quarantine /Applications/PicGo.app
```

回到终端窗口按回车，输入系统密码回车即可。接着重新打开安装软件，就可以正常使用了。

## 4：软件配置

### 4.1：iterm2 使用 rzsz

1. 通过brew给Mac安装 lrzsz

   ```shell
   brew install lrzsz
   ```

2. 编写执行脚本

   将  `iterm2-send-zmodem.sh`  和  `iterm2-recv-zmodem.sh`  保存到  `/opt/homebrew/Cellar/`  目录下，给予执行权限  `chmod +x iterm2*.sh` ，并用软链配置至 `/opt/homebrew/bin` 目录中。

   **注意脚本中 sz 和 rz 命令的路径根据实际情况调整**

   iterm2-send-zmodem.sh

   ```shell
   #!/bin/bash
   # Author: Matt Mastracci (matthew@mastracci.com)
   # AppleScript from http://stackoverflow.com/questions/4309087/cancel-button-on-osascript-in-a-bash-script
   # licensed under cc-wiki with attribution required 
   # Remainder of script public domain
   
   osascript -e 'tell application "iTerm2" to version' > /dev/null 2>&1 && NAME=iTerm2 || NAME=iTerm
   if [[ $NAME = "iTerm" ]]; then
       FILE=$(osascript -e 'tell application "iTerm" to activate' -e 'tell application "iTerm" to set thefile to choose file with prompt "Choose a file to send"' -e "do shell script (\"echo \"&(quoted form of POSIX path of thefile as Unicode text)&\"\")")
   else
       FILE=$(osascript -e 'tell application "iTerm2" to activate' -e 'tell application "iTerm2" to set thefile to choose file with prompt "Choose a file to send"' -e "do shell script (\"echo \"&(quoted form of POSIX path of thefile as Unicode text)&\"\")")
   fi
   if [[ $FILE = "" ]]; then
       echo Cancelled.
       # Send ZModem cancel
       echo -e \\x18\\x18\\x18\\x18\\x18
       sleep 1
       echo
       echo \# Cancelled transfer
   else
       /opt/homebrew/bin/sz "$FILE" --escape --binary --bufsize 4096
       sleep 1
       echo
       echo \# Received "$FILE"
   fi
   ```

   iterm2-recv-zmodem.sh

   ```shell
   #!/bin/bash
   # Author: Matt Mastracci (matthew@mastracci.com)
   # AppleScript from http://stackoverflow.com/questions/4309087/cancel-button-on-osascript-in-a-bash-script
   # licensed under cc-wiki with attribution required 
   # Remainder of script public domain
   
   osascript -e 'tell application "iTerm2" to version' > /dev/null 2>&1 && NAME=iTerm2 || NAME=iTerm
   if [[ $NAME = "iTerm" ]]; then
       FILE=$(osascript -e 'tell application "iTerm" to activate' -e 'tell application "iTerm" to set thefile to choose folder with prompt "Choose a folder to place received files in"' -e "do shell script (\"echo \"&(quoted form of POSIX path of thefile as Unicode text)&\"\")")
   else
       FILE=$(osascript -e 'tell application "iTerm2" to activate' -e 'tell application "iTerm2" to set thefile to choose folder with prompt "Choose a folder to place received files in"' -e "do shell script (\"echo \"&(quoted form of POSIX path of thefile as Unicode text)&\"\")")
   fi
   
   if [[ $FILE = "" ]]; then
       echo Cancelled.
       # Send ZModem cancel
       echo -e \\x18\\x18\\x18\\x18\\x18
       sleep 1
       echo
       echo \# Cancelled transfer
   else
       cd "$FILE"
       /opt/homebrew/bin/rz --rename --escape --binary --bufsize 4096 
       sleep 1
       echo
       echo
       echo \# Sent \-\> $FILE
   fi
   ```

3. 设置 Iterm2的 Trigger 特性

   **Preferences —— Profiles —— Default —— Adwanced 中的 Trigger，**

   添加两条trigger，分别设置 Regular expression，Action，Parameters，Instant如下：

   ```bash
   Regular expression: rz waiting to receive.\*\*B0100
   Action: Run Silent Coprocess
   Parameters: /usr/local/bin/iterm2-send-zmodem.sh
   Instant: checked
   
   Regular expression: \*\*B00000000000000
   Action: Run Silent Coprocess
   Parameters: /usr/local/bin/iterm2-recv-zmodem.sh
   Instant: checked
   ```

   **示图**

   ![iterm-rszs](https://raw.githubusercontent.com/9206284/Linux/main/img/iterm2-rzsz.png)

### 4.2：zsh终端配置高亮和自动建议填充

如下图所示，

- 高亮效果，特殊命令和错误命令也会有高亮显示；

- 自动建议填充方便我们快速的敲命令；

<img src="https://raw.githubusercontent.com/9206284/Linux/main/img/zsh-plugins.png" alt="zsh-plugins" style="zoom:70%;" />

```shell
## 下载插件
#高亮插件
cd  ~/.oh-my-zsh/custom/plugins 
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
#自动建议填充插件
$ git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions

## zsh配置中添加相关插件
vim ~/.zshrc
plugins=(
  zsh-syntax-highlighting
  zsh-autosuggestions
)

## 执行source 立刻生效
source ~/.zshrc
```

### 4.3：vim配置yaml编辑器

- 高亮效果：vim本身支持yaml语法的高亮；

- 语法缩进：YAML 文档需要有 2 个空格的缩进。然而，Vim 默认不设置这个，但是通过在 vim 配置 `～/.vimrc`  中添加以下行可以很容易地解决这个问题：

  ```ini
  autocmd FileType yaml setlocal ts=2 sts=2 sw=2 expandtab
  ```

  还可以设置缩进guides。缩进guides是每个缩进级别的细垂直线，有助于排列嵌套的 YAML 块。

  这里使用了[indentLine plugin](https://github.com/Yggdroot/indentLine) 插件，安装插件步骤如下

  ```shell
  #创建插件目录，vim 8.0 以上均使用此目录地址
  mkdir -p ~/.vim/pack/vendor/start
  
  #下载 indentLine 插件至指定目录，并立刻生效
  git clone https://github.com/Yggdroot/indentLine.git ~/.vim/pack/vendor/start/indentLine
  vim -u NONE -c "helptags  ~/.vim/pack/vendor/start/indentLine/doc" -c "q"
  
  #编辑 ~/vim.rc 配置文件，修改默认的分割线"¦" 为 "┆"
  let g:indentLine_char = '┆'
  ```

- 代码折叠

  **VIM 代码折叠方式 ** 可以用"foldmethod"选项来设置，如: <font color=green>`set foldmethod=indent`</font>

  有6种方式来折叠代码

  1. manual //手工定义折叠
  2. indent //用缩进表示折叠
  3. expr　 //用表达式来定义折叠
  4. syntax //用语法高亮来定义折叠
  5. diff  //对没有更改的文本进行折叠
  6. marker //用标志折叠

  **VIM 代码折叠命令**

  vim 代码折叠方式选择 indent 时，会自动利用缩进进行折叠。

  - zM，关闭所有折叠
  - zR，打开所有折叠
  - za，打开或关闭当前折叠

  可以在（括号）折叠处输入以下命令

  - zc 折叠

  - zC 对所在范围内所有嵌套的折叠电进行折叠

  - zo 展开折叠

  - zO 对所在范围内所有嵌套的折叠点展开

  - [z   到当前打开的折叠的开始处

  - ]z   到当前打开的折叠的末尾处

  - zj    向下移动，到达下一个折叠的开始处，关闭的折叠也被计入

  - zk   向上移动，到前一折叠的结束处，关闭的折叠也被计入


### 4.4：iterm2 使用密码管理器

此方案适合登录硬件防火墙、交换机等——目的时实现这样的一个功能 ：**在Profile设置登录命令，开启新Profile窗口后，会自动输入登录命令，然后利用触发器的关键字触发打开密码管理器，选择密码管理器对应的密码就可以快速登录服务器。**

1. **Profile**

   CMD+O 打开 profile界面，新建一个profile项目。

   <img src="https://raw.githubusercontent.com/9206284/Linux/main/img/iterm2-profile.png" style="zoom:50%;" />

   以后就可以通过CMD + O 快速的访问这个  `ssh-srv2` 的profile。

2. **密码管理器**

   iTerm2菜单Window里有一个密码管理器( password manager)，快捷键Opt+CMD+ F 。新增一个账户，填入密码。

   <img src="https://raw.githubusercontent.com/9206284/Linux/main/img/iterm2-password-manager.png" style="zoom:50%;" />

3. **触发器**

   在输入登录命令，以及配好密码之后，如何让命令输入后自动触发输入对应密码。使用Trigger触发器。

   <img src="https://raw.githubusercontent.com/9206284/Linux/main/img/iterm2-trigger.png" style="zoom:50%;" />

   


### 4.5：自建证书添加信任

harbor安装之后，使用的自建CA证书资料，需要添加到系统中。有两种方法

方法一：

1. 在macOS上打开终端，并运行以下命令来检查您的证书是否存在于macOS的钥匙串中：

   ```shell
   security find-certificate -c <certificate name> -Z | grep -A1 "SHA-1 hash"
   ```

   将`<certificate name>`替换为您的证书名称。如果您的证书未在macOS的钥匙串中，请将其导入到钥匙串中。

2. 运行以下命令来列出钥匙串中所有的证书：

   ```shell
   security find-certificate -a -p /Library/Keychains/System.keychain | openssl x509 -inform DER -text -noout | grep Issuer
   ```

   这将列出您的macOS系统中所有已安装证书的颁发机构（CA）。请检查列表中是否包含您的Harbor站点使用的证书颁发机构（CA）。

3. 如果您的证书颁发机构（CA）不在列表中，<font color=blue>请运行以下命令将其添加到macOS的信任列表中</font>：

   ```shell
   sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <certificate path>
   
   ```

   将`<certificate path>`替换为您的证书路径。这将将您的证书添加到macOS系统的信任列表中，以便Docker客户端能够验证它。

4. 重新启动Docker客户端，并尝试再次登录您的Harbor站点。如果一切正常，您应该可以成功登录并使用Harbor私有仓库了。

方法二：

打开“钥匙串”，点击锁头以“允许编辑“，在证书中，添加自建CA证书资料，并双击证书，在弹出窗口中，点击”信任“展开后，找到”使用此证书时“选项，将下面X.509基本策略——选择”始终信任“。

不过在使用浏览器访问自建证书时，可能仍然会遇到问题。记得使用https://xxx.xxx.xxx:443的方式强制使用https进行交互，另外可以配置浏览器对自建证书的信任。链接https://www.ibm.com/docs/zh/elm/6.0?topic=SSYMRC_6.0.0/com.ibm.rational.rrdi.admin.doc/topics/t_browser_ss_cert.htm

docker login 自建证书的harbor站点时 ，使用命令 " docker login https://xxx.xxx.xxxx" ，这将强制Docker客户端使用HTTPS协议进行认证。





## 5：特殊符号

### 5.1 苹果符号

按下Shift+Option+K就可以插入Apple logo了，不过要注意的是，在Windows可能直接显示为一个框框，而Linux系统则有可能显示为另外一个符号。

### 5.2 货币符号

Shift + 4能输出($)符号，当然如果是在中文输入法的情况下，它会输出人民币符号(￥)。

下面说的都是在默认英语书法的情况下：

美分(￠): Option + 4

英镑(￡): Option + 3

日元/人民币(￥): Option + Y

欧元(€):Shift + Option + 2

### 5.3 版权符号

按下Option + G就可以插入漂亮的版权符号(©)了。

### 5.4 商标符号

Option + 2 可以显示商标符号(™)，Option + R 可以获得注册商标符号(®)

### 5.5 数学符号

约等于(≈): Option + X

度(°): Shift + Option + 8

除号(÷): Option + /

无穷(∞): Option + 5

小于等于(≤): Option + ,

大于等于(≥): Option + .

不等于(≠): Option + =

Pi(π): Option + P

加减(±): Shift + Option + =

平方根(√): Option + V

求和(∑): Option + W
