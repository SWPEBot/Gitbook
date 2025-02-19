# Astronaut LTE 测试示例

## 相关链接

[mmcli man page on Slackware](http://www.polarhome.com/service/man/?qf=mmcli&tf=2&of=Slackware&sf=8)

[ME909S-821 4G-LTE 模块在 Linux 系统下拨号上网测试](https://blog.csdn.net/CSDNhuaong/article/details/74910569)

[GSM 之 AT 操作命令详解](https://www.cnblogs.com/yuweifeng/p/5587473.html)

[Cellular Modems (GSM/GPRS/EDGE/UMTS/HSPA/LTE)](http://trac.gateworks.com/wiki/wireless/modem)

[VSWR && TxRX Function Test Sop](共享文件中心)

~~[无线信号强度解析及 linux 如何查看 wifi 信号强弱等](https://blog.csdn.net/lbyyy/article/details/51392629)~~

## 查询附近有效运营商信号

```bash
mbimcli -p -d /dev/cdc-wdm0 --query-visible-providers
```

## 获取信号 CSQ 强度

```sh
# Need to insert SIM Card
echo at+cops? > /dev/ttyACM0 | head -n 4 /dev/ttyACM0
echo at+csq > /dev/ttyACM0 | head -n 4 /dev/ttyACM0
```

## RSSI 测试设置命令

```sh
at@xns:init()
at@xns:rat_nonsig_rx_start(<rat>,<band>,<channel>,<bandwidth>,<main/div>,<expected_rxlevel>,0)
at@xns:nonsig_rx_stop()
```

## 参数说明

```sh
<rat>               0: 2G
                    1: 3G
                    2: 4G
                    3: TDSCDMA
<band>              Band,
                    e.g. 1/3/19/21/28
<channel>           DL channel,
                    e.g. 300/1575/6075/6525/9435
<bandwidth>         0: 1.4M
                    1: 3M
                    2: 5M
                    3: 10M
                    4: 15M
                    5: 20M
<main/div>          0: main antenna
                    1: diversity antenna
                    2: both main and diversity antenna
<expected_power>    dB*16
```

## 使用 AT 命令测试 RSSI

> 已知 Channel

| Type          | Band | Channel | Bandwidth |   Mark   |
| ------------- | ---- | ------- | --------- | -------- |
| CHN-UNICOM 3G | 1    |  10700  |    20M    |          |
| CHN-UNICOM 4G | 3    |  1765   |    10M    | priority |
| CHN-TELCOM 4G | 3    |  1780   |    20M    |          |


```sh
at+cfun=15                                       # need waiting 5s
at@xns:init()                                    # double check
at@xns:rat_nonsig_rx_start(1,1,10700,0,0,-800,0) # main
at@xns:rat_nonsig_rx_start(1,1,10700,0,1,-800,0) # aux
at@xns:rat_nonsig_rx_start(1,1,10700,0,2,-800,0) # double
at@xns:nonsig_rx_stop()                          # stop rssi test
```

## 信号检查

```sh
0  <-113 dBm poor, signal breaks up and all kinds of nasty
1  -111 dBm poor, signal breaks up and all kinds of nasty
2  -109 dBm works, but signal fluctuates, especially upload
3  -107 dBm works, but signal fluctuates, especially upload
4  -105 dBm works, but signal fluctuates, especially upload
5  -103 dBm works, but signal fluctuates, especially upload
6  -101 dBm works, but signal fluctuates, especially upload
7  -99 dBm still better than ADSL
8  -97 dBm still better than ADSL
9  -95 dBm still better than ADSL
10 -93 dBm still better than ADSL
11 -91 dBm still better than ADSL
12 -89 dBm full download, good upload
13 -87 dBm full download, good upload
14 -85 dBm full download, good upload
15 -83 dBm full download, good upload
16 -81 dBm full download, good upload
17 -79 dBm excellent! good signal and ping
18 -77 dBm excellent! good signal and ping
19 -75 dBm excellent! good signal and ping
20 -73 dBm excellent! good signal and ping
21 -71 dBm excellent! good signal and ping
22 -69 dBm excellent! good signal and ping
23 -67 dBm excellent! good signal and ping
24 -65 dBm excellent! good signal and ping
25 -63 dBm excellent! good signal and ping
26 -61 dBm excellent! good signal and ping
27 -59 dBm you're right next to the cell tower!
28 -57 dBm you're right next to the cell tower!
29 -55 dBm you're right next to the cell tower!
30 -53 dBm you're right next to the cell tower!
31 >-51 dBm you're right next to the cell tower!
32 99 not known or not detectable
```
