### 1. 防arp断网攻击

以管理员身份打开终端工具cmd，执行命令

```shell
# 1. 查看mac地址
C:\Users\User>arp -a
接口: 192.168.208.66 --- 0xc
  Internet 地址         物理地址              类型
  192.168.208.1         d0-dd-49-d5-6e-88     动态
  192.168.208.2         48-57-02-bf-c9-de     动态
  192.168.208.10        52-54-00-6a-44-86     动态
  192.168.208.11        2c-ea-7f-ee-5c-8d     动态
  192.168.208.12        52-54-00-4b-f2-c5     动态
  192.168.208.13        52-54-00-90-80-15     动态
  
# 2. 查看网络适配器 idx 参数 
C:\Users\User>netsh i i show in
Idx     Met         MTU          状态                名称
---  ----------  ----------  ------------  ---------------------------
  1          75  4294967295  connected     Loopback Pseudo-Interface 1
 12          15        1500  connected     以太网 2
 
# 3. 确认本机上网使用的适配器为"以太网 2"，IDX参数值为12

# 4. 将本机的网卡绑定网关IP和mac
netsh -c "i i" add neighbors 12 192.168.208.1  d0-dd-49-d5-6e-88

# 5. 如果需要断开网关IP与MAC地址的绑定关系
netsh -c "i i" delete neighbors 12
```

7栋8楼

```shell
#
## 7栋8楼有线网络网关地址 192.168.1.1，mac地址 34-0a-98-33-7d-70

# 1.查看自己本机网卡适配器的idx参数
netsh i i show in
# 2.确认当前使用的适配器，以确认其idx值
# 3.绑定网关IP和MAC地址
netsh -c "i i" add neighbors idx值 192.168.1.1 34-0a-98-33-7d-70
```



### 2. 域控组TBJ的成员批量修改密码过期策略

powershell 实现批量修改组成员的密码过期策略

```powershell
# 指定要查询的组名称
$groupName = "TBJ"

# 获取组的成员
$groupMembers = Get-ADGroupMember -Identity $groupName

# 遍历组的成员并设置密码策略
foreach ($member in $groupMembers) {
    if ($member.objectClass -eq "user") {
        $user = Get-ADUser -Identity $member
        Set-ADUser -Identity $user -PasswordNeverExpires $true
        Write-Host "已将密码策略设置为永不过期的用户：" $user.Name
    }
}

```

powershell在新增用户时配置密码过期策略

```shell
# 新增用户，首次登录不强制修改密码，密码无过期策略
New-ADUser -SamAccountName 用户名 -UserPrincipalName "用户名@jr.com" -Name 真实姓名 -GivenName $username -Surname "User" -Enabled $true -AccountPassword 密码 -ChangePasswordAtLogon $false -PasswordNeverExpires $true
# 添加用户到TBJ组
Add-ADGroupMember -Identity TBJ -Members 用户名 -Confirm:$false
```

