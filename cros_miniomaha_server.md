## Mini-Omaha Server Guide

### Prepare and setting env

```bash
# Samba (used for SF system)
sudo apt-get install python-glade2 system-config-samba
# use Mini-Omaha translate data, need install Cherrypy.
sudo apt-get install cherrypy3
# install pigz
sudo apt-get install pigz
```

### Server Setup

#### Setup DHCP Server (Ubuntu)

**setup router.**

```bash
sudo iptables -I INPUT -p tcp --dport 8080:8084 -j ACCEPT
sudo iptables -I INPUT -p udp --dport 69 -j ACCEPT
```

**install DHCP Server.**

`sudo apt-get install isc-dhcp-server`

setup IP (e.g, use 192.168.0.1 to be server IP)

```bash
sudo vim /etc/dhcp/dhcpd.conf

subnet 192.168.0.0 netmask 255.255.255.0 {
range 192.168.0.10 192.168.0.30;
option subnet-mask 255.255.255.0;
option routers 192.168.0.1;
next-server 192.168.0.1;
}

host mydhcp {
# This is your mac address on the interface to provide DHCP, use ifconfig to check
hardware ethernet c4:54:44:96:2f:c6;
fixed-address 192.168.0.1;
}
```

**setup DHCP server.**

`sudo vim /etc/default/isc-dhcp-server`

`INTERFACES="eth0"` (this is your interface to provide dhcp)

**fixed IP.**

`sudo ifconfig eth0 192.168.0.1`

**restart DHCP Server.**

`sudo /etc/init.d/isc-dhcp-server restart (or $ sudo restart isc-dhcp-server)`

Connect the DUT to the server to check if the DUT can get ip 192.168.0.xx. If
not, please check /var/log/syslog for error messages.

#### TFTP Server Setup (Ubuntu desktop)

**install TFTP Server.**

`sudo apt-get install tftpd-hpa`

**create TFTP root dir.**

`cd ~` `mkdir tftpboot`

**edit config.**

```bash
sudo vim /etc/default/tftpd-hpa

TFTP_USERNAME=“your_username”
TFTP_DIRECTORY=“/home/your_username/tftpboot”
TFTP_ADDRESS=“0.0.0.0:69”
TFTP_OPTIONS=“-v --secure”
```

**restart TFTP Server.**

`sudo service tftpd-hpa restart (or $ sudo /etc/init.d/tftpd-hpa restart)`

#### Mini-omaha Server

under factory bundle dir (show the complete screen "OK")

`./start_download_server.sh`

