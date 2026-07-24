## Install Dome for android project

1. follow install docker 
```bash
./cros_docker update
./cros_docker pull
./cros_docker install
./cros_dcoker run
```
- 默认端口http://localhost:8000/ 
- 创建项目并勾选 **Is this an Andorid project** 
- Fastboot 上传Dome必要文件 **android_apk android_preflash_img (*.img) gpt_bin (*.bin) ota_zip (*.zip)**
- **android_apk** from PDK local built
- **android_preflash_image** from image_tool.py built
- **gpt_bin** from image_tool.py built
- **ota_zip** from [andorid ci](ci.andorid.com)
```bash
./tools/image_tool.py  -d ./android-desktop_image.bin -o ./preflash.img -t ./ocicat-ota.zip -g ./gpt.bin --factory_config ./tools/proto/factory_config.txtpb --preload_partition_zip ./ocicat-img.zip --gpt_sector_size 4096 --factory_app {factoryapp}
```
目前只能逐个文件上传，无法 bundle 方式完成

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

**BoardName**：填写 DUT 的型号名称例如:``ocicat``，因为新设备中没有“板”的概念。用户空间 fastboot 使用此字段来检查 DUT 是否是我们要刷写的目标设备。

**ModelName**：待测设备 (DUT) 的型号名称例如``ocicat``，固件 fastboot 使用此名称检查 DUT 是否为要刷写的目标设备。
```bash
# ALl name
fastboot -s tcp{ip}:5554 getver product
ex
product: Ocicat
```

**ufsProvision**: ufs storage 让设备读取 UFS descriptor,并把结果放进 staged 区,把 staged 区内容导出来
```bash
fastboot -s tcp:{ip}:5554 oem read-ufs-descriptor:0.0
fastboot -s tcp:{ip}:5554 get_staged /tmp/device_descriptor.bin
fastboot -s tcp:{ip}:5554 oem read-ufs-descriptor:7.0
fastboot -s tcp:{ip}:5554 get_staged /tmp/geometry_descriptor.bin
fastboot -s tcp:{ip}:5554 oem read-ufs-descriptor:1.0
fastboot -s tcp:{ip}:5554 get_staged /tmp/config_descriptor.bin
/path/to/factory_ufs provision-file -d device_descriptor.bin -g geometry_descriptor.bin -c config_descriptor.bin -o provision_config_descriptor.bin
fastboot -s tcp:{ip}:5554 stage provision_config_descriptor.bin
fastboot -s tcp:{ip}:5554 oem write-ufs-descriptor:1,0
......Reboot DUT
```
**broadcastPing**: 添加网卡名例如enp130s0 网卡网关例如10.17.5.255


## Set 


