# Chrome OS Mode Guide

[Firmware Menu Interface](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#Firmware-Menu-Interface)

## Recovery Mode

允许用户从 USB 或 SD 卡重新安装 Chrome OS，当系统出现故障时，默认会处于 Recovery Mode

**Enter Recovery Mode:**

* Chromebook
  按住`Esc + Refresh`并按`Power`
* Chromebox Recovery Mode
  Chromeboxes 有一个恢复按钮。要进入恢复模式，请按住恢复按钮并按`Power Button`
* Chrometablet Recovery Mode
  `Power + Volume-Up + Volume-Down`保持 10 秒钟，如果屏幕在此期间关闭或打开，请忽略它并确保在整个 10 秒钟内按住所
  有三个按钮，并等待进入 `Recovery Mode`.

## Developer Mode

用于启动而无需引导验证, 在切换期间会擦除状态分区, 在工厂中用于启动测试 Image, 允许从 USB 启动.

**Enter Developer Mode:**

* Chromebook
  首先进入 Recovery Mode，然后按`Ctrl + D`，然后按`Enter`
* Chromebox
  首先进入 Recovery Mode 并按`Ctrl + D`，然后按下 Chromebox 上的物理恢复按钮 (Reset Button)
* Cheometablet
  首先进入 Recovery Mode 并按`Ctrl + D`，然后在菜单中选择 “Confirm Enabling Developer Mode”

### 快捷键说明

* `Ctrl + D`：从内部磁盘引导系统。
* `Ctrl + U`：从外部 USB 或 SD 卡启动系统，该选项须在命令行中设置`crossystem dev_boot_usb=1`或
  `enable_dev_usb_boot`.
* `Ctrl + L`：Chain-load 加载包含的传统引导加载程序（例如 SeaBIOS），这可能允许更轻松地启动其他操作系统，并非所有设备都附带
  传统引导，特殊情况使用。

## Factory Mode

工厂测试模式，基于 `Test Image` 和 `Factory Toolkit`, 通过 `Goofy` 运行工厂测试.

### Engineer Mode(Base on Factory Mode)

**基于测试图像和工厂工具包，工厂工程模式按`Ctrl + Alt + 0`进入**

* RMA 工程模式密码默认是`cros`
* 工厂工程模式，默认密码: `ocarina86`
  某些专案使用其他密码:`chrome` or `chrometeam`
* 修改工程模式密码

  ```bash
  cd /usr/local/factory/py/test/test_lists/
  echo -n chrome | sha1sum >> newpassword.log
  ```

  **添加生成的 sha1sum 到 main_test_list.**

### 手动 wipe 测试系统, 切换至出货系统

```bash
# Clear Gbb flags.
# /usr/share/vboot/bin/set_gbb_flag.sh 0x0000
# Some times need do wipe init.
# gooftool wipe_init

gooftool wipe_in_place
# Waiting system restart and start wipe in progress.
```

## Shipping Mode

最终用户 (Shipping) 模式, 完成工厂流程并切断电池电源.

### **清除出货 image 下的用户数据**

**To do power wash:**
打开设备并进入登录界面, 按 `ctrl + alt + shift + r` 并选择 `reset` 重置设备，通常为 PQA/FQA 使用
