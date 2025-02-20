# CBI: CrOS Board Info

**Useful links**
[CBI: CrOS Board Info](https://chromium.googlesource.com/chromiumos/docs/+/master/design_docs/cros_board_info.md)

## Definitions:

- Device: A computer running Chrome OS.
- Family: A set of devices which share the same reference design. (e.g. Fizz).
- Model: Within a family, devices are distinguished by non-customizable hardware features (e.g. chassis, motherboard). They’re grouped as a model and usually given a code name (e.g. Teemo, Sion). Within a model, devices are distinguished by SKU.
- SKU: Numeric value describing customizable hardware features (e.g. CPU, memory, storage, touch panel, keyboard, etc.)

## CBI Image Format

| Offset | # bytes |    data name     |                         description                          |
| :----: | :-----: | :--------------: | :----------------------------------------------------------: |
|   0    |    3    |      MAGIC       |                   0x43, 0x42, 0x49 (‘CBI’)                   |
|   3    |    1    |       CRC        |       8-bit CRC of everything after CRC through ITEM_n       |
|   4    |    1    | FORMAT_VER_MINOR | The data format version. It’s 0.0 (0x00, 0x00) for the initial version. |
|   5    |    1    | FORMAT_VER_MAJOR |                                                              |
|   6    |    2    |    TOTAL_SIZE    |          Total number of bytes in the entire data.           |
|   8    |    x    | ITEM_0,..ITEM_n  |      List of data items. The format is explained below.      |

## Data Fields

| Offset | # bytes | data name |                   description                   |
| :----: | :-----: | :-------: | :---------------------------------------------: |
|   0    |    1    |   <tag>   | A numeric value uniquely assigned to each item. |
|   1    |    1    |  <size>   | Total size of the item (i.e. <tag><size><data>) |
|   2    |    x    |  <value>  | Value can be a string, raw data, or an integer. |

Here are a list of standard data items. See `ec/include/cros_board_info.h` for the latest information. Data sizes can vary project to project. Optional items are not listed but can be added as needed by the project.

| BOARD_VERSION | 0    | integer | yes  | yes  | Board version (0, 1, 2, ...)       |
| ------------- | ---- | ------- | ---- | ---- | ---------------------------------- |
| OEM_ID        | 1    | integer | yes  | no   | OEM ID                             |
| MODEL_ID      | 5    | integer | no   | no   | ID assigned to each model.         |
| SKU_ID        | 2    | integer | no   | yes  | ID assigned to each SKU.           |
| DRAM_PART_NUM | 3    | string  | yes  | no   | DRAM part name in ascii characters |
| OEM_NAME      | 4    | string  | yes  | no   | OEM name in ascii characters       |

### BOARD_VERSION

The number assigned to each hardware version. This field ideally should be synchronously incremented as the project progresses and should not differ among models.

Assignment can be different from project to project but it’s recommended to be a single-byte field (instead of two-byte field where the upper half describes phases (EVT, DVT, PVT) and the lower half describes numeric versions in each phase).

### OEM_ID

A number assigned to each OEM. Software shared by multiple OEMs can use this field to select a customization common to a particular OEM. For example, it can be used to control LEDs, which tend to follow an OEM's preference.

### MODEL_ID

A number assigned to each model. Numbering is managed within each OEM. So, <OEM_ID, MODEL_ID> should form a unique ID within the family. This allows a host tool such as mosys to share the code to derive a model name among variants in the family. It also allows EC to identify a battery pack, which is usually a per-model configuration.

### SKU_ID

The number used to describe hardware configuration or hardware features. Assignment varies family to family and usually is shared among all models in the same family.

Some family uses it as an index for a SKU table where hardware features are described for SKUs. An array of C struct can be auto-generated ([go/cros-publish-hw-config-runtime](https://goto.google.com/cros-publish-hw-config-runtime)).

It can also be a bitmap where each bit or set of bits represents one hardware feature. For example, it can contain information such as CPU type, form factor, touch panel, camera, keyboard backlight, etc. Top 8 bits can be reserved for OEM specific features to allow OEMs to customize devices independently.

### DRAM_PART_NUM ([go/octopus-dram-part-number-retrieval](https://docs.google.com/document/d/17WugKTbeDBWYe4GplmraOKyX9tb_g76Vu5Qckb75oXs/edit?usp=sharing))

A string value that is used to identify a DRAM. This is different from RAM ID, which is an index to a table where RAM configuration parameters are stored. RAM ID is encoded in resistor strapping. This makes RAM ID visually validatable (as opposed to being a field in CBI). DRAM_PART_NUM is used to track the inventory.

### OEM_NAME (TBD)

OEM name in ascii string.

## Software

### mosys

Mosys is a tool which runs on the host to provide various information of the host. One of its sub-command, platform, retrieves board information (e.g. SKU, OEM) from SMBIOS, which is populated by coreboot. We’ll update coreboot so that it’ll fetch board information from EEPROM (via EC) instead of resistor strapping.

### cbi-util

A tool which runs on a build machine. It creates a EEPROM image file with given field values. Manufacturers use CBI image files to pre-flash EEPROMs in large volume. This tool also can print the contents of a CBI image file.

### ectool

A tool which runs on a host (i.e. Chromebooks). It can fetch CBI data from the EEPROM (through the EC). Manufacturers can use this tool to validate the EEPROM contents. If the board is not write protected, ectool can change the CBI. Note that some fields are not expected to be changed after the board is manufactured. The data of existing fields can be changed and resized.

### cbi_info

This is a script used by debugd to include CBI dump in user feedback reports. Currently, it prints only BOARD_VERSION, OEM_ID, and SKU_ID.

## Example

### Write SKUID

```bash
# ectool cbi set SKUID_TYPE SKUID_NUMBER OEMID
ectool cbi set 2 1 1
```

### Write Dram Part Number

```bash
ectool cbi set 3 dram_part_number 0
```
