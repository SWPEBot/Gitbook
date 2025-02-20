# Chrome OS HWID Database

## 介绍

The role of a hardware ID (HWID) configuration file is to verify that every
hardware component used in a Chrome device is qualified. The config file
mainly lists the phase of the device image_id, the pattern of the encoded
string pattern, the mapping of bit value to the component encoded_field,
and the probe result of each component component. For convenience, we call
the HWID config file HWID database.

**参考链接:**

**[updatingHWID](https://www.google.com/chromeos/partner/fe/docs/factory/updatingHWID.html)**

**[ChromeOS HWID Database User Guide](https://chromium.googlesource.com/chromiumos/platform/factory/+/HEAD/py/hwid/README.md)**

## 更新规则

1. 通用规则：仅增加项目，不修改或删除项目
2. 不要修改 `encoded_field` 的集合
3. Encoded_fields：不修改编码映射
4. 组件：使用已弃用 / 不支持删除组件 (**Components: Use deprecated/unsupported to Remove Component**)

## Database Builder

注意: 以下情况不适用于 `Database Builder`

1. 多个`image_ids`处于活动状态
2. 针对特殊规则的修改
3. 更改 component qualifications 状态

### 创建一个 issue for HWID 更改

登录[Issue tracker](https://partnerissuetracker.corp.google.com)并为 hwid database 更改 Create Issue.

### 请注意 Probe 方法更改

使用 HWID Builder 创建和更新 HWID Database

对于 Dome Umpire 结构, 使用: `hwid probe`, 旧项目使用`gooftool probe`，你可以运行命令检查它。

### Build the HWID database

> 仅供首次构建使用 (Proto phase 或 EVT stage, 后续阶段请使用更新 HWID database 方法)

```bash
hwid build-database \
    --project <project name> \
    --output-database-path <output folder> \
    --probed-results-file <probed result file> \
    [--image-id <IMAGE_ID>] \  # Name of the image_id, default is 'EVT'
    [--add-default-component COMP [COMP ...]] \  # Add the default item
    [--add-null-component COMP [COMP ...]] \  # Add the null item
    [--region REGION [REGION ...]] \  # Add supported regions
    [--chassis ID1 [ID2 ...]] \  # Add supported chassis
```

**示例:**

**获取信息 & 创建 HWID Database 更改.**

* 获取项目 Model Name

  ```bash
  mosys platform model #get model
  ```

* 获取硬件侦测信息

  ```bash
  # gooftool probe --include_vpd > /tmp/probe_sku1.log
  hwid probe > /tmp/probe_sku1.log # get the probe result
  ```

* 创建 HWID Bundle 目录

  ```bash
  mkdir -p /usr/local/factory/hwid
  ```

* 构建 HWID Database

  ```bash
  # hwid build-database --probed-results-file /tmp/probe.log -b BOARD -p /usr/local/factory/hwid --image-id EVT --no-verify-checksum
  hwid build-database –j MODEL --no-verify-checksum --probed-results-file /tmp/probe_result_sku1 –p /usr/local/factory/hwid/
  ```

* 验证 HWID database

  ```bash
  # hwid generate --probed-results-file /tmp/probe.log -b BOARD -p /usr/local/factory/hwid --no-verify-checksum
  hwid generate --probed-results-file /tmp/probe.log -j MODEL -p /usr/local/factory/hwid --no-verify-checksum
  ```

  _现在你有了新的 HWID 文件在`/usr/local/factory/hwid/MODEL`, 请确保在侦测之前已经写好了所有必需的 VPD（例如 region......）._

### 更新 HWID Database(Update the HWID database)

> 当 CPFE 已存在 HWID Bundle 或 Config 时，使用如下方法更新 HWID database

```bash
hwid update-database \
    --project <project name> \
    --output-database-path <output folder> \
    [--probed-results-file <probed result file>] \
    [--output-database <output file>] \  # Write into different file
    [--image-id <IMAGE_ID>] \  # Name of the image_id
    [--add-default-component COMP [COMP ...]] \  # Add the default item
    [--add-null-component COMP [COMP ...]] \  # Add the null item
    [--region REGION [REGION ...]] \  # Add supported regions
    [--chassis ID1 [ID2 ...]] \  # Add supported chassis
```

**示例:**

* 获取硬件侦测信息

  ```bash
  # gooftool probe --include_vpd > /tmp/probe_sku1.log
  hwid probe > /tmp/probe_sku1.log # get the probe result
  ```

* 创建 HWID Bundle 文件夹并放入配置文件

  * 使用现有的 hwid bundle 生成 HWID Config

    1. 从 CPFE 下载 hwid bundle，例如:
        `hwid_v3_bundle_MODEL_201x_0x_0x_0x_xx_xx_xxx.sh`
    2. 将 hwid bundle 复制到 DUT 并运行它
    3. 现在你的 HWID 配置文件在 `/usr/local/factory/hwid/MODEL`

  * 从其他地方复制

    ```bash
    mkdir -p /usr/local/factory/hwid/ # 首先，需要创建路径
    cp /media/MODEL /usr/local/factory/hwid/ # Model 代表项目名称，这是hwid配置文件
    ```

* 更新 HWID Database

  ```bash
  # hwid update-database --probed-results-file /tmp/probe.log -b BOARD -p /usr/local/factory/hwid --no-verify-checksum
  hwid update-database –j MODEL –p /usr/local/factory/hwid/ --no-verify-checksum --probed-results-file /tmp/probe_sku2.log
  ```

* 验证 HWID database

  ```bash
  # hwid generate --probed-results-file /tmp/probe.log -b BOARD -p /usr/local/factory/hwid --no-verify-checksum hwid generate --no-verify-checksum
  hwid generate --probed-results-file /tmp/probe.log -j MODEL -p /usr/local/factory/hwid --no-verify-checksum
  ```

  _现在你有了新的 HWID 文件`/usr/local/factory/hwid/MODEL`, 请确保原始 HWID Database 存在于`/usr/local/factory/hwid/MODEL`中._

* 检查 HWID Database

  ```bash
  # Need verify
  hwid verify --no-verify-checksum --probed-results-file /tmp/probe_all_sku --phase PVT 'BOARD XXXX-XXXX-XXXX-XXXX'
  ```

* 解析 HWID Database

  ```bash
  hwid decode -p /usr/local/factory/hwid/ --no-verify-checksum 'BOARD XX-XX-XX-XX-XX' #decode verify``
  ```

**注意事项:**

**Confirm the status of the component items!**

All component items generated by the database builder are unqualified. After the SIE or TAM confirms the component is qualified at AVL, please change the status to supported.

### 上传到 CPFE

复制 MODEL 文件，打开 [CPFE](https://www.google.com/chromeos/partner/fe/) 并上传 HWID 文件.

必须删除第一行 `"checksum:"` 以及`"board:"`. 点击 `"Submit changes"` 并获得 CL Number.

**注意: 请保存 CL Number!**

### 等待审核

提供 [CL Number] 给 Device PM 并等待批准.

获得批准后，可在[CPFE](https://www.google.com/chromeos/partner/fe/)下载更新的 HWID Bundle.

### 部署到 Dome Server

打开工厂 DOME Server Web Console ，上传 HWID Bundle，进行验证.

## 官方操作示例

[Use Cases](https://chromium.googlesource.com/chromiumos/platform/factory/+/HEAD/py/hwid/README.md#use-cases)

## 手动检查 HWID Database

```bash
hwid generate --no-verify-checksum
```
