# Chrome OS Factory Device Data

## Python list

| Block               | Type   | Summary            |
| ------------------- | :----- | ------------------ |
| factory device-data | string | any string for r/w |

## Json list

| Block               | Section   | Summary                                    |
| ------------------- | :-------- | ------------------------------------------ |
| factory device-data | serials   | string for serial_number,mlb_serial_number |
| ................... | component | string for device component                |
| ................... | factory   | string for factory data, test status       |
| ................... | vpd.rw    | string for vpd rw section data             |
| ................... | vpd.ro    | string for vpd ro section data             |

## 检查 factory device data

```bash
factory device-data
```

## 手动写入 device data

```bash
factory device-data vpd.ro.region=us
```

## 手动清除 device data 中项目

```bash
factory device-data -d vpd.ro.region
```
