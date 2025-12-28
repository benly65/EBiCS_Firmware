# STM32F103C8Tx 64KB flash notes

## Why the 64KB change is required
STM32F103C8Tx is officially a 64KB flash part. Many boards expose 128KB in practice, but not all.
If firmware uses pages 126/127 (addresses beyond 64KB), EEPROM emulation fails on genuine 64KB
chips and the controller may not boot.

## EEPROM changes (Inc/eeprom.h)
Use the last two pages of the 64KB map — **pages 62 and 63**:
- `EEPROM_START_ADDRESS` → `ADDR_FLASH_PAGE_62`
- `PAGE0_ID` → `ADDR_FLASH_PAGE_62`
- `PAGE1_ID` → `ADDR_FLASH_PAGE_63`

Pages 64..127 are wrapped in `#if 0` to make it explicit that they belong to the unofficial 128KB map.

### Reverting to 128KB (at your own risk)
In `Inc/eeprom.h`:
1. Switch the commented `EEPROM_START_ADDRESS` line to `ADDR_FLASH_PAGE_126`.
2. Switch the commented `PAGE0_ID`/`PAGE1_ID` lines to `ADDR_FLASH_PAGE_126`/`ADDR_FLASH_PAGE_127`.
3. Optionally remove the `#if 0` around pages 64..127.

## Linker script (STM32F103C8Tx_FLASH.ld)
The main linker script reserves the last 2KB for EEPROM when using 64KB flash:
```
FLASH (rx)  : ORIGIN = 0x08000000, LENGTH = 0xF800
EEPROM (rx) : ORIGIN = 0x0800F800, LENGTH = 0x800
```

### Reverting to 128KB (at your own risk)
In `STM32F103C8Tx_FLASH.ld`, uncomment the 128KB layout:
```
// FLASH (rx)  : ORIGIN = 0x08000000, LENGTH = 0x1F800
// EEPROM (rx) : ORIGIN = 0x0801F800, LENGTH = 0x800
```

## Summary
Default is **64KB** (safe for genuine STM32F103C8Tx parts). To use unofficial 128KB flash, edit:
- `Inc/eeprom.h` (EEPROM pages 126/127).
- `STM32F103C8Tx_FLASH.ld` (uncomment the 128KB layout).
