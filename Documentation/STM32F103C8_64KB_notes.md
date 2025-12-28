# STM32F103C8Tx 64KB flash notes (EEPROM emulation)

Many e-bike controllers use **STM32F103C8** MCUs. Officially they have **64KB** of flash.
Some boards ship with "C8" markings but actually contain **128KB** parts, while others are **genuine 64KB**.

This matters because this firmware uses **EEPROM emulation** and stores settings in the **last 2KB of flash**.
If the firmware is built for 128KB and your chip is really 64KB, it will try to use non‑existent pages and may fail to boot.

## What this branch does

This branch is configured to be **safe for genuine 64KB STM32F103C8**:

- EEPROM emulation uses flash pages **62 and 63** (the last 2KB within 64KB).
- The linker script reserves the same last 2KB, so the application code will not overwrite it.

You normally do **not** need to change anything.

## If your chip is 128KB (optional)

Only do this if you are sure your MCU really has 128KB.

You must change **two files** so they stay consistent:

1) **Linker script**: `STM32F103C8Tx_FLASH.ld`

In the `MEMORY` block, switch the active (uncommented) pair to the 128KB layout:

```ld
// 64KB layout (default)
// FLASH  (rx) : ORIGIN = 0x08000000, LENGTH = 0xF800
// EEPROM (rx) : ORIGIN = 0x0800F800, LENGTH = 0x0800

// 128KB layout
FLASH  (rx) : ORIGIN = 0x08000000, LENGTH = 0x1F800
EEPROM (rx) : ORIGIN = 0x0801F800, LENGTH = 0x0800
```

2) **EEPROM page addresses**: `Inc/eeprom.h`

Switch the active (uncommented) page definitions to the 128KB pages:

```c
// 64KB layout (default)
// #define EEPROM_START_ADDRESS ADDR_FLASH_PAGE_62
// #define PAGE0_ID             ADDR_FLASH_PAGE_62
// #define PAGE1_ID             ADDR_FLASH_PAGE_63

// 128KB layout
#define EEPROM_START_ADDRESS ADDR_FLASH_PAGE_126
#define PAGE0_ID             ADDR_FLASH_PAGE_126
#define PAGE1_ID             ADDR_FLASH_PAGE_127
```

### Important

- **Both files must match** (linker `EEPROM` region <-> `Inc/eeprom.h` pages).
- When switching layouts, it is recommended to do a **full chip erase** before flashing,
  so old EEPROM pages from the previous layout do not confuse the emulation.
