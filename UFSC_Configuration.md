# Zephyr CBI UFSC 配置

[参考链接](https://source.chromium.org/chromiumos/chromiumos/codesearch/+/main:src/platform/ec/docs/zephyr/zephyr_ufsc.md)

## Overview

较新的设备使用名为 UFSC（统一固件和第二源配置）的统一系统。该系统用存储在 CBI 中的单个、基于模式的 128 位（4 个双字）值取代了原有的 FW_CONFIG 和 SSFC 字段。

UFSC 的主要目标是：
- 将静态（不可探测）配置和动态（可探测）配置统一到一个单一的真实来源中。
- 强制执行标准化方案，以减少错误并消除特定于电路板的解码逻辑。
- 提供一种清晰且易于维护的硬件变更管理方法。

128 位 UFSC 字段的结构如下：
- BITS 0-55、64-119（标准化固件配置）：包含常用硬件组件（例如，音频编解码器、传感器）的标准化定义。
- BITS 56-63（AP OEM 定制字段）：保留给 OEM/ODM 合作伙伴，用于编码 AP 使用的板级特定信息。
- BITS 120-127（EC OEM 定制字段）：保留给 OEM/ODM 合作伙伴，用于编码 EC 使用的板级特定信息。


| Field Name	          | Target DWORD	| Bit Range |	Extracted Binary	| Integer Value | Matched Component / Probe      |
|-----------------------|---------------|-----------|-------------------|---------------|--------------------------------|
| AUDIO_CODEC	          | DWORD 0	      |  0..2	    |   001₂	              1	          |    ALC3343 (Audio Codec)       |
| AUDIO_AMPLIFIER	      | DWORD 0	      |  3..5	    |   001₂	              1	          |    ALC1320 (Speaker Amp)       |
| STORAGE_TYPE	        | DWORD 0	      |  12..14	  |   001₂	              1           |    VME (Storage Type)          |
| SENSOR_HUB	          | DWORD 0	      |  23..23	  |   1₂	                1	          |    PRESENT (ISH_PRESENT)       |
| FINGERPRINT_INTERFACE |	DWORD 0	      |  24..25	  |   00₂	                0	          |    ABSENT                      |
| WIFI_INTERFACE	      | DWORD 0	      |  26..27	  |   00₂	                0	          |    ABSENT                      |
| FORM_FACTOR	          | DWORD 0	      |  30..31	  |   10₂	                2	          |    CONVERTIBLE (Form Factor)   |
| KB_BACKLIGHT	        | DWORD 1	      |  11..11	  |   1₂	                1	          |    PRESENT (Keyboard Backlight)|
| KEYBOARD_LAYOUT	      | DWORD 1	      |  13..15	  |   000₂	              0	          |    AEZDQU01010 / SF03P_C33YWL  |
| AP_OEM_2BIT_FIELD0	  | DWORD 1	      |  27..28	  |   01₂	                1	          |    USB3C (IO_BOARD_USB3C)      |
| LID_SENSOR	          | DWORD 2	      |  13..15	  |   001₂	              1	          |    LIS2DH12 (Lid Accelerometer)|
| BASE_SENSOR	          | DWORD 2	      |  16..18	  |   001₂	              1	          |   LIS2DW12 (Base Accelerometer)|
