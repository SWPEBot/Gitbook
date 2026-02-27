## Install Dome for android project

1. follow install docker 
```bash
./cros_docker update
./cros_docker pull
./cros_docker install
./cros_dcoker run
```
2.默认端口http://localhost:8000/ 
3.创建项目并勾选 **Is this an Andorid project** 
4.Fastboot 上传Dome必要文件 **android_apk android_preflash_img (*.img) gpt_bin (*.bin) ota_zip (*.zip)**
**android_apk** from PDK local built
**android_preflash_image** from image_tool.py built
**gpt_bin** from image_tool.py built
**ota_zip** from [andorid ci](ci.andorid.com)
```bash
image_tool.py \
-d /path/to/userdebug_image.bin \
-o /path/to/output/preflash.img \
-g /path/to/output/mbr-gpt.bin \
--user_data_size USER_DATA_SIZE_IN_GiB \
--gpt_sector_size SECTOR_SIZE_IN_BYTE \
--factory_app /path/to/factory_app.apk \
--ota_package /path/to/ota_package.zip
```
目前只能逐个文件上传，无法bundle 方式完成
## Set fastboot Service
**dutipaddrs:** 填写fastboot 需要扫描的 IP 地址。此字段接受单个 IP 地址或 CIDR 范围
## 常见的CIDR      - IP计算方式: ipv4 一共32位  IP个数2^(32-CIDR)  例如 2^(32-24)=256
| CIDR | 主机位 | 地址总数  |
| ---- | --- | ----- |
| /24  | 8   | 256   |
| /23  | 9   | 512   |
| /22  | 10  | 1024  |
| /21  | 11  | 2048  |
| /20  | 12  | 4096  |
| /19  | 13  | 8192  |
| /18  | 14  | 16384 |
| /17  | 15  | 32768 |
| /16  | 16  | 65536 |

**ScanInterval**：调用 nmap 扫描 ``dutIpAddrs`` 中所有 IP 地址的间隔。默认10秒  
i. 10 秒是 1 个 CIDR 范围的推荐间隔。如果使用更多CIDR范围，则可能需要增加间隔。

**BoardName**：填写 DUT 的型号名称，因为新设备中没有“板”的概念。用户空间 fastboot 使用此字段来检查 DUT 是否是我们要刷写的目标设备。

**ModelName**：待测设备 (DUT) 的型号名称，固件 fastboot 使用此名称检查 DUT 是否为要刷写的目标设备。
