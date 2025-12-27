# netboot download flow
## 将你的vmlinuz及配置放置到WDS server端.
```
## 例如:
1.将"factory_shim/netboot/vmlinuz"上传到 //40.31.1.55(77/66)/chromebot/lazor/vmlinuz
  -->这里的vmlinuz 必须与该Project Branch里面的test_image版本一样
  -->//40.31.1.55(77/66)为你当线的WDS Server IP
2.在//40.31.1.55/77/66/创建omahaserver_lazor.conf
  -->此文件会随着后续讲的balance所控制
  -->里面的内容为所使用的Dome Server IP 以及Port:"http://40.31.1.48:8510/update"
```
## 配置你的机种的netboot BIOS
```bash
./setup/image_tool netboot -i image-${board}.net.bin -o ${board}_fa_net.bin --bootfile chrome-bot/${board}/vmlinuz --board ${board} --arg nocompleteprompt
#{board}与omahaserver_{board}.conf 相同
```
## 输出结果如下:-->以lazor为例
```bash
Reading from image-lazor.net.bin...
Current settings:
{'argsfile': b'chrome-bot/lazor/cmdline',
 'bootfile': b'chrome-bot/lazor/vmlinuz',
 'kernel_args': b'',
 'tftp_server_ip': b''}
Settings modified. New settings:
{'argsfile': b'chrome-bot/lazor/cmdline',
 'bootfile': b'chrome-bot/lazor/vmlinuz',
 'kernel_args': b'cros_board=lazor 'nocompleteprompt', ## nocompleteprompt,完成后直接进入os,无需敲击Enter 
 'tftp_server_ip': b''}
Generating output to lazor_fa_net.bin...
```
## 设置gbb_flags,在原有的基础上加1000,就是disable lid switch:-->以lazor为例
```
gbb_utility -g --flags lazor_fa_net.bin 
-->flags: 0x00000039

gbb_utility -s --flags=0x1039 lazor_fa_net.bin 
-->successfully saved new image to: lazor_fa_net.bin

gbb_utility -g --flags lazor_fa_net.bin 
-->flags: 0x00001039

```
## Nissa系列更新R121之后, ME会在Netboot DL时进行UnlockME动作,容易出现Unlock失败,所以直接使用UnlockME的BIOS来避免此问题.
```
(chroot)ifdtool -p adl -g bios.bin -O bios_unlock.bin   # -p参数为bios平台, -g表示解锁 , -O 生成
File bios.bin is 16777216 bytes
Value at GPRD offset (21) is 0x814f0001
--------- GPR0 Protected Range --------------
Start address = 0x00001000
End address = 0x0014ffff
Writing new image to bios_unlock.bin
GPR0 protection is now disabled    
#出现now disabled字样表示原BIOS为LockME,-O生成的为UnlockME BIOS. 
#如果是GPR0 protection is already disabled. 表示原BIOS已经是UnlockME的状态.

ifdtool -p adl -c bios.bin   # -c check GPRO ststus  Enable -> ME Lock  Disable -> ME unlock

PVT阶段 BIOS 默认Lock ME, 测试流程也会保持ME Lock 到WIPE
```

## Repack FirmwareUpdater 需要上传至DL bundle(set gbb_flags disable lid)
```
./chromeos-firmwareupdate --repack folder

```

