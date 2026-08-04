# Zephyr CBI UFSC 配置

## Overview

较新的设备使用名为 UFSC（统一固件和第二源配置）的统一系统。该系统用存储在 CBI 中的单个、基于模式的 128 位（4 个双字）值取代了原有的 FW_CONFIG 和 SSFC 字段。

The primary goals of UFSC are to:
- Unify static (non-probeable) and dynamic (probeable) configuration into a single source of truth.
- Enforce a standardized schema to reduce errors and eliminate board-specific decoding logic.
- Provide a clear and maintainable way to manage hardware variations.
