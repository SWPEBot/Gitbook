# Hardware Descriptor DVT 工厂流程用户指南（中文译本）

> **原文标题**：[EXTERNAL] Hardware Descriptor DVT Factory Process User Guide  
> **状态**：Ready for publish  
> **作者**：Clark Chung, Yong Hong, lt-hw-identification@google.com  
> **最后更新**：2026-03-10  
> **页数**：37  
> **说明**：本文档为外部共享（SHARED EXTERNALLY），可与 ODM 等外部伙伴共享。

---

## 译本说明

- **专业术语**：保留 Google / Aluminium 官方英文名，并附国内产线常用中文解释。  
- **代码 / 字段 / Action 名**：保持英文原样（如 `HwDescProbeProvision`、`google.mlb_pn`）。  
- **适用范围**：仅适用于 **Aluminium 第一波机型的 DVT 工厂 build**；后续工厂版本可能有调整。

---

## 目标读者

- 负责 Aluminium 设备的 **ODM 伙伴**
- 与相关 ODM 紧密协作的 **Google 相关方（stakeholders）**

---

## 一、背景（Background）

在制造一台 Aluminium 设备（unit）时，有一项**必须写入（provision）并校验（verify）**的关键值，叫做：

**Hardware Descriptor（硬件描述符）**

简单理解：它是写入 **VPD** 里的一段「本机实际装了哪些关键料」的编码信息。  
要在工厂端正确 **生成 → 写入 → 校验** 这台 **DUT** 的 Hardware Descriptor，必须按本文规定的工厂流程执行。

---

## 二、目标（Objective）

本文希望帮助读者：

1. 了解 Factory App 如何 **provision** Hardware Descriptor。  
2. 学会搭建环境，以支持 **Hardware Descriptor 写入**。  
3. 学会搭建环境，以支持 **已写入 Hardware Descriptor 的校验**。  
4. 在 Factory App 写入/校验失败时，能按错误做 **triage（问题分诊）**。  
5. 针对每类失败，知道后续该怎么跟进、怎么修。

---

## 三、不在本文范围内（Out-of-scope）

本文只讲 **工厂端** 的 Hardware Descriptor 流程，**不覆盖**：

1. ODM / 相关方如何向 Google 提交 manufacturing data（制造数据）与 key component（关键件）信息，作为 mapping table 的数据源。  
2. ODM 工厂如何从 Google 获取该机型的 Hardware Descriptor mapping table 文件。  
3. ODM / 相关方如何申请更新 mapping table。

---

## 四、前置条件（Prerequisite）

1. 熟悉 **Factory App Development Workflow**（Factory App 开发工作流）。  
2. 确认 git 仓库 `vendor/google_devices/release/desktop` 至少包含 commit **`c0eb629`**，或 flag 版本不低于：  
   - `RELEASE_PACKAGE_HWID` ≥ **HardwareDescriptorManager_1.0.878960366**  
   - `RELEASE_PACKAGE_FACTORY` ≥ **Factory_1.0.880713891**  
3. **Googlers**：也可使用 Google 内部 Android checkout；后文 `$ARSP_ROOT` 请替换为你的 googleplex-android 路径。

---

## 五、术语表（Terminology）

### 5.1 设备工作流相关

| 英文术语 | 中文理解 | 说明 |
|---------|---------|------|
| **Unit** | 单机 / 单台整机 | 一台具体的笔记本/平板实例。**per-unit** = 仅针对这一台（如 SN）。 |
| **DUT** | Device Under Test（待测机 / 在制机） | 当前正在制造或测试的那台 unit。 |
| **Product** | 产品（上市营销产品） | 以对外 **marketing name** 标识。**per-product** = 同一产品共用。 |
| **Device** | 设备项目 / 机型项目 | 类似 ChromeOS 的 **DLM device**。一个 Device 下可有多个 Product；一个 Product 下有多台 Unit。**per-device** = 整个项目共用。Aluminium 中 **OS image 是 per-device**（与 ChromeOS 多个 device 可能共用同一 OS image 不同）。 |