or factory bundle folder/factory_setup (Won't show the complete screen)

`python ./miniomaha.py`

### Via Mini-Omaha server sync factory bundle

**Before 2018.**

```bash
./factory_setup/make_factory_package.sh \
--board $BOARD \
--factory factoryimage \
--release releaseimage \
--firmware firmware \
--hwid hwidbundle
```

### Via Mini-Omaha Server Update HWID Bundle

```bash
# Check shopfloor Path, cat shopfloor_xxxx.sh
cat shopfloor.xxxx.sh
cd /home/sysadmin/chrome/project/shopfloor/shopfloor_data/update/
#Copy you prepare hwid bundle to this folder. mini-omaha will auto check.
```

### Via Mini-Omaha Server Setting TFTP Download

**Deprecated.**

```bash
cp vmlinux-board.bin /home/qms-bk/tftpboot/
sudo /etc/init.d/tftpd-hpa restart/stop/start
```

### Check DUT Test report on factory server

Under shopfloor reports folder, use serial number or mlb serial number check
reports name, you can edit it check registration key or other flag, e.g MAC/WLAN
ID/BT MAC.

e.g:

```bash
cat ~/shopfloor.paine.smt.sh
cd /home/sysadmin/chrome/paine/shopfloor/shopfloor_data/reports
(sudo) find -name *620160JTMBQC0025D*
./logs.20160603/SMT-620160JTMBQC0025D-20160603T093439Z.rpt.xz.md5
./logs.20160603/SMT-620160JTMBQC0025D-20160603T093439Z.rpt.xz
cd ./logs.20160604/
vi  SMT-620160JTMBQC0025D-20160604T013611Z.rpt.xz
?ggkey_check
```

### Check Mini-Omaha Server TFTP and DHCP serivces status

```bash
service tftpd-hpa status
sudo /etc/init.d/tftpd-hpa restart/start/status
service isc-dhcp-server status
sudo /etc/init.d/isc-dhcp-server status/restart
```

### Debug make factory bundle image type is usb & mini-omaha download error

**Python Framework, make image type shoould be recovery.**

factory_setup/lib/cros_image_common.sh

```python
grep -w "root=" | grep -w "cros_recovery")"   #Fail
grep -w "root" | grep -w "cros_recovery")"    #Pass
```

### Use Mini-Omaha Server Update Factory Toolkit

**Used for before 2018.**

```bash
# mount factory image partition 1
./factory_setup/mount_partition.sh factory_test/chromiumos_factory_image.bin 1 /mnt/factory_bundle/
# compress need update file to bzip format
tar cf factory.tar.bz2 -I pbzip2 -C /mnt/factory_bundle/dev_image --exclude factory/MD5SUM factory autotest
# update factory toolkit
cp factory.tar.bz2  shopfloor/shopfloor_data/update/
```

### Install Method

1.Factory Toolkit Applying the toolkit to a test image => factory image

Install factory toolkit on a device imaged with test image. Reboot device to
start factory test.

2.Booting factory test image from USB (Ctrl-U boot) then copy the image from USB
into internal storage (SSD/eMMC)

For developer only, cannot be applied in production line

3.Factory Install Shim

4.Netboot Imaging

5.RMA Shim

6.Copy Machine : Pre-imaging firmware and SSD into Chromebook devices so that no
bootstrap download is required.

### Mini-Omaha Netboot Imaging Download

Prepare for the netboot firmware for TFTP transfer, the image.net.bin file
locates in the bundle/netboot_firmware/.. folder.

If you want to change the IP of the file, the method as shown below:

```bash
# Under the chroot, in bundle/factory_setup folder:
# create the new file with the 192.168.0.1 IP
./update_firmware_settings.py --bootfile vmlinux-${BOARD}.bin
--input ../netboot_firmware/image.net.bin
--output ../netboot_firmware/image.net.bin.new
--omahaserver=http://192.168.0.1:8080/update
--tftpserverip=192.168.0.1
# remove the old file
rm ../netboot_firmware/image.net.bin
# rename the new file
mv ../netboot_firmware/image.net.bin.new ../netboot_firmware/image.net.bin
```

If you need to update the netboot firmware in factory test image, the method as
below:

```bash
The netboot firmware is stored in “/usr/local/factory/board/image.net.bin” of DUT. Under the chroot, in bundle/factory_setup folder:
./mount_partition.sh ../ factory_test/chromiumos_factory_image.bin 1 /media
# replace the file with the new netboot firmware(updated IP address)
cd ../netboot_firmware/
cp image.net.bin ../media/dev_image/factory/board/image.net.bin
sudo umount /media
```

1.Flash netboot firmware into DUT

Copy the image.net.bin file to USB, and mount the USB to DUT.

```bash
sudo mount /dev/sdc1 /media
cd /media
flashrom -w image.net.bin # or flash_netboot --image image.net.bin
reboot
```

2.Prepare network boot image for TFTP server Put the following file into TFTP
folder and restart TFTP server. In bundle,

```bash
cp ./factory_shim/netboot/ vmlinux-${BOARD}.bin ~/tftpboot/.
cd ~/tftpboot
sudo chmod 644 vmlinux-${BOARD}.bin
```

If you don\'t have the vmlinux-\${BOARD}.bin file, the building method as below:

````bash
You can modify the source code under: ~/trunk/src/platform/factory_installer/*
Under the chroot, in ~/trunk/src/scripts/ folder:

```bash
./setup_board --board=${BOARD}
cros_workon-${BOARD} start chromeos-factoryinstall
./build_packages --board=${BOARD}
./build_image --board=${BOARD} test
./make_netboot.sh --board=${BOARD}
````

You will be able to see the vmlinux.uimg and vmlinux.bin files in the
~/trunk/src/build/images/${BOARD}/latest/netboot/ folder, and you need to rename the vmlinux.bin to vmlinux-${BOARD}.bin
and put it into the bundle/factory_shim/netboot/ folder and TFTP folder. Or
strip the vmlinux.uimg out the first 64 bytes to get vmlinux-${BOARD}.bin $ dd
bs=64 skip=1 if=./vmlinux.uimg of=./vmlinux-\${BOARD}.bin

3.Start TFTP server

`sudo service tftpd-hpa restart`

4.Start DHCP server

`sudo ifconfig eth0 192.168.0.1`

`sudo /etc/init.d/isc-dhcp-server restart`

5.Start Mini-omaha server In a factory bundle folder,

`./start_download_server.sh`

6.Install Connect the DUT to server (Linux host), the installation will start
automatically.

### Python Framework RMA Shim

1.Create the RMA Shim Image

Under the chroot, in bundle/factory_setup folder:

```bash
./make_factory_package.sh \
--usbimg output_file_name \
--install ../bundle/factory_shim/bin_file \
--factory ../bundle/factory_test/chromiumos_factory_image.bin \
--release ../bundle/release/bin_file \
--firmware_updater ../bundle/firmware/chromeos-firmwareupdate \
--hwid_updater ../bundle/hwid/sh_file
```

2.Install

Insert USB stick and enter into DEV mode.

Press Ctrl+U to boot from USB, the installation can be started automatically.

### Factory image modified for RMA shim

For main.py:

1.Modified factory_environment = False

2.Modified enable_shop_floor = False

3.For test list: Remove Get_Device_info

4.Add below setting in HWID item

Dargs={'generate' : True, 'skip_shpfloor':True, 'rma_mode':True}

### Firmware update and extract (EC/BIOS Extract)

The script locates in bundle/factory_setup folder, and the
chromeos-firmwareupdate will be extracted after executing the command. (The
extracted file should be put into bundle/firmware folder.)

`./extract_firmware_updater.sh -i RecoveryImagePath - o OutDirPath`

In the bundle/firmware folder, under the chroot:

```bash
chromeos-firmwareupdate [bundle_option|--]
bundle_option (only one option can be selected):

- h,--help: Show usage help
- V: show version and content of bundle
  --force: force execution and ignore /root/.leave_firmware_alone
  --sb_extract [PATH]: extract bundle content to a temporary folder
  --sb_repack [PATH]: update bundle content from given folder
  FLOW:

You can extract the EC and BIOS file from the chromeos-firmwareupdate, and replace
it or flash it into DUT via the servo board.
```

## Mini-Omaha Server 设定 TFTP download

**Deprecated.**

```bash
cp vmlinux-board.bin /home/qms-bk/tftpboot/

sudo /etc/init.d/tftpd-hpa restart/stop/start
```

## Mini-Omaha Server 更新 HWID Bundle

```bash
# 检查 shopfloor Path, cat shopfloor_xxxx.sh

cat shopfloor.xxxx.sh

cd /home/sysadmin/chrome/project/shopfloor/shopfloor_data/update/

#Copy you prepare hwid bundle to this folder. mini-omaha will auto check.
```


## 通过 Mini-Omaha server 同步 factory bundle

仅适用于 Python 架构机种

```bash
./factory_setup/make_factory_package.sh \
--board $BOARD \
--factory factoryimage \
--release releaseimage \
--firmware firmware \
--hwid hwidbundle
```

## 处理 MiniOmaha 同步失败，Download 异常，shopfloor umount is busy

仅适用于 Python 框架

```bash
sudo vimdiff ./factory_setup/make_factory_package.sh ../cyan/factory_setup/make_factory_package.sh

#grep 'sleep10' {Modify sleep 10}
```

## 检查 Mini-Omaha Server TFTP 和 DHCP 服务状态

```bash
service tftpd-hpa status

sudo /etc/init.d/tftpd-hpa restart/start/status

service isc-dhcp-server status

sudo /etc/init.d/isc-dhcp-server status/restart
```

### Use Mini-Omaha Server Update Factory Toolkit

1.  Mount factory image partition 1

    ./factory_setup/mount_partition.sh factory_test/chromiumos_factory_image.bin 1 /mnt/factory_bundle/

2.  Compress update file

    tar cf factory.tar.bz2 -I pbzip2 -C /mnt/factory_bundle/dev_image --exclude factory/MD5SUM factory autotest

3.  Update toolkit

    cp factory.tar.bz2 shopfloor/shopfloor_data/update/


## 检查工厂服务器上的 DUT 测试报告

在 shopfloor reports 文件夹下，使用序列号或 mlb 序列号检查报告名称，您可以编辑它检查注册密钥或其他标志，例如 MAC / WLAN ID / BT MAC。

e.g:

```bash
cat ~/shopfloor.paine.smt.sh
cd /home/sysadmin/chrome/paine/shopfloor/shopfloor_data/reports
(sudo) find -name *620160JTMBQC0025D*
./logs.20160603/SMT-620160JTMBQC0025D-20160603T093439Z.rpt.xz.md5
./logs.20160603/SMT-620160JTMBQC0025D-20160603T093439Z.rpt.xz
cd ./logs.20160604/
vi  SMT-620160JTMBQC0025D-20160604T013611Z.rpt.xz
?ggkey_check
```

## 通过 Mini-Omaha Server 更新 Factory Toolkit

**仅供 Python 架构使用.**

```bash
# 挂载 factory image partition 1
./factory_setup/mount_partition.sh factory_test/chromiumos_factory_image.bin 1 /mnt/factory_bundle/

# 压缩要更新文件为指定格式
tar cf factory.tar.bz2 -I pbzip2 -C /mnt/factory_bundle/dev_image --exclude factory/MD5SUM factory autotest

# 更新 toolkit
cp factory.tar.bz2  shopfloor/shopfloor_data/update/
```
