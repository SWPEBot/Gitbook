#  SWPE Learning Notes

> Last Update Data: Mon Mar 30 2020 1:23:29 PM

## Ubuntu 18.04 安装 Go Language

```bash
sudo add-apt-repository ppa:longsleep/golang-backports
sudo apt-get update
sudo apt-get install golang-go
```

## Ubuntu 14.04 屏蔽 apport 错误提示

```bash
sudo gedit /etc/default/apport
enabled=1　#open apport
enabled=0　#close apport
```

## Ubuntu 14.04 修改启动等待时间（不推荐）

```bash
# 设定 grub：
sudo gedit /boot/grub/grub.cfg

# 修改 grub：
#Default startup item，setting 0 to first boot
set default=0

#Default waiting time，setting waiting 10’s
set timeout=10
```

## Ubuntu 启动项修复

可通过 U 盘进入 ubuntu 体验桌面再行修复.

启动修复

```bash
sudo add-apt-repository ppa:yannubuntu/boot-repair && sudo apt-get update
sudo apt-get install -y boot-repair && boot-repair
# select recommended repair
```

## Ubuntu 16.04 安装 Google Pingying 中文输入法

1. 在命令行中运行：`sudo apt install fcitx-googlepinyin`
2. 在 system setting > Language Support 中 Keyboard input method system 选择 fcitx
3. 重启系统
4. 在 Text Entry 中 text 框右下角的“设置”（图标）中 add googlepinyin，设置后即可使用

## Ubuntu 设定系统环境变量

1.方案 1 （推荐）

```bash
vim ~/.bashrc

#将环境变量写在bashrc或zshrc中，重启terminal生效
export PATH=$PATH:/xxx/xxx
```

2.方案 2

```bash
vim /etc/profile

# 配置如下
export PATH=$PATH:/xxx/xxx
```

3.方案 3

```bash
edit environment

# 配置如下
export PATH=$PATH:/xxx/xxx
```

## Ubuntu 16.04 设定 Launcher bar 位置

```bash
# 移至下方
gsettings set com.canonical.Unity.Launcher launcher-position Bottom

# 移至左边
gsettings set com.canonical.Unity.Launcher launcher-position Left
```

## Ubuntu 本地镜像像还原

1. BIOS 选择 UEFI 模式，设置 LAN 首次启动;

2. When show j:

    > key in：`copy j:\DL_TOOLS\AcerImageDL\LPXIMGiT64v395 /y`

3. format disk

    ```powershell
    diskpart
    sel disk 0
    lis disk
    clean
    exit
    ```

4. Restore:`Restore.cmd 0 W10Ubuntu\UBUNTUOS\UNUNTUOS.IMG`

5. Waiting progress complete

## Ubuntu 16.04 安装 RDP 及 VNC