## 配置你的机种的balance
``` 
## 这是for 平衡Dome Server Loading,避免只在同一台Server 下载image 
## 这只是在一定的时间段内去替换前文所讲的omahaserver_lazor.conf
## 配置你的机种balance.ini,以ZC9为例：
```
```bash
  1 [Setting0]                                                                                                                                                                                                                                
  2 WDS11_IP=40.33.1.46
  3 WDS11_PORT=10120
  4 WDS12_IP=40.33.1.46
  5 WDS12_PORT=10130
  6 WDS13_IP=40.33.1.48
  7 WDS13_PORT=10120
  8 TIME=300
  9  
 10 [Setting1]
 11 WDS11_IP=40.33.1.48
 12 WDS11_PORT=10130
 13 WDS12_IP=40.33.1.49
 14 WDS12_PORT=10120
 15 WDS13_IP=40.33.1.49
 16 WDS13_PORT=10130
 17 TIME=300
 18  
 19 [Setting2]
 20 WDS11_IP=40.33.1.49
 21 WDS11_PORT=10130
 22 WDS12_IP=40.33.1.46
 23 WDS12_PORT=10120
 24 WDS13_IP=40.33.1.46
 25 WDS13_PORT=10130
 26 TIME=300
 27  
 28 [Setting3]
 29 WDS11_IP=40.33.1.48
 30 WDS11_PORT=10130
 31 WDS12_IP=40.33.1.48
 32 WDS12_PORT=10120
 33 WDS13_IP=40.33.1.49
 34 WDS13_PORT=10120
 35 TIME=300
 36  

```
## 开启Balance.py
```bash
这里的balance需要放置于

~/shopfloor/balance/bobba$ tree 
.
├── A_Line                         ==>线别
│   ├── balance_zak.py
│   └── zak.ini
├── B_Line
│   ├── balance_zak.py
│   ├── multiple_WDS_setting
│   │   ├── balance_zak.py
│   │   └── zak.ini
│   └── zak.ini
└── C_Line
    ├── balance_zak.py
    └── zak.ini

将你的机种配置更改到balance.py并按着线别执行
sysadmin@localhost:~/shopfloor/balance/zc8_sample/D_LINE$ sudo python balance_${project}.py 
[sudo] password for sysadmin: 
start,keeping monitor........
```
```
然后通过产生的monitor log 去判断有无执行成功
2021-08-11 17:04:04,064:INFO:start
2021-08-11 17:04:05,078:INFO:**************************************************************************************
2021-08-11 17:04:05,080:INFO:zak:WDS 11 server write http://40.33.1.46:10120/update in conf file,delay 300s
2021-08-11 17:04:05,082:INFO:zak:WDS 12 server write http://40.33.1.46:10130/update in conf file,delay 300s
2021-08-11 17:04:05,085:INFO:zak:WDS 13 server write http://40.33.1.48:10120/update in conf file,delay 300s
2021-08-11 17:04:05,085:INFO:**************************************************************************************
2021-08-11 17:09:06,199:INFO:**************************************************************************************
2021-08-11 17:09:06,201:INFO:zak:WDS 11 server write http://40.33.1.48:10130/update in conf file,delay 300s
2021-08-11 17:09:06,204:INFO:zak:WDS 12 server write http://40.33.1.49:10120/update in conf file,delay 300s
2021-08-11 17:09:06,206:INFO:zak:WDS 13 server write http://40.33.1.49:10130/update in conf file,delay 300s
2021-08-11 17:09:06,207:INFO:**************************************************************************************
2021-08-11 17:14:07,243:INFO:**************************************************************************************
2021-08-11 17:14:07,246:INFO:zak:WDS 11 server write http://40.33.1.49:10130/update in conf file,delay 300s
2021-08-11 17:14:07,249:INFO:zak:WDS 12 server write http://40.33.1.46:10120/update in conf file,delay 300s
2021-08-11 17:14:07,250:INFO:zak:WDS 13 server write http://40.33.1.46:10130/update in conf file,delay 300s
2021-08-11 17:14:07,250:INFO:**************************************************************************************
2021-08-11 17:19:08,368:INFO:**************************************************************************************
2021-08-11 17:19:08,371:INFO:zak:WDS 11 server write http://40.33.1.48:10130/update in conf file,delay 300s
2021-08-11 17:19:08,374:INFO:zak:WDS 12 server write http://40.33.1.48:10120/update in conf file,delay 300s
2021-08-11 17:19:08,377:INFO:zak:WDS 13 server write http://40.33.1.49:10120/update in conf file,delay 300s
2021-08-11 17:19:08,377:INFO:**************************************************************************************
2021-08-11 17:24:08,477:INFO:current circle:1

```
## Dome Server 注意事项
* Bundle:一包SF过站 + 6包 Download 
* complete:要取消敲击回车和空格才能重启进入OS的机制，这里要注意complete script 和 net boot bios 的setting
* HWID Bundle：放置于SF 过战Bundle
* Firmware: 要设置GBB Flag,避免在Download 过程中黑屏或Download 完成后在合盖时，不能开机
* toolkit:
  1. 要专门设置 For Flash netboot BIOS 的测试列表

```bash
{
  "inherit": [
    "common.test_list",
    "generic_main.test_list"
  ],
  "label": "GML Flash Net Boot BIOS",
  "tests": [
    "SMT"
  ],
  "constants": {
    "phase": "PVT",
    }
  },
  "options": {
    "engineering_password_sha1": "6848bf3617a2cf82bd5260a146d4a3236a20badc",
    "SMT": {
      "label": "(GML Flash Net Boot BIOS)",
      "subtests": [
        "SMTEnd"
      ]
    },
    "255": {
      "inherit": "ExecShell",
      "label": "SKUID=255",
      "args": {
        "commands": "ectool cbi set 2 255 4"
      }
    },
    "SMTEnd": {
      "subtests": [
        "FlashNetBIOS",
     	"Barrier",
        "255",
	    "Barrier",
        "RebootStep"
      ]
    }
  }
}

```
  2. 要放置netboot BIOS 至toolkit,统一路径:`/usr/local/factory/board/${board}_net_fa.bin`
  3. 在SMT测试列表中，S/P的M/B 不能刷NetBoot BIOS
