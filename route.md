# Linux Route Manage

参考链接: <https://www.cnblogs.com/baiduboy/p/7278715.html>

部分网络通常需要增加路由才可访问，如 QCI/P3 Access SWPE Server 等
e.g: 10.18.xx.xx 需添加路由才可访问 10.17.xx.xx

## Check Route

通过下面指令查看 `Linux Kernel` 路由表

```bash
route
```

输出示例:

```bash
sysadmin@swpe-server:~$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    0      0        0 enp216s0f0
10.17.0.0       10.18.6.254     255.255.0.0     UG    0      0        0 enp134s0f3
10.18.6.0       0.0.0.0         255.255.255.0   U     0      0        0 enp134s0f3
30.30.0.0       0.0.0.0         255.255.0.0     U     0      0        0 enp134s0f1
192.168.0.0     0.0.0.0         255.255.0.0     U     0      0        0 enp134s0f2
192.168.200.0   0.0.0.0         255.255.255.0   U     0      0        0 enp216s0f0
```

机器实际 `IP` 为 `10.18.6.x`

## P3 Module 测试注意事项

如果架设的过站 server 有重启，需要做如下两个动作：

1.增加 P3 测试段 IP 10.18.X.X 段路由，因为 10.17.63.250 这台服务区没有配置 10.18.X.X 的网卡
`sudo route add -net 10.18.0.0 netmask 255.255.0.0 gw 10.17.63.254 dev eth0`

2.挂载 mount P3 过站交互文件夹'Module'
`sudo mount -t cifs -o username=bekins,password=bekind2 //10.18.6.61/sp_m /opt/sdt/Module`

## 检查 Linux 服务器路由规则

Check NetMask

`ifconfig eth0 | grep Mask`

增加 ip 网段到路由表

`sudo route add -net 10.243.0.0 netmask 255.255.0.0 gw 10.18.6.254 dev enp134s0f3`

允许台北端连接 factory server, 通过 10.17 IP 进行跳转

`sudo route add -net 10.243.0.0 netmask 255.255.0.0 gw 10.18.6.254 dev enp134s0f3`
