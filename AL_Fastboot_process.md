## Install Dome for android project

1. follow install docker 
```bash
./cros_docker update
./cros_docker pull
./cros_docker install
./cros_dcoker run
```
2. Fastboot 上传Dome必要文件 **android_apk android_preflash_img (*.img) gpt_bin (*.bin) ota_zip (*.zip)**
**android_apk** from PDK local built
**android_preflash_image** from image_tool.py built
```bash
image_tool.py -d /path/to/userdebug_image.bin -o /path/to/output/preflash.img -g /path/to/output/mbr-gpt.bin --user_data_size USER_DATA_SIZE_IN_GiB --gpt_sector_size SECTOR_SIZE_IN_BYTE --factory_app /path/to/factory_app.apk --ota_package /path/to/ota_package.zip
```