[参考链接](https://blog.51cto.com/moerjinrong/2179138)

## Ubuntu 16.04 安装 i-bus 中文输入法

1. 安装中文语言支持

    `System setting -> language supoort` 选择中文

2. 安装 i-bus

    `sudo apt-get install ibus ibus-clutter ibus-gtk ibus-gtk3 ibus-qt4`

3. 配置 i-bus 服务

    `im-config -s ibus`

4. 安装 i-bus 拼音

    `sudo apt-get install ibus-pinyin`

5. 设置 i-bus

    `sudo ibus-setup`

    选择 `input method`, 添加 zh-ch 拼音系统设置 -> `Text entry > zh-cn Pinyin`

## Ubuntu 下 Vscode 终端乱码解决方案

解决方法

首先是下载需要的字体，包括执行 sudo fc-cache -f -v 命令刷新字体：

```bash
cd /usr/share/fonts/truetype/
sudo git clone https://github.com/abertsch/Menlo-for-Powerline.git
sudo fc-cache -f -v
```

设置 vscode 中的字体为 Menlo for Powerline：

注：Vs Code 的用户设置`.json` 中加入代码：`"terminal.integrated.fontFamily": "Menlo for Powerline"`

## Ubuntu Server 关闭每日自动更新

```bash
检查:
sudo systemctl status apt-daily.timer

禁用:
sudo apt-get remove unattended-upgrades
sudo systemctl stop apt-daily.timer
sudo systemctl disable apt-daily.timer
sudo systemctl disable apt-daily.service
sudo systemctl daemon-reload
```

## Ubuntu 配置 mkdocs Web Documention 服务

Support for `python2` and `python3`

Choose to use `python3`

```bash
pip3 install mkdocs mkdocs-material
```

Offical Link:[MkDocs](https://www.mkdocs.org/)

## Ubuntu 卸载孤立包，删除已卸载软件残留

卸载孤立包

```bash
# 安装 deborphan
sudo apt-get install deborphan -y
# 使用 deborphan 检查孤立包
deborphan
# 使用 orphaner 进行清理
sudo orphaner
```

清理软件残留

```bash
# 卸载
sudo apt-get remove --purge 软件名
# 删除系统不再使用的孤立软件
sudo apt-get autoremove
 # 清理旧版本的软件缓存
sudo apt-get autoclean
# 清除残余的配置文件，完全清理
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

## Ubuntu 安装 fzf

```bash
# 安装
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install

# 升级
cd ~/.fzf && git pull && ./install
```

## 解除系统分区只读权限

```bash
# 解除指定分区只读权限
/usr/share/vboot/bin/make_dev_ssd.sh --remove_rootfs_verification --partitions 2

# 还原 Partition 只读权限
/usr/share/vboot/bin/make_dev_ssd.sh --noremove_rootfs_verification --partitions 2
```

## 调节 LCD 背光

**仅适用部分机种.**

路径: `/sys/devices/backlight_lcd/backlight/backlight_lcd`

- Intel Platform

  ```bash
  echo 100 > /sys/class/backlight/intel_backlight/brightness
  ```

  **Json 架构请使用 backlight-tool 进行调节.**

## 检查 backlight level

```bash
backlight-tool
```

## 设定屏幕常亮不进入 S3

```bash
initctl stop powerd
#or#
stop powerd
```

## 调整服务器网络缓存

```bash
echo 4096 > /proc/sys/net/ipv4/neigh/default/gc_thresh3
```

## 定期获取 Battery/Power info and log

```bash
watch –n 1 ectool battery #Battery Info

watch –n 1 power_supply_info #AC／DC information
```

## 检查电池信息

```bash
watch devkit-power -d
```

## 检查 Power source log

```bash
mosys eventlog list
```

## 检查蓝牙状态

```bash
hciconfig hci0 up
hciconfig

# scan bluetooth device
hcitool scan
# check more usage
hcitool --help
# reset hci0 device
hciconfig hci0 reset
```

## 检查 DUT thermal

```bash
cat /sys/class/thermal/thermal_zone*/temp

cat /sys/class/thermal/thermal_zone*/temp | tail -1 | cut -b 1-2
```

## 检查 Lid／Gyro Sensor

`cd /sys/bus/platform/devices/`

**cros-ec-accel.0 is lid，cros-ec-accel.1 is base.**

```bash
grep -Fow cros-ec-gyro ／sys／bus/iio/devices/iio:device*/name*
grep -Fow cros-ec-accel ／sys／bus/iio/devices/iio:device*/name*
```

## 检查 light sensor

```bash
cd /sys/bus/iio/devices/iio\:device0\
#(Note: use Tab for path completion, it saves a lot of typing!)

cat illuminance0_input
#(value is probably 1)

#to read the value continuously do:
watch -n 0.5 cat illuminance0_input
#(press Ctrl-C when done)

#To set the calibration multiplier to 4 do:
echo 4 > illuminance0_calibscale
```

## 检查 Camera 设备路径

PATH `/sys/class/video4linux/`

Name `video0` or `video1`

使用 find 命令查找：

```bash
find / -name video4linux
```

USB Cmera device path:

```bash
ls /sys/bus/usb/drivers/uvcvideo/1-xxxx/video4linux/video*/name
```

## 检查 USB 设备和 USB 串口

`lsusb -t`

`lsusb -v` (show detail)

## 删除文件 ^M 符号

在 vim 中，键入: `:%s/\r//g`

或者使用 dos2unix 进行转换，也可以使用 vscode 进行转换.

## 获取硬件 i2c address

```bash
dmesg | grep i2c

i2cdetect -l

i2cdetect -y -r $path

# 根据x + y 进行计算以获取 i2c address
```

## 保存 python&shell 脚本 stdout 执行输出 log

```bash
# 示例1:
./test/sh > test.log 2>&1

# 示例2:
sudo ./shopfloor_service.py & > shopfloor_service_project_debug.log 2>&1
```

## 使用命令监视 LID 传感器角度

```bash
watch -n 0.5 ectool motionsense lid_angle
```

## 母盘制作与检查

```shell
# Python 架构使用
gooftool verify_keys
gooftool verify_rootfs
rm /var/log/eventlog.txt  && factory_restart -a && halt
```

## 制作母盘时增加 CheckSum(CRC32)

```bash
# Ubuntu 设定如果没有安装 CRC32, 请先安装如下 package
sudo apt-get install libarchive-zip-perl

# 使用实例
crc32 my_file
```

## 命令行运行 Stress App Test

```bash
stressapptest
```

## Json List 设定双重判断机制

```json
"Touchscreen": {
    "pytest_name": "touchscreen",
    "run_if": "device.component.has_touchscreen",
    "run_if": "not device.component.is_cmi_panel",
    "args": {
    "timeout_secs": 200,
    "x_segments": 8,
    "y_segments": 8
    }
}
```

## 设定 Python Vim IDE，使用 fisa vim config

```bash
sudo apt install git curl python3-pip exuberant-ctags ack-grep
sudo pip3 install pynvim flake8 pylint isort
curl https://raw.githubusercontent.com/fisadev/fisa-vim-config/master/config.vim
cp config.vim ~/.vimrc
vim
```

## 测试 Audio（debug）

1.  检查音频设备列表

    `arecord –-list-devices`

2.  测试音频/扬声器

* **测试录音**

  ```bash
  # arecord -f cd 11.wav
  arecord -f dat test.ware # chrome os used dat format, 48000 Hz
  arecord -f cd -d 10 11.wav  #-d is duration; 10 is time=10s
  aplay 11.wav  #play record file
  ```

* **测试播音**

  ```bash
  arecord -f dat /tmp/1.wav  #(stereo)

  #Play 1kHz sine command
  play -n synth 1 sine 440 gain -3
  #1 is time “s”，440 is 440hz(20-20000 is sweep)，-3 is dB
  ```

### 获取 AudioJack dongle 状态

```bash
echo -n 168 > /sys/class/gpio/export

cat /sys/class/gpio/gpio168/value #1(indicates headphone inserted)

#Remove headphones
cat /sys/class/gpio/gpio168/value #0(indicates headphone removed)
```

### 测试 Mic 实时状态

`arecord -vv -f dat /dev/null` 或 `arecord -vvv -f dat /dev/null`

### 测试 Speaker

Intel Platform

`aplay PQC_Test.wav`

AMD Platform

`aplay -r 44100 -D hw:0,2 -c 2 /media/PQC_Test.wav -vv`

## 通过终端测试 S3

Switch to terminal screen <Ctrl + Alt + F2> Log in as root.

```bash
echo mem > /sys/power/state (s3)
powerd_suspend (S3)
echo freeze > /sys/power/state (S0ix)
```

### Suspend 测试（Suspend Test）

```bash
powerd_dbus_suspend

echo mem > /sys/power/state (s3)
powerd_suspend (S3)
echo freeze > /sys/power/state (S0ix)
#S0ix 仅适用 Intel 平台

suspend_stress_test -c 500  500 is cyle
#Longrun S3: suspend_test_test

#for MTK
suspend_stress_test -i spi32766.0
suspend_stress_test -i spi32766.0 -c 100   #Only for MTK
```

## 扫描可用的 WLAN 信号强度

```bash
iwlist wlan0 scan | grep -C 5 'sw_antenna_test' | grep 'Signal level'
```

### 设定 factory mode 下连接 Wi-Fi

路径: `/usr/local/lib/flimflam/test/`

```bash
#将以下文件copy至PATH
wifi
wifi_proxy.py
shill_proxy.py
```

测试示例：

```bash
/usr/local/lib/flimflam/test/wifi connect hostname password
```

新方案：

```bash
/usr/local/autotest/scripts/wifi connect SSID PASSWORD
```

## 命令行使用 toybox 挂载指定磁盘分区

```bash
toybox mount -o ro,loop /dev/mmcblkp5 /media
# 你可以从分区复制文件
# 示例：
cp /media/opt/google/firmware/touch/elan_i2c.bin /tmp
```

## Google Key FTP Server

10.18.8.24

user：`f2-smt\smt`

password: `QCMC+smtsf`

## Chrome FTP Server Note

FTP 服务用户名密码:
用户`chrome-ftp` 密码 `sysadmin`

Samba 共享文件夹用户名密码:
用户`sysadmin` 密码 `sysadmin`

## 验证45站过站方法,避免DUT Wipping 到出货需要重新Download.

1. 用Ubuntu 链接DUT
2. 在Ubuntu 中key 如下命令
```
gooftool wipe_in_place --test_umount --shopfloor_url http://10.18.6.***:${SF_Port}
/usr/local/factory/sh/cutoff/inform_shopfloor.sh http://10.18.6.***${SF_PORT} factory_wipe
```


