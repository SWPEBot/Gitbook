# Chrome OS Debug Buttons

## 带键盘的设备

|             Functionality             |                           Shortcut                           |
| :-----------------------------------: | :----------------------------------------------------------: |
|            Clean shutdown             |             Power button, hold for 3 - 8 seconds             |
|             Hard shutdown             |               Power button, hold for 8 seconds               |
|          Screenshot capture           |                 `Ctrl + Switch-Window` *****                 |
|      File feedback / bug report       |                   `Alt + Shift + I` *****                    |
|               Powerwash               |       `Ctrl + Alt + Shift + R` from login screen *****       |
|               EC reset                |                      `Power + Refresh`                       |
|             Recovery mode             | See [firmware keyboard UI](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#Firmware-Keyboard-Interface) section |
|            Developer mode             | See [firmware keyboard UI](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#Firmware-Keyboard-Interface) section |
|            Battery cutoff             |   `Power + Refresh`, hold down, remove power for 5 seconds   |
|             Warm AP reset             |                    `Alt + Volume-Up + R`                     |
|            Restart Chrome             |                 `Alt + Volume-Up + X` *****                  |
|          Kernel panic/reboot          |               `Alt + Volume-Up + X + X` *****                |
|      Reboot EC but don't boot AP      |                `Alt + Volume-Up + Down-Arrow`                |
|          Force EC hibernate           |                    `Alt + Volume-Up + H`                     |
| Override USB-C port used for charging |   `Left-Ctrl + Right-Ctrl + Search + (0 or 1 or 2)` *****    |
|      Virtual terminal switching       |           `Ctrl + Alt + F1 (or F2, F3, F4)` *****            |
|        Show Device Serial Info        |                          `Ctrl + V`                          |

## 不带键盘的设备

| Screen off          | Power button, short press                                    |
| ------------------- | ------------------------------------------------------------ |
| Clean shutdown      | Power button, hold for 3 seconds, select option in menu      |
| Hard shutdown       | Power button, hold for 10 seconds                            |
| Screenshot capture  | `Power + Volume-Down`, short press                           |
| File feedback       | Power button, hold for 3 seconds, select option in menu      |
| EC reset            | `Power + Volume-Up`, hold for 10 seconds                     |
| Recovery mode       | See [firmware menu UI](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#Firmware-Menu-Interface) section |
| Developer mode      | See [firmware menu UI](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#Firmware-Menu-Interface) section |
| Battery cutoff      | `Power + Volume-Up`, hold down, remove power for 5 seconds   |
| Warm AP reset       | See [EC debug mode](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#EC-Debug-Mode) section |
| Restart Chrome      | See [EC debug mode](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#EC-Debug-Mode) section |
| Kernel panic/reboot | See [EC debug mode](https://chromium.googlesource.com/chromiumos/docs/+/master/debug_buttons.md#EC-Debug-Mode) section |
