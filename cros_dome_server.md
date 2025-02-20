# DOME Server Guide

**Reference link:
[FACTORY_SERVER](https://chromium.googlesource.com/chromiumos/platform/factory/+/HEAD/setup/FACTORY_SERVER.md)**

## Install ubuntu server

Google recommand install ubuntu server 16.04.1, But for quanta QCT server, Can
not install ubuntu server 16.04.1, We need install ubuntu server version more
than the 16.04.5, now used 16.04.6

For QCT server BIOS issue, under install, please select HWE method install.

## Setting network interface

* Call QMS Setup Ip Address

```bash
# setup ip address
sudo vim /etc/network/interfaces 
# For Ubuntu 20.04
# sudo vim /etc/netplan/00-installer-config.yaml


# restart networking
sudo /etc/inti.d/networking restart
# For Ubuntu 20.04
# sudo netplan apply

# check network status
ifconfig     #sometime need reboot host server
```

## Install package and docker

```bash
## Get Update and Install Docker
sudo apt-get update && sudo apt-get install docker.io

## install package
sudo apt-get install curl vim tmux tree git python3 python3-pip
# For Ubuntu 20.04/22.04
# Install python2 first and then install python2 pip tool.
# sudo apt install python2  
# curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py 
# sudo python2 get-pip.py
```

## Get Factory Server Script

```bash
# Created Dir for factory server script
mkdir -p ${HOME}/chromiumos/factory-server

# Via curl get factory server script
curl -L http://goo.gl/gKCyo1 | base64 --decode >cros_docker.sh
# New get factory server script
curl -L 'https://chromium.googlesource.com/chromiumos/platform/factory/+/HEAD/setup/cros_docker.sh?format=TEXT' | base64 --decode >cros_docker.sh

# Change script permission and get update
chmod +x cros_docker.sh
./cros_docer.sh update
```

## Download and install factory server

```bash
# Get Docker image
./cros_docer.sh pull

# Install Docker Image
./cros_docer.sh install

# Run Factory Server
./cros_docker.sh run
```

## Setting python mssql for database station

```bash
# install python mssql
sudo dpkg-reconfigure locales ## pymssql 依赖
echo "export LC_ALL=en_US.UTF-8" >> ~/.bashrc
pip3 install pymssql==2.1.5 
# sudo apt-get -y install python-pymssql --> 这种方法会造成各种过站报错,故不能用

# test
#python console run
from pymssql import connect
```

## Create path for SMT text station

```bash
sudo mkdir -p /opt/sdt/sp_m
sudo mkdir -p /opt/sdt/CQ_Monitor
sudo mkdir -p /opt/sdt/nft
sudo mkdir -p /opt/sdt/ACER_M
sudo mkdir -p /opt/sdt/live
sudo mkdir -p /opt/sdt/ACER_M_W
sudo mkdir -p /opt/sdt/CQ_FVS
```

## Get factory server shopfloor service

```bash
git clone http://10.18.6.89/swpe/shopfloor.git
```

## Via Chrome web connect DOME

```bash
user: admin
password: test0000
```

## Update deployment script and server images

The version of server image is tracked inside deployment script cros_docker.sh.
To update deployment script and server images to latest version, do:

```bash
./cros_docker.sh update
```

Then repeat the steps in previous section to update Docker images.

**Note: Umpire instances already created won't be updated automatically. To
update, go to Dome console and enter created projects, click the "DISABLE"
action button then "ENABLE" again using same port, then click the "CREATE A NEW
UMPIRE INSTANCE" button.**

## Reboot Dome S/F Server

1. 备份工作任务 
tmux session，作提示作用， 以便重开S/F Service和Balance, 以及git auto相关事项 
```
# S/F Service Task, Balance Task, Git Task
tmux list > tmux_session
#route information ，添加路由信息 
route > route.tbl 
```
2. 确认好你的服务器信息是否为你想要重启的服务器，预计10分钟左右
```
sudo reboot
``` 
3. 服务器重启进OS后，先将git auto打开， 以便你后续开的S/F Service和Balance Task 是最新配置
```
cd ~/shopfloor/scripts
tmux new -s git_auto
bash git_auto
# 等执行无误后即可退出， 若提示输入用户名和密码， 则需输入后再退出
```
4. mount WDS 服务器， 可参考`~/shopfloor/scripts/mount_dhcp_server_path.sh`, mount对应线别的wds即可，<font color="red">切忌不可直接执行此脚本</font>. 例如:
```
sudo mount -t cifs -o domain=quantacn,username=A0010415,password=PU4+stnswpe //40.30.1.55/reminst /mnt/55
```
5. 按着备份的路由信息， 确认路由是否添加完整. 可参考`/etc/rc.local`里面的路由命令
```
sudo route add -net **.**.**.** netmask ${mask} gw ${bcast} dev ${lan card}
```
6. 开启S/F Task 和Balance Task, 命名规则如下：
```
# S/F Service:
tmux new -s ${Quanta_Name}_${Google_Name}_sf
# Balance Task:
tmux new -s balance_${Quanta_Name}_${Google_Name}
```
