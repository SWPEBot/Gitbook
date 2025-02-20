# Linux/ChromeOS Check Hardware

## Check All Hardware Information

```bash
lshw
```

## Check CPU Info

1. Via command lscpu

   ```bash
   lscpu
   ```

2. Via command cpuinfo

   ```bash
   cat /proc/cpuinfo
   ```

3. Check CPUID via command dmidecode

   ```bash
   dmidecode -t 4
   ```

4. To count the number of processing units use grep with wc

   ```bash
   cat /proc/cpuinfo | grep processor | wc -l
   ```

5. Get the actual number of cores, check the core id for unique values

   ```bash
   cat /proc/cpuinfo | grep 'core id'
   ```

## Check Memory

1. Check Memory Size

   ```bash
   dmidecode -t memory | grep -i size
   ```

2. Check Memory Info

   ```bash
   cat /proce/meminfo
   ```

## Check Disk

1. Check Disk List

   ```bash
   sudo fdisk -l
   ```

## Chrome OS Check Hardware Information

**Chrome OS 检查硬件信息.**

```bash
#ctrl N open chrome browser
chrome://system
```