层级关系可记为：

```
Device（机型项目）
  └── Product（上市产品）
        └── Unit / DUT（单台机器）
```

### 5.2 制造数据相关

| 英文术语 | 中文理解 | 说明 |
|---------|---------|------|
| **MFG SKU** | 制造 SKU | ODM 定义的制造规格档。 |
| **MFG SKU ID** | 制造 SKU 编号 | ODM 为该 MFG SKU 定义的 ID。 |
| **MLB** | Main Logic Board（主板） | 装有 **SoC** 的 PCBA。 |
| **Daughter board (DB)** | 子板 / 副板 | 不含 SoC 的 PCBA。 |
| **Key components** | 关键件 | ODM 提供给 Google 的制造数据里列出的硬件件。 |
| **Component type** | 部件类型 | 规范类型，如 `"eDP Panel"`、`"Battery pack"`。一个 key component 可对应一个或多个 component type（例如同时是 eDP Panel + Touchscreen）。 |
| **Part number (PN)** | 料号 | ODM 为关键件或 PCBA 分配的料号。 |

### 5.3 硬件识别相关

| 英文术语 | 中文理解 | 说明 |
|---------|---------|------|
| **Hardware-probeable component type** | 可硬件探测的部件类型 | 该类型绑定了预定义属性，且：① 属性可作为部件身份标识；② 属性可从 OS 读出。读到的实际值叫 **probed attributes（探测属性）**。例：eDP Panel 用 EDID Manufacturer ID + Product code；USB Camera 用 `lsusb` 读 VID/PID。 |
| **Hardware-identifiable key component** | 可硬件识别的关键件 | 通过「探测属性 vs 期望属性」比对，能判断 DUT 上是否存在该件。匹配成功的称为 **probed key components（已探测到的关键件）**。 |

**例子**：UFS 存储 `KLUDG4UHGC-B0E1` 可读出 Manufacturer Name / Product Name，与期望值（如 SAMSUNG + 型号）比对，即可识别是否装了该料。

### 5.4 Hardware Descriptor 相关

| 英文术语 | 中文理解 | 说明 |
|---------|---------|------|
| **Hardware descriptor mapping table**（本文简称 **mapping table**） | 硬件描述符映射表 | Google 提供的 **per-device** `.txtpb` 文件，描述制造数据、产品信息、SKU、关键件等。 |
| **Manufacturing database（制造数据库）** | 映射表中的制造库 | ① 按 MLB/DB 的 PN，列出该 PCBA 上的关键件（含 second-source 二供）；② 按 MFG SKU ID，列出该 SKU 下的关键件、MLB、DB。另含可识别关键件的 **expected component attributes（期望探测属性）**。 |
| **Hardware descriptor mapping table patch** | 映射表补丁 | 用于 **覆盖** 映射表中关键件探测属性的 per-device `.txtpb`。 |
| **DUT's manufacturing information** | DUT 制造信息 | 本质上是该机上 PCBA 与关键件的 **PN 列表**。 |
| **Hardware descriptor** | 硬件描述符 | Aluminium 特有、写入 **VPD** 的值，编码了上述 DUT 制造信息。 |

---

## 六、端到端工厂流程（End-to-end factory process）

### 6.1 流程开始前

先按 **SDT / PSE / SIE** 既定流程，向 Google 侧拿到该机型的 mapping table（`.txtpb` 文件）。

### 6.2 准备：Action List 编排

测试列表（action list）中必须包含：

- **`HwDescProbeProvision`**：探测 + 生成并写入 Hardware Descriptor  
- **`HwDescVerify`**：校验已写入的 Hardware Descriptor  

**硬性要求：**

