# Chrome OS Firmware

## What is Chrome OS Firmware

[Official Document](https://www.google.com/chromeos/partner/fe/docs/firmware-specs/firmwareoverview.html)
{Note}: This link requires permission to open.

## Get firmware updater from release image

`image_tool get_firmware -i recovery_image.bin -o firmware`

## Extract AP/EC Firmware from firmware updater

Extract to current folder

`./chromeos-firmwareupdate --sb_extract .`

Extract to target folder

`./chromeos-firmwareupdate --sb_extract ./et/`

## Get Firmware Hash

```bash
gooftool get_firmware_hash --file bios.bin
goottool get_firmware_hash --file ec.bin
```

## Chrome OS firmware gbb Flag

The GBB (Google Binary Block) flags are defined in the vboot_reference source. A
varying subset of these flags are implemented and/or relevant for any particular
board.

[Source Code Link](https://chromium.googlesource.com/chromiumos/platform/vboot_reference/+/master/firmware/include/gbb_header.h)

### Setting gbb flag

Check wirte protection status, setting gbb flag need disable SW write protect

`flashrom -p host --wp-disable/enable/status`

Normal firmware gbb flag(factory dev flag)

`/usr/share/vboot/bin/set_gbb_flag.sh 0x039`

Setting gbb developer mode for flash ec

`/usr/share/vboot/bin/set_gbb_flag.sh 0x239`

Close developer mode enter end user mode

`/usr/share/vboot/bin/set_gbb_flag.sh 0x000`

Disable lid function

`/usr/share/vboot/bin/set_gbb_flag.sh 0x1039`

### Check gbb flag and HWID info

Read DUT bios bin file

`flashrom -p ec -r /tmp/123.bin`

Check gbb flag

`gbb_utility -g --flags /tmp/123.bin`

Check hwid value

`gbb_utility -g --hwid /tmp/123.bin`

## Chrome OS Check and Update Firmware (BIOS/EC/PD)

**Update BIOS.**

```bash
flashrom –p host –w bios.bin
# Use flashrom flash BIOS will clear all vpd data, please careful use it.
# under factory image:
/usr/local/factory/board/chromeos-firmwareupdate --force --factory
```

**Update EC.**

```bash
flashrom –p ec –w ec.bin
```

**Update PD.**

```bash
flashrom –p ec:dev=1 --fast-verify -w pd.bin
```

**Only Update RW Firmware.**

_Deprecated._

```shell
/usr/local/factory/board/chromeos-firmwareupdate-rw --mode=recovery --wp=1
```

**Check firmware Updater version.**

_Under Chroot._

```bash
./chromeos-firmwareupdate -V
```

**Check and read ec info.**

```bash
mosys ec info
ectool version
flashrom -p ec -r ec.bak # read ec 16 byte bin file
```

**Check and read bios info.**

```bash
crossystem fwid
mosys smbios info bios
# read bios 16 byte bin file
flashrom -p host -r bios.bak
```

## Chrome OS crossystem Tool

**Check Firmware Version.**

```bash
# Check RW Firmware version
crossystem fwid
# Check RO Firmware version
crossystem ro_fwid
```

**Close virtual developer mode.**

```bash
crossystem disable_dev_request=1 && reboot
```

**Clear tpm.**

```bash
crossystem clear_tpm_owner_request=1
```

**Setting dev boot.**

```bash
crossystem dev_boot_usb=1
```

## Chrome OS ectool

_Before use ectool, please run ectool help check the usage._

**Check RO/RW ec version.**

```bash
ectool version
mosys ec info
```

**Check and control battery or power LED.**

```bash
# setting battery led
ectool led battery off/blue/green/yellow/auto
# setting power led
ectool led power off/blue/green/yellow/auto
```

**Check CPU themeral.**

```bash
ectool temps + (sensor ID)
```

**Use ectool control lid function.**

```bash
# open lid function
ectool forcelidopen 0
# close lid function
ectool forcelidopen 1
```

**Use ectool reboot ec.**

```bash
ectool reboot_ec cold at-shutdown
ectool reboot_ec
```

**Use ectool get/set fan speed.**

```bash
# set fan speed to 3000 rpm：
ectool pwmsetfanrpm 3000
# get fan speed：
ectool pwmgetfanrpm
# check fan status
ectool pwmgetnumfans # if fans = 1, the fan status normal
# set fan control to auto
ectool autofanctrl
```

## Flashrom command

1.Copy the firmware files into the USB

2.Mount the USB on the DUT

3.Flash the files

BIOS: `flashrom -p host -w bios.bin`

EC: `flashrom -p ec - w ec.bin`

PD: `flashrom -p ec:dev=1, block=0x800 - w pd.bin`

4.Reboot the DUT

## Check SW write protect status

1.check firmware write protect status `flashrom –p host --wp-status`

2.check ec write protect status `flashrom –p ec --wp-status`

3.enable firmware write protect `flashrom -p host --wp-enable`

4.disable firmware write protect `flashrom -p host --wp-disable`

## Check Hardware wrire protection status

refer link:
[crossystem](https://chromium.googlesource.com/chromiumos/platform/vboot_reference/+/master/utility/crossystem.c)

`crossystem | grep wpsw_cur`

## Checking machine information with mosys

1.  Check sku

    `mosys platform sku`

2.  Check model

    `mosys platform model`

3.  Check the chassis

    `mosys platform chassis`

4.  Check all information

    `mosys platform id`

## Chrome OS Region Config Path

`/usr/share/misc` or `regions_overly.py`

## 检查硬件写保护状态

参考链接:
<https://chromium.googlesource.com/chromiumos/platform/vboot_reference/+/master/utility/crossystem.c>

`crossystem | grep wpsw_cur`

## 使用 crossystem 检查 BIOS/Firmware 版本

```bash
# 检查 RW Firmware 版本
crossystem fwid

# 检查 RO Firmware 版本
crossystem ro_fwid
```

## 检查&设定软体写保护状态

```bash
# 检查固件写保护状态

flashrom –p host --wp-status

# 检查 ec 写保护状态

flashrom –p ec --wp-status

# 设置固件/ec 写保护

flashrom -p host --wp-enable
flashrom -p ec --wp-disable
```

## 关闭虚拟开发者模式

`crossystem disable_dev_request=1 && reboot`

## 设定 EC Reboot

```bash
ectool reboot_ec cold at-shutdown

ectool reboot_ec
```

## 获取 Firmware Hash

```bash
gooftool get_firmware_hash --file bios.bin
goottool get_firmware_hash --file ec.bin
```

## 通过 ectool 获取/设定 风扇转速

```bash
# 设定风扇转速为 3000 rpm：
ectool pwmsetfanrpm 3000

# 获取风扇转速：
ectool pwmgetfanrpm

# 检查风扇状态
ectool pwmgetnumfans # 如果fans = 1, 则风扇状态正常

# 设定风扇 control 为 auto
ectool autofanctrl
```

### How to Flash & Check firmware（BIOS/EC/PD)

1.  Flash BIOS

    `$ flashrom –p host –w bios.bin`

    **Use flashrom flash BIOS will clear all vpd data, please careful use it.**

    under factory image:

    `$ /usr/local/factory/board/chromeos-firmwareupdate --force --factory`

2.  Flash EC

    `$ flashrom –p ec –w ec.bin`

3.  Flash PD

    `$ flashrom –p ec:dev=1 --fast-verify -w pd.bin`

4.  Only Update RW Firmware

    `$ /usr/local/factory/board/chromeos-firmwareupdate-rw --mode=recovery --wp=1`

5.  Check firmware Updater Version

    `chromeos-firmwareupdate -V`

6.  Check and Read ec Info

    `$ mosys ec info`

    `$ ectool version`

    `$ flashrom -p ec -r ec.bak` {read ec 16 byte bin file}

7.  Check and read bios info

    `$ crossystem fwid`

    `$ mosys smbios info bios`

    `$ flashrom -p host -r bios.bak` {read bios 16 byte bin file}

8.  Check hexdump

    `$ hexdump -C /tmp/xxx.bin | less`

### Extract BIOS and EC bin file from firmware updater

1.  Local flash

    Switch to terminal screen {Ctrl + Alt + F2}
    Log in as root:

    `chromeos-firmwareupdate --sb_extract /tmp/xxxx.xxxxxxx`

    The DUT extract file to /tmp/xxxx.xxxxxxx

2.  Extract flash BIOS/EC only

    `./chromeos-firmwareupdate --sb_extract .` _extract to current folder_

    `./chromeos-firmwareupdate --sb_extract ./et/` _need mkdir et folder_

## 从 firmware updater 提取 BIOS EC

```bash
# 提取到当前目录,不推荐使用
./chromeos-firmwareupdate --sb_extract .

# 提取到et目录下
./chromeos-firmwareupdate --sb_extract ./et/

```
## 需要更新RO和RW 不同版本的Firmware 时，这里的image.bin 是在CPFE上下载对 应版本的firmware archive中提取的
```
futility update -i ro.serial.bin #Update RO BIOS
futility update -i rw.serial.bin --wp=1 #Update RW BIOS
```
