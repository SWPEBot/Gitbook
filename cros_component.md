# Component

## Component Firmware

### Manually Update ELAN touch firmware

```bash
# Deprecated
mount -o remount, rw /
echo 1 > /sys/bus/i2c/devices/ELAN0000:00x0/update_fw
reboot
```

### Manually Update wacom digitizer firmware

> r  -> read
> w  -> write

```bash
# Update
wacom_flash /lib/firmware/wacom2_firmware_12.hex -a i2c-11

# Read FW Version
wacom_flash W9013_0661.hex 1 i2c-11

```

### Manually Update Raydium Touchscreen Firmware

> Test on project aleena, firmware version show 255.255

```bash
# First need disable read-only system
/usr/share/vboot/bin/make_dev_ssd.sh --remove_rootfs_verification --partitions 2

reboot

# Create symbol link of firmware file
ln -sf /opt/google/touch/firmware/raydium_0xa222173f_2.6.bin /lib/firmware/raydium_0xffffffff.fw

# Force update firmware
echo 1 > /sys/bus/i2c/devices/i2c-RAYD0001:00/update_fw

# For debug check error message
# dmesg | grep i2c

reboot

# Check firmware Update success
cat /sys/bus/i2c/devices/i2c-RAYD0001:00/fw_version
```

### Manually Update FingerPrint Firmware

Firmware file path: `/opt/google/biod/fw/nami_fp_xxx.bin`
Flash Tool path: `/usr/local/factory/sh/flash_fp_mcu`

```bash
# DO flash
/usr/local/factory/sh/flash_fp_mcu /opt/google/biod/fw/nami_fp_xxx.bin
```

### Check Synaptics Touchpad Firmware

```bash
rmi4update -p -d /dev/hidraw0
or
rmi4update -p -d /dev/hidraw1
```