| 要求 | 说明 |
|------|------|
| 顺序 1 | 两者都必须在 **`ProvisionVPD`** 之后，确保 VPD 里已有 `mfg_sku_id`、`product` |
| 顺序 2 | **`HwDescVerify` 必须在 `HwDescProbeProvision` 之后** |
| Probe 前 device data | 运行 `HwDescProbeProvision` 时，下列字段必须已填好 |
| Verify 前 device data | 运行 `HwDescVerify` 时，`google.phase` 必须已填好 |

**`HwDescProbeProvision` 所需 device data：**

| 字段 | 含义 | 格式 |
|------|------|------|
| `google.phase` | 当前 build phase | 如 `"DVT"` |
| `google.mlb_pn` | 本机 MLB 料号 | 字符串 |
| `google.db_pns` | 本机子板料号列表；无子板则为空列表 | **字符串**，内容为 **JSON 字符串数组**。例：`["DB_LEFT_PN1", "DB_RIGHT_PN2"]` |

**`HwDescVerify` 所需 device data：**

| 字段 | 含义 |
|------|------|
| `google.phase` | 当前 build phase（如 `"DVT"`） |

> **试产（trial run）提示**：不必跑完整产线流。可按后文 **Development tips** 单独试跑这两个 action（手动写 VPD、手动填 device data 等）。

### 6.3 准备：Side-load mapping table（侧载映射表）

#### 推荐方式：通过 DOME 从 Factory Drive 下载

首选做法：在 action list 里加一步 **下载最新 mapping table**，这样以后改表 **不必每次重编 Factory APK**。

**步骤 1：把 `.txtpb` 编译成 `.binpb`**

```bash
$ protoc \
      -I "$ARSP_ROOT/vendor/google_shared/packages/desktop/Factory/base/lib/src/main/proto/" \
      hardware_descriptor_mapping_table.proto \
      --encode device.google.desktop.hardware_descriptor.HardwareDescriptorMappingTable \
      < up-to-date-mapping-table.txtpb > up-to-date-mapping-table.binpb
```

编译失败见附录故障排查。

**步骤 2：** 通过 **DOME** 把 `up-to-date-mapping-table.binpb` 上传到 **factory drive**，记下目录名与文件名。

**步骤 3：** 在 action list 增加 **`DownloadFactoryDrive`**，从 factory drive 下到 DUT 本地：

目标路径示例：

`/data/user_de/10/com.google.android.factory.factory/files/factory_drive/mapping_table.binpb`

```textproto
subtests {
  label: "Download mapping table"
  action_name: "DownloadFactoryDrive"
  args {
    fields {
      key: "paths"
      value {
         list_value {
           values {
             list_value {
               values {
                 # factory drive 上的路径
                 string_value: "/mapping_table/up-to-date-mapping-table.binpb"
               }
               values {
                 # DUT 本地路径
                 string_value: "/data/user_de/10/com.google.android.factory.factory/files/factory_drive/mapping_table.binpb"
               }
             }
           }
         }
      }
    }
    fields {
      key: "serverUrl"
      value {
         string_value: "192.168.11.6:8089"  # 按实际修改
      }
    }
    fields {
      key: "timeoutSecs"
      value {
         number_value: 30
      }
    }
  }
}
```

**步骤 4：** 将 `HwDescProbeProvision` / `HwDescVerify` 的 `mappingTableSource` 设为：

`appfile://factory_drive/mapping_table.binpb`

（规则：把绝对路径前缀  
`/data/user_de/10/com.google.android.factory.factory/files/`  
替换成 `appfile://`）

```textproto
subtests {
  label: "HW descriptor Probe/Provision"
  action_name: "HwDescProbeProvision"
  args {
    fields {
      key: "mappingTableSource"
      value { string_value: "appfile://factory_drive/mapping_table.binpb" }
    }
    fields {
      key: "mappingTablePatchSource"
      value { string_value: "" }
    }
  }
}
subtests {
  label: "HW descriptor Verify"
  action_name: "HwDescVerify"
  args {
    fields {
      key: "mappingTableSource"
      value { string_value: "appfile://factory_drive/mapping_table.binpb" }
    }
    fields {
      key: "mappingTablePatchSource"
      value { string_value: "" }
    }
  }
}
```

