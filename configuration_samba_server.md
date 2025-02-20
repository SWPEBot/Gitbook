# Configuration Samba Server

参考连接：
[Install and Configure Samba](https://tutorials.ubuntu.com/tutorial/install-and-configure-samba#0)

> Note: `sysadmin` 为主机用户名.

## 1. 安装 Samba

```bash
sudo apt update
sudo apt install samba
```

## 2. 配置 Samba

> 需切换至 root 用户

```bash
su - root

vim /etc/samba/smb.conf
```

### 在[global]部分下添加

> 该步骤限定指定域名才可访问，如不需限定，可跳过设定 global

```bash
[global]
# Change this to the workgroup/NT-domain name your Samba server will part of
   workgroup = QUANTACN
   security = user
```

### 在配置末尾添加

```bash
[SWTeam]    # 需要共享的文件名
   path = /home/sysadmin/SWTeam # samba 文件存储路径
   valid users = sysadmin   # 有效的samba 用户
   available = yes
   browseable = yes
   public = yes
   writable = yes
   guest ok = yes
```

## 3. 设定 Samba 用户和密码

> 注意, 该用户必须为系统存在的用户

```bash
smbpasswd -a sysadmin # sysadmin 使用户名
# Note: setting password or not
```

## 4. 激活设定完成的 Samba 用户

```bash
smbpasswd -e sysadmin # sysadmin 是用户名
```

## 5. 重启 samba 服务

```bash
systemctl restart smbd.service nmbd.service
```

## 6. 连接方式

### 在 Ubuntu 上

Open up the default file manager and click Connect to Server then enter:

`smb://ip-address/SWTeam`

### 在 macOS 上

In the Finder menu, click Go > Connect to Server then enter:

`smb://ip-address/SWTeam`

### 在 Windows 上

Open up File Manager and edit the file path to:

`\\ip-address\SWTeam`
