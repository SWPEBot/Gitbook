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
| AUDIO_CODEC	          | DWORD 0	      |  0..2	    |   001₂	          |    1	        |    ALC3343 (Audio Codec)       |
| AUDIO_AMPLIFIER	      | DWORD 0	      |  3..5	    |   001₂	          |    1	        |    ALC1320 (Speaker Amp)       |
| STORAGE_TYPE	        | DWORD 0	      |  12..14	  |   001₂	          |    1          |    VME (Storage Type)          |
| SENSOR_HUB	          | DWORD 0	      |  23..23	  |   1₂	            |    1	        |    PRESENT (ISH_PRESENT)       |
| FINGERPRINT_INTERFACE |	DWORD 0	      |  24..25	  |   00₂	            |    0	        |    ABSENT                      |
| WIFI_INTERFACE	      | DWORD 0	      |  26..27	  |   00₂	            |    0	        |    ABSENT                      |
| FORM_FACTOR	          | DWORD 0	      |  30..31	  |   10₂	            |    2	        |    CONVERTIBLE (Form Factor)   |
| KB_BACKLIGHT	        | DWORD 1	      |  11..11	  |   1₂	            |    1	        |    PRESENT (Keyboard Backlight)|
| KEYBOARD_LAYOUT	      | DWORD 1	      |  13..15	  |   000₂	          |    0	        |    AEZDQU01010 / SF03P_C33YWL  |
| AP_OEM_2BIT_FIELD0	  | DWORD 1	      |  27..28	  |   01₂	            |    1	        |    USB3C (IO_BOARD_USB3C)      |
| LID_SENSOR	          | DWORD 2	      |  13..15	  |   001₂	          |    1	        |    LIS2DH12 (Lid Accelerometer)|
| BASE_SENSOR	          | DWORD 2	      |  16..18	  |   001₂	          |    1	        |   LIS2DW12 (Base Accelerometer)|

## Kconfig Options

- CONFIG_CROS_EC_CBI_UFSC_PARSER Kconfig 选项用于启用解析设备树中 UFSC 字段并提供必要 API 的驱动程序。如果存在并启用了兼容 cros-ec,cbi-ufsc 的设备树节点，则此选项会自动启用。因此，您无需在项目配置文件中手动设置此 Kconfig 选项。

## Devicetree Nodes

UFSC 在设备树中采用基于模板的方法进行配置。项目覆盖层包含标准模式文件，并定义该设备上硬件的具体值。这些节点的模式定义在 cros-ec,cbi-ufsc.yaml 和 cros-ec,cbi-ufsc-value.yaml 文件中。

## Schema Templates

UFSC 位域的结构在两个模板文件中定义，项目覆盖层应包含这两个模板文件：
- zephyr/include/cros/cbi_ufsc_std_schema.dtsi：定义标准化字段。仅定义 EC 感兴趣的字段。
- zephyr/include/cros/cbi_ufsc_oem_schema.dtsi：定义用于 OEM 定制的通用可选字段。仅定义 EC 感兴趣的字段。

这些文件定义了每个配置项的字段名称、start位和值。size

示例片段来自cbi_ufsc_std_schema.dtsi：
```bash
#define UFSC_BIT(dword, bit) ((dword) * 32 + (bit))

/ {
    cbi_ufsc: cbi-ufsc {
        compatible = "cros-ec,cbi-ufsc";

        ufsc_thermal_fan: thermal-fan {
            enum-name = "UFSC_THERMAL_FAN";
            start = <UFSC_BIT(2, 2)>;
            size = <1>;
        };
        ufsc_base_sensor: base-sensor {
            enum-name = "UFSC_BASE_SENSOR";
            start = <UFSC_BIT(2, 6)>;
            size = <3>;
        };
        /* ... other standard fields ... */
    };
};
```
## Project Value Definition

项目通过在相应的字段节点内创建子节点来定义每个字段的可能值。这些值定义通常放置在项目特定的覆盖文件中（例如，generated_std_ufsc.dtsi 用于自动生成的值或oem_ufsc.dtsi自定义值）。

每个值节点都具有以下属性：
- compatible = "cros-ec,cbi-ufsc-value" : 必需的兼容字符串。
- status = "okay" : 此属性为必填项。
- value：将存储在位域中的整数值。
- default：一个布尔属性，表示这是默认值。
  
示例项目叠加层（project.overlay）：
```bash
/* Include the standard and OEM schema templates */
#include <cros/cbi_ufsc_std_schema.dtsi>
#include <cros/cbi_ufsc_oem_schema.dtsi>

/* Include the generated file that defines the values for this project */
#include "generated_std_ufsc.dtsi"

/* Optionally include and define OEM custom values */
#include "oem_ufsc.dtsi"
```
示例值定义（generated_std_ufsc.dtsi）：
```bash
/* This file is typically auto-generated */
&ufsc_thermal_fan {
	ufsc_fan_absent: absent {
		compatible = "cros-ec,cbi-ufsc-value";
		status = "okay";
		value = <0>;
		default;
	};

	ufsc_fan_present: present {
		compatible = "cros-ec,cbi-ufsc-value";
		status = "okay";
		value = <1>;
	};
};

&ufsc_base_sensor {
	ufsc_base_lsm6dso: lsm6dso {
		compatible = "cros-ec,cbi-ufsc-value";
		status = "okay";
		value = <0>;
	};
	ufsc_base_bmi160: bmi160 {
		compatible = "cros-ec,cbi-ufsc-value";
		status = "okay";
		value = <1>;
		default;
	};
};
```
## API Usage

固件通过通用驱动程序 API 与 UFSC 数据交互，抽象化了位级细节。
首选方法是使用特定值进行检查 cros_cbi_ufsc_check_match()。这种方法出错率较低，类似于传统的 SSFC API。
```bash
#include "cros_cbi.h"

if (cros_cbi_ufsc_check_match(
        CBI_UFSC_VALUE_ID(DT_NODELABEL(ufsc_fan_present)))) {
    /* Fan is present */
}
```
## Testing and Debugging

可以使用控制台命令和主机命令读取 UFSC数据cbi。ectool cbi
使用 UFSC 值设置时ectool，提供的参数必须是按字节顺序排列的十六进制字符串，采用小端序格式（最低有效字节在前）。

例如，如果所需的 UFSC 数据由以下 32 位字组成：
- DWORD[0]: 0x11223344
- DWORD[1]: 0x55667788
- DWORD[2]: 0x99aabbcc
- DWORD[3]: 0xddeeff00
将此信息写入 UFSC 标签的命令29是：
```bash
ectool cbi set 29 4433221188776655ccbbaa9900ffeedd
```
在chroot build ec cbi 请使用cbi-util
- 十六进制用--ufsc_hex
```bash
cbi-util create --file cbi.bin --board_version 1 --sku_id 1 --size 256 \
    --ufsc_hex 4433221188776655ccbbaa9900ffeedd
```
- 整数 Use the --ufsc argument with four comma-separated 32-bit hexadecimal values (DWORD 0 to DWORD 3).
```bash
cbi-util create --file cbi.bin --board_version 1 --sku_id 1 --size 256 \
    --ufsc 0x11223344,0x55667788,0x99aabbcc,0xddeeff00
```