**步骤 5：** 编译 Factory App 并跑工厂流程。此后 action 会使用指定的 mapping table。

#### 备选方式：打进 APK asset

若 factory drive 方案不可用，可把 mapping table 打进 APK，见 **附录**。

---

### 6.4 制造：运行 `HwDescProbeProvision`（写入 Hardware Descriptor）

- **全自动**，操作员无需交互。  
- 会从 mapping table、device data、DUT 硬件自动收集信息，生成并 **provision 到 VPD**。  
- **成功**：自动结束。  
- **失败**：屏幕显示错误并 **暂停**，方便试产排查。  
- **量产（main builds）时，ODM 严禁点 “Mark Passed” 跳过此必测项。**

更细的内部逻辑见附录 **Overview of how HwDescProbeProvision works**。建议所有读者先理解该 action 的原理。

---

### 6.5 制造：运行 `HwDescVerify`（校验已写入的 Hardware Descriptor）

- 同样 **全自动**。  
- 综合 mapping table、device data、DUT 硬件，校验 VPD 中已写入的 Hardware Descriptor。  
- 失败时暂停等待工程师排查。  
- **量产时严禁 “Mark Passed” 跳过。**

---

## 七、错误分诊与后续处理（Error triage and follow-up）

两个 action 都成功 → 整条 Hardware Descriptor 工厂流程完成。  
任一失败 → 因是 **required action（必测）**，必须跟进修复。  
**ODM 是第一责任方**，根据错误信息做 triage。

### 失败类型 1：前置条件失败（Precondition failures）

多为本地 **环境/配置** 问题。

| 示例错误信息 | 可能原因与检查点 |
|-------------|------------------|
| `… invalid argument …` | test list 参数语法错误 → 对照 **argument spec** |
| `Failed to prepare mapping table.` | 指定的 mapping table / patch 不存在或内容非法 → 检查参数路径与是否正确放到 DUT |
| `One of MLB_PN, or PHASE not found in DeviceData` | 必填 device data 未填或格式错 → 检查前置字段 |

### 失败类型 2：无法确定 DUT 制造信息

| 示例错误信息 | 可能原因与检查点 |
|-------------|------------------|
| `Not enough part numbers.` | 无法从输入推出完整制造信息 → 按下列 Step 1–4 排查 |
| `MFG SKU ${mfgSkuId} not found.` | mapping table 无此 MFG SKU ID |
| `MLB PCBA ${mlbPn} is not in the MFG SKU …` / `MLB PCBA … not found.` | MLB PN 不在该 SKU / 制造库中 |
| `DB PCBA count … does not match …` / `DB PCBA … not found.` | 子板 PN 组合与 MFG SKU 定义不符 |

#### Step 1：检查 DUT 硬件是否按 BOM / 制造数据组装

核对整机、PCBA 及板上料的 PN 是否符合 ODM **BOM / manufacturing data**。  
若装错料 → **改硬件** 后重跑 `HwDescProbeProvision`。

**例子：**

| MFG SKU | CPU | 面板 |
|---------|-----|------|
| A | i7 | UHD1 或 UHD2 |
| B | i5 | FHD1 或 FHD2 |

若实机是 **CPU i7 + 面板 FHD1** → 组合不在 BOM 内，属 **错装**。

#### Step 2：检查 device data 是否与实机一致

`HwDescProbeProvision` 会用 device data 做 **unique key component list resolution（唯一关键件列表解析）**。  
若 `google.mfg_sku_id`、`google.mlb_pn` 等与实机不符，会失败。  
请核对必填/可选 device data（可用后文 Device Data Editor 查看）。

#### Step 3：检查 mapping table 中的 manufacturing database 是否最新

