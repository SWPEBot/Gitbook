# Servo User Guide

## Servo Board (Servo V2) 用法示例

说明，下述所有步骤需在 Chroot Supper Console 环境中执行

示例，`cros_sdk --no-ns-pid`

### 安装 package

```bash
sudo emerge hdctools # 如有新机种，请重新run 此步骤获取其board xml config
# sudo USE=ftdi emerge openocd
sudo emerge minicom
sudo emerge flashrom
sudo emerge tmux tree
```

### 连接 Servo Board

```bash
sudo servod -b ${BOARD}
# 示例 sudo servod -b cheza
# 需要minitor 进程，请保持此进程运行

# 通用连接方式
sudo servod --config=servoflex_v2_r0_p50.xml
```

### 通过 Servo board 更新 BIOS

```bash
# 此步骤需在连接 Servo Board 后进行
# 注意: 部分机型（如平板）使用电压为 pp1800
dut-control spi2_vref:pp3300 spi2_buf_en:on spi2_buf_on_flex_en:on spi_hold:off cold_reset:on
sudo flashrom -p ft2232_spi:type=servo-v2 -w BiosFilePath
dut-control spi2_vref:off spi2_buf_en:off spi2_buf_on_flex_en:off cold_reset:off
# 请在更新完成后关闭电压控制
```

### 通过 Servo Board 更新 EC/PD

```bash
cd ~/trunk/src/platform/ec/util

./flash_ec --board ${BOARD} --image EcFilePath
# 示例:
# ./flash_ec --board cheza --image EcFilePath

./flash_ec --board ${BOARD}_pd --image PdFilePath
# 示例:
# ./flash_ec --board cheza_pd --image EcFilePath
```

### 查找 EC Serial Port

`dut-control ec_uart_pty`

### 查找 CPU/AP Serial Port

`dut-control cpu_uart_pty`

### 抓取 Debug Log

```bash
# 查找 bios/ec serial port
dut-control cpu_uart_pty
dut-control ec_uart_pty

# Generate Log
sudo minicom -C bug.txt -p /dev/pts/7  # 7 为对应 Serial Port

sudo minicom -D /dev/pts/23 -C debug.log

# 以上方式二选一
```

### Debug Port 80

```bash
cros_sdk --no-ns-pid
sudo servod -b BOARD
minicom -D /dev/pts/18

chan 00

port 80
```

### 使用 Servo Board 设定 Firmware Gbb Flag

```bash
# 连接 Servo board
sudo servod --config=servoflex_v2_r0_p50.xml

# 设定环境
dut-control spi2_buf_en:on spi2_buf_on_flex_en:on spi2_vref:pp3300 cold_reset:on

# 从 DUT 读取当前BIOS bin file
sudo flashrom -V -p ft2232_spi:type=servo-v2 -r image.bak

# 设定 gbb flag
gbb_utility -s --flags=0x1039 image.bak

# 将更改gbb flag 后的 Firmware 烧入 DUT
sudo flashrom -V -p ft2232_spi:type=servo-v2 -w image.bak

# 关闭 spi2 control
dut-control spi2_buf_en:off spi2_buf_on_flex_en:off spi2_vref:off cold_reset:off
```

### For new project or public project

1. When run `sudo  servod -b board_name` show `no board xml config`:

   ```bash
   repo sync
   # check '~/trunk/src/third_party/hdcptools/servo/data'
   # need have 'servo_board_name_overlay.xml found, if not.
   sudo emerge hdcptools # 安装hdcptools, 每有新案子增加，就必需要做一次.
   ```

2. When run `./flash_ec --board trogdor --image ~/ec.bin` show `Failed to find npcx_monitor.bin`:

   ```bash
   cd ~/trunk/src/platform/ec
   # make buildall
   # make buildall build all project

   make --no-print-directory BOARD=project
   # make build for one project.
   ```

## Servo Board (Servo micro) 用法实例

### 连接 Servo board(Servo micro)

```bash
sudo servod -b ${BOARD} # in chroot 'cros_sdk --no-ns-pid'
# 示例 sudo servod -b hatch
# 需要minitor 进程，请保持此进程运行
```

### 通过 Servo board(Servo micro) 更新 BIOS 或 EC BIN

```bash
cros_sdk --no-ns-pid # Enter chroot
dut-control spi2_vref:pp3300 spi2_buf_en:on
# sudo flashrom --programmer raiden_debug_spi -r bios.bin/ec.bin  --> read
sudo flashrom --programmer raiden_debug_spi -w bios.bin/ec.bin
dut-control spi2_vref:off spi2_buf_en:off
# google官方文档
# https://chromium.googlesource.com/chromiumos/third_party/hdctools/+/refs/heads/master/docs/servo_micro.md
```

### 通过 Servo board(Servo micro) 抓取 BIOS 或 EC log

```bash
cros_sdk --no-ns-pid # Enter chroot
# 获取BIOS port
dut-control cpu_uart_pty
# 获取EC port
dut-control ec_uart_pty
# 获取cr50 port
dut-control cr50_uart_pty
# Capture Log
sudo minicom -D /dev/pts/$PORT -C bios.log/ec/cr50.log
# google官方文档
# https://chromium.googlesource.com/chromiumos/third_party/hdctools/+/refs/heads/master/docs/servo.md
```
