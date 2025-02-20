# Chrome OS Cr50 Guide

cr50 - a special security chip asserts the WP signal. While a battery is
present on the device, the WP signal will be asserted. Disassembling the
device and physically disconnecting the battery causes WP to be deasserted.

## 检查 H1/Cr50 Firmware 信息

```bash
#cat /var/cache/cr50-version

#usb_updater -f -t

gsctool -f -t
```

检查本地 H1 固件文件版本

command :`/usr/sbin/gsctool -b /usr/local/factory/cr50.bin.prod`

## Check H1/Cr50 board ID space

```bash
stop trunksd
#usb_updater -s -i
#gsctool -s -i

gsctool -t -i
#board id space: xxxxxxxxxxxxxxxxxxxxxxxxx
```

## Cr50 Board ID Flag

```bash
ffff #来料状态
7f80 #未设定
7f7f #PVT 之前所有阶段flag
7f8f #PVT 及MP flag
**After 2021**
7f80 #PVT 及MP flag
注： board id space 前16 码为 rlz_brand_code, 后 8 位为board id flag
```

## Check Cr50 Factory Mode Status

```bash
gsctool -t -I
```
##Disablei/Enable Factory mode 

```bash
gsctool -a F disable/enable 
```