用工具手动选择本机各组件/PCBA 的 PN。若某些 PN **选不进去**，说明该硬件组合 **不在 mapping table 支持范围**。  
例如 MFG SKU `"A"` 下只能选面板 `"B"`/`"C"`，但产线要用 `"D"` → **必须报 Google 更新制造数据与 mapping table**。  
拿到新表后，按 side-load 流程重新加载。

> ⚠️ **禁止 ODM 自行修改 mapping table 正文。**

#### Step 4：修正输入源，或手动生成 Hardware Descriptor

硬件装对、组合也在库内时，问题变成：

> 如何让 `HwDescProbeProvision` 知道完整制造信息，以便继续编码？

两条路：

1. **修环境**：保证跑 action 时 device data 已正确填好，让唯一关键件列表解析成功。  
2. **手动生成并写入**：  
   1. 点 **“Open manual generation tool”**  
   2. 手动选择/确认各关键件与 PCBA 的 PN  
   3. 点 **“Provision”**  
   4. 成功后回到 Factory App，该 `HwDescProbeProvision` 记为成功

### 失败类型 3：PVT/MP 批准不满足，或部件未识别

| 示例错误信息 | 可能原因与检查点 |
|-------------|------------------|
| `Unsatisfied FW version, required: AAAA,BBBB` | 探测到的固件版本不在 `fw_ver` 列表 → 确认 FW 正确，或见 **Skip PVT/MP approval check** |
| `Missing pairing component(s): [AAAA]` | ApprovalRule 要求配对件存在但未识别 → 确认配对件，或调整关键件列表解析 |
| `Not identified` | 解码出的部件与 mapping table 探测值对不上 → 打 patch 修正探测值，或整类标为 non-identifiable |
| `No PVT/MP approved rules satisfied.` | 无 `SUPPORTED` 状态的 ApprovalRule → 可临时 skip PVT/MP check |

### 失败类型 4：其他

联系 **Google PoC** 进一步排查。

---

## 八、调整工厂 Action 行为（Tuning）

### 8.1 跳过某些 component type 的探测

若探测某类型导致：

- **数据无效/不准**（仅 Probe）：例如误把所有二供 eDP 都识别成在位  
- **不稳定**：probe 崩溃或挂死  

→ **必须向 Google 报障**。  

在 PVT 前为不阻塞产线，可在 device data 设置：

| 项 | 内容 |
|----|------|
| 字段名 | `google.hw_descriptor.component_types_to_skip` |
| 格式 | JSON 字符串数组；每项为 `ComponentType` 枚举名，**去掉** `COMPONENT_TYPE_` 前缀 |
| 示例 | `["NVME", "EDP_PANEL"]` → 不探测 NVMe 与 eDP 面板 |

### 8.2 指定额外关键件 PN（无法 probe 识别的二供）

在指定 MFG SKU + MLB + DB 下，若仍有二供无法靠硬件探测识别，必须用 device data 写明期望 PN：

| 项 | 内容 |
|----|------|
| 字段名 | `google.hw_descriptor.additional_component_pns` |
| 格式 | JSON 字符串数组；每项为关键件 PN；同件多颗则重复填写 |
| 示例 | `["ABS0256G123", "31ZCQMB0456", "31ZCQMB0456"]` |

### 8.3 修正关键件的期望探测属性（mapping table patch）

当 **实机探测值 ≠ mapping table 期望值** 时，在同时满足下列条件时，ODM 可提供 **component value patch**：

1. 当前 **不是 PVT/MP** phase  
2. 官方自动生成 mapping table 流程尚未就绪，无法在可接受 TAT 内拿到 Google 正式修正包  

**打补丁步骤概要：**

1. 从 mapping table 的 `mlbs` / `dbs` / `skus.assembly_components` 找到要匹配的 **component_id**。  
2. 在 `components { ... }` 中定位对应消息。  
3. 复制需要 patch 的 `components` 消息，拼成单独文件。  
4. 修改 `*_attr` 为 **实机探测值**（可用 debug 页查看）。注意：改 `pvt_mp_approvals` **不会** patch 进映射表。  
5. 编译 patch：

