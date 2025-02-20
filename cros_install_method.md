# Chrome OS Install Method

## DD Image 到 U 盘, 制作 Chrome OS 安装盘

dd 是 Unix 和类 Unix 操作系统的主要命令行实用程序, 目的是转换和复制文件.

**参考链接**
[Unix DD](<https://en.wikipedia.org/wiki/Dd_(Unix)>)

### 磁盘格式化操作

* 清除磁盘分区并格式化为 FAT32 格式, sdx 指磁盘名称.

  1. 插入 U 盘
  2. 运行 `fdisk -l` 检查磁盘名称, 在 Ubuntu 系统需要加`sudo`
  3. 格式化磁盘 : `mkfs.vfat -F 32 dev/sdx –I`

* 将磁盘格式化为 NTFS 格式

  [详细连接](https://linux.die.net/man/8/mkfs.ntfs)

  `mkfs.ntfs -f -Q /dev/sdx`

### Linux 环境下 dd

```bash
#易误操作, 不推荐
sudo dd if=image_name.bin of=/dev/sdx bs=4M iflag=fullblock oflag=dysnc

# 清空磁盘(全零填充)，谨慎使用
sudo dd if=/dev/zero of=/dev/sda
```

### Windows 环境下

_使用 "Win32 Disk Imager" 程序._

1. 打开 Win32 Disk Imager 程序
2. 选择 Image 文件以及磁盘
3. 点击写入映像到磁盘
4. 等待写入完成

### Chroot 环境下 (推荐)

```bash
cros flash usb:// image.bin
```

### 使用 Chromebook Recovery Utility 制作（推荐）

1. 安装并打开 Chrome 浏览器`https://www.google.com/chrome/`
2. 安装 `Chromebook Recovery Utility`
   link： `https://chrome.google.com/webstore/detail/chromebook-recovery-utili/jndclpdbaamdhonoechobihbbiimdgai`
3. 打开 `Chromebook Recovery Utility`
4. 点击右上角`Settings`图标.
5. 首先选择 `Erase recovery media`以格式化 U 盘.
6. 在 dropdown 目录，选择插入的 USB 以进行格式化
7. 点击 `Continue`，然后点击`Erase now`.
8. 再次点击`Settings`图标
9. 点击 `Use local Image` 选项并选择要制作的档案.
10. 选择要使用的 U 盘.
11. 点击 `Continue`开始制作并等待制作完成.

## 从 USB 启动安装 Test Image

1. 将系统切换至开发者模式.
2. 在 VT2 下设定 U 盘为第一启动项:

   `chromeos-firmwareupdate --mode=todev`

   `crossystem dev_boot_usb=1 或 enable_dev_usb_boot`

3. 插入含有 Test image 的 U 盘并重新启动设备，并按 <ctrl + U >从 U 盘启动
4. 在 VT2 输入 `chromeos-install`，提示时选择 "y"，将会开始安装，大约需 5 分钟左右
5. 移除 U 盘并重新启动设备

## 手动安装出货系统

1. 按下 `Esc + Refresh + Power` 组合键进入 recovery mode.
2. 屏幕将会显示 chromeos missing 或者 recovery mode.
3. 接入制作好的含 Recovery image 的 USB, 系统将会自动开始安装.

## 修改工厂映像 rootfs 文件系统并禁用只读系统

> 仅针对某些项目的特殊要求

1. 解除工厂测试映像 rootfs verification (chroot 环境下):

    ```bash
    make_dev_ssd.sh --force --remove_rootfs_verification -i factory_bundle_cyan_20151118_pvt/factory_test/chromiumos_factory_image.bin
    ```

2. 挂载工厂测试映像，替换指定文件，如下为替换 mosys

    ```bash
    ./factory_setup/mount_partition.sh ./factory_test/chromiumos_factory_image.bin 3 /mnt/factory_bundle/
    ```

    replace `/mnt/factory_bundle/usr/sbin/mosys`

3. 取消挂载 factory bundle

    ```bash
    sudo umount /mnt/factory_bundle
    ```

4. 恢复 rootfs verification (chroot 环境下):

    ```bash
    make_dev_ssd.sh --force --noremove_rootfs_verification -i factory_bundle_cyan_20151118_pvt/factory_test/chromiumos_factory_image.bin
    ```

## 强制安装 test image 到磁盘

`chromeos-install --dst /dev/mmcblk0`

## 制作 Factory Image (Python 架构)

在 2018 年之前机种，使用 python 框架，需要制作 factory image.

将 factory toolkit 安装到 test image 并生成 factory image.(chroot 环境下)

1. 在 CPFE 查找对应 Board Name Source [CPFE](https://www.google.com/chromeos/partner/fe/)
2. 下载 `FACTORY_IMAGE_ARCHIVE` 并解压缩
3. 下载 `TEST_IMAGE_ARCHIVE` 并解压缩 `factory_test/chromiumos_test_image.bin`
4. 安装 factory toolkit 到 test image

   `./factory_toolkit/install_factory_toolkit.run ./factory_test/chromiumos_test_image.bin --yes`

5. 重命名 `test image` 为 `factory image`

   `mv ./factory_test/chromiumos_test_image.bin ./factory_test/chromiumos_factory_image.bin`

## 从 USB 启动并安装测试映像

1. Change OS mode to developer mode.
2. Boot into the device. The device should say "OS verification is OFF".
3. Press Ctrl-D skip show message and quick boot.
4. Switch to VT2 (Ctrl+Alt+F2; F2 is the "Forward" button).
5. Log in as root
6. Run:

   `chromeos-firmwareupdate --mode=todev`

   `crossystem dev_boot_usb=1`

   `enable_dev_usb_boot`

   This enables USB boot.

7. Insert the USB drive.
8. Run reboot
9. When you see "_OS verification is OFF_", press Ctrl-U to boot into the USB drive.
10. Switch to VT2 (Ctrl+Alt+F2). Log in as root
11. Run `chromeos-install`. Type "y" at the prompt. This installs the factory
    image to the eMMC storage device. It takes about 5 minutes; when finished
    you will see "_Please shutdown, remove the USB device, cross your fingers
    and reboot_."
12. Run reboot
13. You will see "OS verification is OFF". Remove the USB driver.
