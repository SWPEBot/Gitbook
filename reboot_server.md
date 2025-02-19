#重启Dome Server之后需做事宜
##查询路由

```bash
##以832为例子
route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
10.17.0.0       10.18.6.254     255.255.0.0     UG    0      0        0 enp216s0f3
10.18.0.0       10.18.6.254     255.255.0.0     UG    0      0        0 enp216s0f3
10.18.6.0       *               255.255.255.0   U     0      0        0 enp216s0f3
30.30.0.0       *               255.255.0.0     U     0      0        0 enp216s0f2
40.30.0.0       *               255.255.0.0     U     0      0        0 enp134s0f0
40.31.0.0       *               255.255.0.0     U     0      0        0 enp134s0f1
40.32.0.0       *               255.255.0.0     U     0      0        0 enp134s0f2
40.33.0.0       *               255.255.0.0     U     0      0        0 enp134s0f3
172.17.0.0      *               255.255.0.0     U     0      0        0 docker0
192.168.0.0     *               255.255.0.0     U     0      0        0 enp216s0f1
```
##开路由
```bash
sudo route add -net 10.18.0.0 netmask 255.255.0.0 gw 10.17.63.254 dev eth0
sudo route add -net 10.17.0.0 netmask 255.255.0.0 gw 10.18.6.254 dev enp216s0f2  # For 82
sudo route add -net 10.17.0.0 netmask 255.255.0.0 gw 10.18.6.254 dev enp134s0f3  # For 89
sudo route add -net 10.17.0.0 netmask 255.255.0.0 gw 10.18.6.254 dev enp216s0f3  # For 83
```
##注意,这里在开路由时，要去ping DB Server,若不能ping通，还要加DB路由，例如:
```bash
sudo route add -net 10.18.8.0 netmask 255.255.255.0 gw 10.18.6.254 dev enp134s0f1
```
##克隆shopfloor文件
```bash
git clone http://10.18.6.89/swpe/shopfloor.git  #克隆shopfloor service 文件
tmux new -s git_auto_pull_shopfloor   #建立自动pull 最新shopfloor service文件的session
bash git_auto.sh  #自动pull 最新shopfloor service 文件
```
##Mount 相应的文件夹
```bash
sudo mount -t cifs -o domain=quantacn,username=bekins,password=bekind2 //192.168.2.2/Monitor_NB4 /opt/sdt/CQ_FVS
sudo mount -t cifs -o domain=quantacn,username=bekins,password=bekind2 //192.168.2.2/sp_m /opt/sdt/sp_m
sudo mount -t cifs -o domain=quantacn,username=bekins,password=bekind2 //30.30.1.5/tmon /opt/sdt/CQ_Monitor
sudo mount -t cifs -o domain=quantacn,username=bekins,password=bekind2 //30.30.1.5/nft /opt/sdt/nft
sudo mount -t cifs -o domain=quantacn,username=bekins,password=bekind2 //30.30.1.5/ACER_M /opt/sdt/ACER_M
sudo mount -t cifs -o domain=quantacn,username=bekins,password=bekind2 //30.30.1.5/alive /opt/sdt/live
sudo mount -t cifs -o username=bekins,password=bekind2 //10.18.6.61/sp_m /opt/sdt/Module  #这是巴西module线必须要开的mount,否则不能过站,83 need do it!
##以上mount可以执行"/home/sysadmin/shopfloor/scripts/mount_sf_path.sh"
```