```bash
$ protoc \
    -I "$ARSP_ROOT/vendor/google_shared/packages/desktop/Factory/base/lib/src/main/proto/" \
    hwdescriptor.proto \
    --encode com.google.android.factory.base.proto.hwdescriptor.ComponentList \
    < mapping-table-patch.txtpb > mapping-table-patch.binpb
```

6. 经 DOME 上传到 factory drive。  
7. 在 `DownloadFactoryDrive` 中增加下载 patch 的路径对。  
8. 将 `mappingTablePatchSource` 设为  
   `appfile://factory_drive/mapping_table_patch.binpb`。  
9. 编译并跑流程。

若 factory drive 不可用，见附录「把 patch 打进 APK asset」。

> ⚠️ **打 patch 只是临时放行手段。ODM 必须把修正后的 component attributes 回报 Google。**

### 8.4 将某 component type 标为 “non-identifiable”（暂时不可识别）

若某关键件的某 component type **理应可 probe，但 PVT/MP 前暂时不可 probe**，可通过 **清空探测属性** 使其变为 non-identifiable，以放行产线。  
（在 probe 工具 **未崩溃** 的前提下，应优先用本方法，而不是 skip 整类探测。）

1. 按 8.3 的步骤 1–3 准备 patch。  
2. 在 `type_info` 里 **清空** type-specific info（如 `touchscreen_module_info`）。  
   - 若同一 key component 有多个 type，仅部分暂时不可 probe：对其它 `ComponentTypeInfo` 置空，表示 patch 跳过这些 type。  

**注意：`component_info` 消息不得重排或删除，否则 patch 失败。**

3. 继续 8.3 的步骤 5–9。

---

## 九、开发技巧（Development tips）

### 9.1 试跑 `HwDescProbeProvision`：手动写 VPD

该 action 依赖 VPD 中已有 **`mfg_sku_id`** 与 **`product`**（故必须在 `ProvisionVPD` 之后）。试产可手动：

```bash
adb -s <SERIAL_OF_DUT> root

adb -s <SERIAL_OF_DUT> shell \
    vpd -i RO_VPD -s mfg_sku_id="<MFG_SKU_ID_YOU_WANT_TO_TRY>"

adb -s <SERIAL_OF_DUT> shell \
    vpd -i RO_VPD -s product="<PRODUCT_GOOGLE_CODE_NAME_YOU_WANT_TO_TRY>"
```

### 9.2 试跑：手动填 device data

正式流应由 shopfloor 等在跑 Probe 前写入 device data。试产可手动设置。可选值在 mapping table 中查：

| device data | 在 mapping table 中对应 |
|-------------|------------------------|
| `google.phase` | `products.phases` 的 **key** |
| `google.mlb_pn` | `MfgSku.mlbs.options` |
| `google.db_pns` | `MfgSku.dbs.options` |

示例结构：

```textproto
products {
  device_name: "device-name"
  google_code_name: "google-code-name"
  marketing_name: "Intel Chromebook"
  board_id: "ABCD"
  phases {
    key: "DVT"   // → google.phase
    value: 20
  }
  // EVT / PROTO / PVT ...
  sku_names: "SKU-NAME1"
}

skus {
  key: "SKU-NAME1"
  value {
    oem_sku_id: "OEM-SKU-ID1"
    mlbs {
       options: "MLB-PN1"  // → google.mlb_pn
       options: "MLB-PN2"
    }
    dbs {
       count: 2
       options: "DB-PN1"   // → google.db_pns
       options: "DB-PN2"
    }
  }
}
```

### 9.3 用 Device Data Editor 手动改

试产或临时修数时，可在 Factory App 打开 **Device Data Editor**（界面菜单步骤见原文档截图），运行时修改 device data。

### 9.4 跳过 PVT/MP approval 检查

PVT/MP 批准尚未就绪时，可对 **`HwDescVerify`** 设置：

