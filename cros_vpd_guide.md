# Chrome OS VPD Guide

ChromeOS Vital Product Data (VPD) Binary Blob

**[VPD Field Requirements](https://www.google.com/chromeos/partner/fe/docs/factory/vpd.html)**

Overview

Chrome OS supports device-specific information stored in a key-value style
storage area named "Vital Product Data (VPD)". Each Chrome OS has two AP
firmware sections reserved: RO_VPD and RW_VPD.

The binary structure and representation format of VPD (and utility usage) is
published on Chromium OS source VPD repository, and what (and how) should be
filled into VPD fields are covered here.

**Chromium OS VPD
[Source Code](https://chromium.googlesource.com/chromiumos/platform/vpd/)**

## 清除 RW 和 RO VPD 内容

```bash
vpd –i RW_VPD –O && vpd -i RO_VPD -O
```

## 重新启动所有工厂测试并清除所有 vpd 数据

```bash
factory_restart -a -d && reboot
```

## 查看 VPD 信息

```bash
vpd -i RW_VPD -l && vpd –i RO_VPD –l
```

## 写入 VPD

```bash
vpd -i RO_VPD -s region=us
```
