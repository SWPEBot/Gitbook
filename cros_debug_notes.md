# Chrome OS Debug

## 获取系统 Debug Log

- **获取系统 log**

  `generate_logs`

- **获取 factory bug**

  ```bash
  # 生成 factory bug 在 tmp 目录下
  factory_bug

  # 将 factory bug 生成至USB 中
  factory_bug --usb
  ```

  通过 UI 左上角, 选择保存工厂记录到 U 盘

- **出货系统抓取 debug log**

  在 OOBE 之后, 登入系统，打开 Chrome 浏览器, 输入`chrome://net-internals/#chromeos`,
  选择保存 logs, log 将会保存至`Download`文件夹

## 使用 Chrome 浏览器连接 DUT

DUT 与 Host 处于同一网域

在浏览器 url 栏输入：`10.17.65.100:4012`

**注意: 端口 4012 是固定的.**

## 处理 Start 测试 Item Fail

_处理 python framework mini-omaha download error._

方式一

Set check_factory_install_complete of the dargs in 'start' test to be False.

方式二

Pre-generate the lsb-factory in factory test image stateful partition.

## 处理 Python framework 站别错误

```python
#Need modify: /usr/local/factory/py/test/shopfloor.py
def finalize():
  """Notifies shop floor server this DUT has finished testing."""
  device_data = GetDeviceData()
  get_instance().Finalize(get_serial_number(), device_data)
```

## Shopfloor service 设定双 BIOS 版本取值

```python
#need modify board_shopfloor.py
#key_translation add
‘BIDNum’: ‘BIOS2’,
#(this shopfloor contorl BIOS version）,

#Finilize setting
(‘BIOS’, device_data[‘BIOS2’])
```

## 修改工程模式密码

```shell
cd /usr/local/factory/py/test/test_lists/
grep -nr echo main.py
echo -n chrome | sha1sum >> newpassword.log
```

将生成的 sha1sum 修改至 main test_list 指定位置

## 使用 mosys 检查机器信息

1. 检查 sku

   `mosys platform sku`

2. 检查 model

   `mosys platform model`

3. 检查 chassis

   `mosys platform chassis`

4. 检查所有信息

   `mosys platform id`

## Chrome OS Factory UI 显示测项语言更改

Ubuntu Base 安装对应套件

```bash
sudo apt-get install gettext
sudo apt-get install poedit
```

反编译 mo 文件成 po 文件，使用 gettext 的 msgunfmt 工具，命令如下:

`msgunfmt test.mo -o test.po`

编码 po 文件为 mo 文件，使用 poedit 的 msgfmt 工具，命令如下:

`msgfmt test.mo -o test.po`

## 检查 dmesg 获取 debug 信息

```bash
dmesg | grep system   #check fail message，"system"

dmesg help | grep chargestate  #check message，"chargestate"
```

## 处理 Linux 下 umount 错误，提示 device is busy

```bash
# 方法一
sudo fuser -m -k /mnt/factorybundle/ #will auto kill busy process

# 方法二
fuser -m -v -i -k /media/BAK/  #will tell you select y/n

# 方法三
fuser ／mnt/dir #推荐
```

## 处理 make factory bundle 提示 image type is usb（mini-omaha download error）

**Python Framework, make image type shoould be recovery.**

```bash
factory_setup/lib/cros_image_common.sh

grep -w “root=“ | grep -w “cros_recovery”)”   #Fail
grep -w “root” | grep -w “cros_recovery”)”    #Pass
```

## 处理 Project ZHS（Olay）hwid 检查 Phase 错误

**Because olay gnawty used same board name , so need setting OLAY-PVT flag.**

```python
$/mnt/midas/dev_image/factory/py/test/phase.py

PHASE_NAMES = ['PROTO', 'EVT', 'DVT', 'PVT_DOGFOOD', 'PVT', 'OLAY-PVT']
```

## 处理 Gooftool probe Product ID 错误（Project ZHS）