`skipPvtMpApprovalCheck = true`

**前提**：现有 PVT/MP 失败项必须先有跟踪记录。

效果：失败类型 3 中多数会标 **`(Skipped)`**。  
但 **`Not identified` 仍算失败**，需用第八章 tuning 处理。  
若所有已解码部件都 **identified**，且 PVT/MP 失败均被 skip → Verify 记为通过。

### 9.5 查看 manufacturing database 中的关键件

1. 主界面左上角菜单  
2. **Open Hardware Descriptor Debug page**  
3. 进入 **MFG database viewer**  
4. 选 MFG SKU ID / MLB PN / DB PN 加载关键件  
5. 点 **Probe and match**  
6. 表格每行一个关键件：是否 hardware-identifiable；可点开看期望属性 vs 实际探测属性  

### 9.6 解码 Hardware Descriptor 并看校验结果

1. 同上进入 Debug 页  
2. **Hw descriptor decoder**  
3. 按物理位置（MLB / DB / assembly）分组显示已编码关键件  
4. 点 **Validate Hardware Descriptor**  
   - 列 **Approved for PVT/MP**  
   - 批准条件：**(A)** non-identifiable **或** 已 identified；且 **(B)** 通过 AVL，`grant PVT/MP usage approval = yes`  
   - 可同时看实际 probe 结果  

---

## 十、文档修订历史（Document history）

| 日期 | 变更 |
|------|------|
| 2026-03-10 | 增加从 factory drive 下载 mapping table 的方式 |
| 2026-02-11 | 更新为 DVT 工厂流程 |
| 2025-11-26 | 修复失效或外部链接 |
| 2025-11-18 | 准备发布 |
| 2025-11-07 | 修正 side-load mapping table 时 APK 编译错误的 workaround |
| 2025-10-30 | 初版 |

---

## 附录 A：把 mapping table 打进 Factory App asset

Factory drive 不可用时：

1. 将 mapping table 复制到：  
   `$ARSP_ROOT/vendor/google_shared/packages/desktop/Factory/factory/app/src/main/hardware_identification/`  
   文件名：**`mapping_table.txtpb`**  
2. action 参数 `mappingTableSource` 设为 **`"asset"`**  
3. 在 factory 目录执行：`./gradlew assembleDebug`  
   - 构建会打进 asset：`hardware_identification/mapping_table.binpb`  
4. （可选）校验 APK：

```bash
unzip -l app/build/outputs/apk/debug/app-debug.apk | grep assets
# 应看到 assets/hardware_identification/mapping_table.binpb
```

5. 在 DUT 上使用新编的 Factory App  

---

## 附录 B：side-load / 编译 mapping table 时 protoc 报错的处理

常见现象：放了 `.txtpb` 后 `./gradlew assembleDebug` 失败，例如：

```
> Task :app:generateHardwareIdentificationMappingTableAssetsFromTxtpb FAILED
/usr/bin/protoc
input:73:5: Unknown enumeration value of "COMPONENT_TYPE_{NEW_COMPONENT}" for field "component_types".
Failed to parse input.
```

原因：Factory base 里的  
`hardware_descriptor_mapping_table.proto`  
因 Google 内部同步机制 **过旧**。

**临时修复：本地 uprev proto**

```bash
cp "$ARSP_ROOT/device/google/desktop/common/hardware_descriptor/hardware_descriptor_mapping_table.proto" \
   "$ARSP_ROOT/vendor/google_shared/packages/desktop/Factory/base/lib/src/main/proto/hardware_descriptor_mapping_table.proto"
```

并在该 proto 文件中 **增加两行**：

```protobuf
syntax = "proto3";

option java_package = "com.google.android.factory.base.proto.hwdescriptor";
option java_multiple_files = true;

// Hardware descriptor schema version 00.
...
```

仍失败 → 联系 **Google PoC**。

---

## 附录 C：把 mapping table **patch** 打进 APK asset

