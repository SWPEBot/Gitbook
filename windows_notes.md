# Windows Notes

## Windows replacae osk.exe to cmd.exe

*Boot from WinPE:*

```sh
c:
cd \
cd windows\system32
ren osk.exe osk1.exe #create backup 
copy cmd.exe osk.exe /v /y
shutdown
```

## Format Disk

*Boot from WinPE:*

```sh
diskpart
list disk
sel disk 0
list par 
clean  
convert gpt/mbr
```

## Windows 10 Install .NET3.5 microsoft-windows-netfx3-ondemand-package

```sh
dism.exe /online /enable-feature /featurename:netfx3 /Source:F:\win10\sources\sxs
```

## Windows show WLAN info

```sh
netsh wlan show drivers
```

## Windows 10 配置内网可远程桌面连接

前提，host 要具备管理员权限，在 host 机器上打开注册表,定位到`HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-tcp`找到 `SecurityLayer`, 设置值为 `0`

## Office Tool Plus

无版权，慎用

Office Tool Plus 基于 Office 部署工具 (ODT) 打造，可以很轻松地部署 Office

链接： <https://otp.landian.vip/zh-cn/>
