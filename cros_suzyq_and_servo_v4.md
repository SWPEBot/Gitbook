# Suzy-Q C2D2 Servo V4 用法示例

## 安装 minicom

```bash
#outside chroot
sudo apt-get install minicom

#inside chroot
sudo emerge minicom
```

## 检查 H1 Firmware 版本

```bash
sudo minicom -D /dev/ttyUSB0 #For Suzy Q Case
sudo minicom -D /dev/ttyUSB3 #For Type C Servo V4 Case
version
```

## 抓取 H1 Console log

```bash
sudo minicom -D /dev/ttyUSB0 -C cr50_debug.log #For Suzy Q Case
sudo minicom -D /dev/ttyUSB3 -C cr50_debug.log #For Type C Servo V4 Case
```

## 抓取 AP(CPU/BIOS) Console Log

```bash
sudo minicom -D /dev/ttyUSB1 -C AP_console.log #For Suzy Q Case
sudo minicom -D /dev/ttyUSB4 -C AP_console.log #For Type C Servo V4 Case
```

## 抓取 EC Console Log

```bash
sudo minicom -D /dev/ttyUSB2 -C EC_console.log #For Suzy Q Case
sudo minicom -D /dev/ttyUSB5 -C EC_console.log #For Type C Servo V4 Case
```

**如果无需保存 log，请使用 `sudo minicom -D /dev/ttyUSBx`**

## 如何查看Suzy-Q 或 Servo V4 C2D2 cr50/bios/ec 路徑

```bash
ls /dev/ttyUSB*
#For SuzyQ Case, H1 -> /dev/tty/USB0, CPU -> /dev/ttyUSB1, EC -> /dev/ttyUSB2
#For Servo V4 Case, H1 -> /dev/ttyUSB3, CPU -> /dev/ttyUSB4, EC -> /dev/ttyUSB5
#For C2D2 Case, H1 -> /dev/ttyUSB0 CPU -> /dev/ttyUSB2 EC -> /dev/ttyUSB3
```


## Flash Cr50 FW/EC/BIOS with Suzy Q or Type C Servo V4

~~## 连接Suzy-Q or Type C Servo V4 (該步驟針對Jasper Lake Platform, 其他Platform 請跳過)~~

## Run Suzy-Q or Type C Servo V4

_chroot 环境下:_

```bash
cros_sdk --no-ns-pid

sudo servod -b board_name  

```

## Flash Cr50 FW with Suzy Q or Type C Servo V4

_chroot 环境下:_

```bash
sudo emerge chrome-cr50   #需要连接VPN

sudo gsctool -a /opt/google/cr50/firmware/cr50.bin.prod or local_build.bin

#You might need to use cr50.bin.prepvt if your device is pvt 

```

## Flash EC with Suzy Q or Type C Servo V4

_chroot 环境下:_

```bash
#注意事項
#1.DUT Cr50 FW 必須大於0.3.X的版本
#2.Factory Mode 必須是Enable 狀態, 即執行'gsctool -a -I'結果爲全'Y'

~/trunk/src/platform/ec/util/flash_ec --board={BOARD} --image ec.bin
```

## Flash BIOS with Suzy Q or Type C Servo V4

_chroot 环境下:_

```bash
#注意事項
#1.DUT Cr50 FW 必須大於0.3.X的版本
#2.Factory Mode 必須是Enable 狀態, 即執行'gsctool -a -I'結果爲全'Y'

sudo flashrom -p raiden_debug_spi:target=AP -w bios.bin

#Use cros flash-ap flash bios (Jasper Lake platform only)
#cros flash-ap -b name --flashrom -i bios.bin --debug   #Deprecated
```

## 如何不拆電池即可Enable Factory Mode

**CCD (Case Closed debug)已结案调试**

```bash
#注意事項,不拆電池即可Enable Factory Mode,必須基於DUT Cr50 version 是0.3.9+ / 0.4.9+
#否則請乖乖拆電池,再去手動Enable factory mode或透過cr50 console去執行.

#1.將Factory Mode Disable的機器透過Suzy-Q 或 Servo V4 連接到host
#2.在host 端打開一個cr50 console 
#3.Run command 'ccd' in cr50 console 可以檢查到此時DUT factory mode 狀態爲Disable
#4.Run command 'ccd open',會提示"Starting CCD open...,Press the physical button now!"
#的字樣,此時根據cr50 console 提示信息去按power button.特別注意, 這裏按Power button的
#次數無限制也無規則,follow console 提示即可. Google 官方文件說一般在5 mins 以內.
#5.當看到"endorsement_complete(): SUCCESS"的字樣,說Factory Mode Enable成功
#同時可以看到DUT 自動reboot.看到這2個現象,說明你已經成功了.
#6.Run command 'ccd' in cr50 console 可以檢查到此時DUT factory mode 狀態爲Enable.
```

## 如何抓kernel log


```bash
#注意事項
#1. 抓取kernel log的test image必須是with USE="pcserial tty_console_ttyS0",正常的test image這個flag爲DIsable狀態
#需要手動add 這個flag再rebuild test image. (參考issue b/155852099#comment50)
#2. kernel log 最後是透過bios log 呈現的,所以一樣還是需要刷serial bios去捕獲ap log;

#抓取步驟
#[1]:
#earlyprintk=ttyS0,115200n8 console=tty1 console=ttyS0,115200n8 #這裏是可變的,因爲這裏會根據不同問題去增加不同的kernel command
#[2]:
#a. enter VT2
#b. /usr/share/vboot/bin/make_dev_ssd.sh --edit_config --partition 2
#c. append the [1] to the kernel command line (note, don't remove anything from the kernel command line)
#d. reboot the system and check the AP log, you will see the kernel log output to the AP console. 
#   Please attach the capture log if you can't determine it is correct or not.
#e. reproduce the problem with servo board/suzy q and capture the AP uart log.

#參考issue: b/152377365#comment19 
```



