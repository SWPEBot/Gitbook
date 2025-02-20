# How to disable the lid function in the factory image

## Disable lid via ectool

### DUT執行命令

```bash
ectool forcelidopen 1 # Disable lid
ectool forcelidopen 0 # Enable lid
```

### 缺點

1. 執行完Disable lid command需要重啓系統才能生效
2. Ec reboot 或者cutoff 之後, disable lid功能會失效 

綜上, 建議2020/07 之後的NPI Chrome book不再使用ectool disable lid的方法
改用set gbb flags的方式去disable lid. 具體操作如下.


## Disable lid via set gbb flags(recommend method)

### DUT執行命令

```bash
/usr/share/vboot/bin/set_gbb_flags.sh 0x1039 # Disable Lid 
/usr/share/vboot/bin/set_gbb_flags.sh 0x39 # Enable lid
/usr/share/vboot/bin/get_gbb_flags.sh -e # Print list of what flags are set
```
### 優點

1. 執行完Disable lid command後立即生效,包含在UpdateFirmware測試項後緊跟set gbb flags爲0X1039
   然後盒蓋,對UpdateFirmware之後的Reboot也是生效的
2. Ec reboot 或者cutoff 之後, disable lid功能仍舊會保持 
3. 使用set gbb flags disable lid後不會對'WriteHWID'測試項造成影響 
4. 使用set gbb flags disable lid後不會對'Lid Switch'測試項造成影響 
5. Finalize 測試項有包含清除gbb flags的設定,所以測試流程不需要額外增加測試項目去復原Lid功能
5. 組合鍵Download RMA Shim(power + reflash + esc)不會受到影響


## 如何把SMT預燒錄BIOS直接設定gbb flags爲0X1039

### 操作步驟

1. 準備待預燒錄的bios文件(量產之後都是從recovery image中提取firmware updater再解壓得到單獨的bios ec bin)
2. 進入chroot 環境, 利用gbb_utility開始設定, 之後再把設定之後的BIOS 提供給SMT 預燒錄.
```bash
gbb_utility -s --flags 0x1039 input_file.bin -o output_file.bin # 將預燒BIOS設定爲gbb flags爲0X1039
gbb_utility -g --flags need_check_file.bin # 檢查目前bios gbb flags 設定值爲多少
```

### 優點

1. 如果預燒錄的BIOS 已經將gbb flags 設定爲0X1039, 那麼第一次開機插上電源然後開蓋等到'小感叹号'顯示後
   在盒蓋,系統仍舊可以noraml boot 到factory image.(如果沒有設定gbb flags '小感叹号'畫面盒蓋會關機)
2. 儘管預燒錄的BIOS有設定gbb flags,按組合鍵Download RMA Shim(power + reflash + esc)不會受到影響