1. 将 patch 放到  
   `.../factory/app/src/main/hardware_identification/mapping_table_patch.txtpb`  
2. `mappingTablePatchSource` 设为 **`"asset"`**（`mappingTableSource` 同理可按需为 asset）  
3. 按附录 A 构建与校验  

---

## 附录 D：`HwDescProbeProvision` 工作原理（强烈建议阅读）

整体五步：

```
(1) Probe DUT          → 收集 probed attributes
(2) 与 mapping table 期望属性比对 → 得到 probed key components
(3) Unique key component list resolution
        （交叉比对：制造库 + 已探测关键件 + device data 中的 DUT 信息）
        → 得到 DUT manufacturing information
(4) 编码成 Hardware Descriptor 字符串
(5) Provision 到 VPD
```

**第 (3) 步分两阶段：**

**Stage 1**  
用 device data 的 **MFG SKU ID + MLB PN + DB PNs** 查 manufacturing database，得到本机可用关键件 PN 列表。

**Stage 2** 解析策略：

| 策略 | 行为 | 例子 |
|------|------|------|
| **Single-source（单供）** | 无论是否 probe 到，都认定装了该件 | Battery Pack、EC、Audio Codec |
| **Multi-source（多供/二供）** | 仅当 **唯一** 一个候选被 probe 到，或写在 `google.hw_descriptor.additional_component_pns` 时，才认定装了该件 | eDP Panel、Storage、Accelerometer |
| 以上都失败 | **无法生成制造信息 → ProbeProvision 失败** | — |

---

## 附录 E：Action 参数规格（Argument spec）

| 字段值 | `mappingTableSource` | `mappingTablePatchSource` |
|--------|----------------------|---------------------------|
| `""`（空） | 从 **Factory Image** 取 | 不应用 patch |
| `"asset"` | 默认 asset：`hardware_identification/mapping_table.binpb` | 默认 asset：`hardware_identification/mapping_table_patch.binpb` |
| `"asset://[path]"` | 指定 asset 路径（须是编译后的 **.binpb** 路径） | 规则同左 |
| `"appfile://[filePath]"` | 本地：`/data/user_de/10/com.google.android.factory.factory/files/[filePath]` | 同左；**filePath 不得以 `_hardware_identification` 开头** |
| 其他任意值 | **非法**，action 失败 | **非法**，action 失败 |

**asset:// 注意：**  
若源文件为  
`factory/app/src/main/hardware_identification/mapping_table-abc.txtpb`  
或同路径的 `.binpb`，字段值都必须是：  
`asset://hardware_identification/mapping_table-abc.binpb`

---

## 附录 F：可硬件探测的部件类型

请在 Component Types 页面中，查看 **“HW desc probeability”** 列为 **Probeable** 的类型。

---

## 快速对照：国内产线一句话记忆

| 英文 | 一句话 |
|------|--------|
| Hardware Descriptor | 写进 VPD 的「本机装了哪些关键料」编码 |
| Mapping table | Google 给的「BOM/SKU/可探测属性」总表（.txtpb/.binpb） |
| Mapping table patch | 临时改探测期望值的补丁表（必须回报 Google） |
| HwDescProbeProvision | 探测 + 算出制造信息 + 编码 + 写 VPD |
| HwDescVerify | 读 VPD 里的描述符，对照表与实机做合规校验 |
| Device data | Factory App 侧的本机运行时数据（phase、MLB PN、DB PNs 等） |
| Shopfloor | 产线系统，常负责下发 SN/SKU/料号等到 device data |
| Second-source | 二供；多候选时需 probe 或 additional_component_pns 才能唯一确定 |
| Side-load | 不走 image 内置，而从 drive/asset 加载最新表 |
| DOME / Factory Drive | 工厂侧文件分发机制与存储 |

---

*译本基于 2026-03-10 版外部用户指南整理，便于 ODM / 工厂工程与 Google 术语对齐。具体以 Google 最新官方文档为准。*