```python
$/mnt/midas/dev_image/factory/py/gooftool/edid.py

product_id = read_short_le(PRODUCT_ID_OFFSET)    #Fail
product_id = read_short_be(PRODUCT_ID_OFFSET)    #Pass
```

## 文本过站手动 Pass D2 to 45（禁止使用）

**内部使用, 处理文本过站, Deprecated.**

1. Go to `10.18.6.61 Acer-M request`directory
2. under `Backup 2016` search SN info
3. Check OS status
4. Copy SN station data to `Acer-M` dir
5. Modify file name，delete OK string
6. Copy modified file to `Handshak` directory
7. Check machine data with Finish directory

## 手动验证 firmware keys

```bash
# 读取 firmware keys
flashrom -p host -r /tmp/fw_main_1234

# 检查 firmware keys
/usr/local/factory/sh/verify_keys.sh /dev/sda4 /tmp/fw_main_1234
```

## Chrome OS 将设备添加到黑名单

`/etc/modprobe.d/blacklist.conf`
e.g:`blacklist uvcvideo`

## Python 架构 Make Pre Flash Fail 处理方式

repo sync chroot 环境为最新

将`factory_bundle/factory_setup/lib/chromeos-common.sh`
替换为`chromiumos/chroot/usr/share/misc/chromeos-common.sh`

## 检查磁盘分区信息

`cgpt show /dev/sdx` 或 `cgpt show /dev/mmcblock0`

## 检查所有 device driver

`lsmod`

## Python Test Item 增加 retry 次数

```python
def BasicWifi(self):
FactoryTest(
        id=‘Wifi’,
        label_en=‘Wifi’,
        pytest_name=‘wireless’,
        retries=1,
  backgroundable=True)
```

## 检查 USB 设备路径及进程

`udevadm monitor`

## 检查 USB device & USB serial port

`lsusb -t`

`lsusb -v #show detail`

## Chrome OS Recovery Reasons 参考

Refer Link:
[recovery reasons](https://chromium.googlesource.com/chromiumos/platform/vboot_reference/+/master/firmware/2lib/include/2recovery_reasons.h)

## 在工厂模式下检查设备组件 (hwid probe)

```bash
gooftool probe | less
```

(After 2018/05, This method is not supported by new projects.)

New Method: `hwid probe`

---

## HWID generated file path

`/usr/local/factory/hwid/$BOARD`

## 工厂模式检查设备硬件详细信息

```bash
gooftool probe | less

# 2018/05 后新专案不适用

# 新机种请使用
hwid probe
```

## EMMC Sector Size

> 16G 30777344
>
> 32G 61071360
>
> 64G 122142720

## 手动验证 Finalize 和工厂流程

- Python

  ```bash
  gooftool finalize
  ```

- Json

  ```bash
  gooftool wipe_in_place --shopfloor_url xxxxxx
  ```

## Re-Build Factory Par

进 `~/trunk/src/platform/factory/` 修改需要的 `path`

在 `/trunk/src/platform/factory/` run `make par BOARD=kalista` 进行 `make`

## 快捷替换 Toolkit 中 `update_toolkit = True`

> `sync_factory_server.py`

```bash
root
test0000
sed -i '0,/default=False/s//default=True/' /usr/local/factory/py/test/pytests/sync_factory_server.py && reboot
```

## 读取 Storage Information （存储设备软件信息）

EMMC/SSD Firmware

```bash
# emmc
smartctl -x /dev/mmcblk0

# nvme
smartctl -x /dev/nvme0

# ssd
smartctl -x /dev/sda
```

### Module 过站异常，无法处理 Request

```bash
主机IP: 10.18.6.61
User name: F2-QCMC-WDS\bekins
Password: bekind
```

问题原因：DUT Send 异常字符串导致 Server 后台 Minitor 程式卡住

解决方案：**手动删除异常文本，例如： $=@$%@$@$.txt**

删除后程式会自动恢复运行
