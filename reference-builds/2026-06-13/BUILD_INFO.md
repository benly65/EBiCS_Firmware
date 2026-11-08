# Diagnostic firmware — 2026-06-13
# Диагностическая прошивка — 13 июня 2026

## Reference binary / Эталонный BIN

File: LishuiFOC_01.bin
Size: 26916 bytes
Build timestamp: 2026-06-13 12:53:49
SHA256: B575D4ECE3EE21010BA97497C19C2B7406F2DEC61CB6BFE162238A7633564BBC

## Source / Исходники

Git branch: work/diagnostic-2026-06-13
Source snapshot commit: 155afc0

RU: Исходники диагностической прошивки с телеметрией UART.
EN: Source snapshot of the diagnostic firmware with UART telemetry.

RU: Побитная воспроизводимость эталонного BIN из этого коммита пока не проверена.
EN: A byte-identical rebuild from this commit has not yet been verified.

## Memory / Память

MCU: STM32F103C8
Program Flash: 62 KiB
EEPROM emulation: 2 KiB (Flash pages 62 and 63)
RAM: 20 KiB

RU: Карта памяти проверена по MAP-файлу исходной сборки.
EN: Memory layout verified against the original build MAP file.

## Field test / Испытания

RU: Диагностическая прошивка применялась на велосипеде с передачей
телеметрии UART через BLE. Выявлена неисправность датчика момента,
проявляющаяся при понижении температуры примерно до +10 C и ниже.

EN: Diagnostic firmware was used on the bicycle with UART telemetry
transmitted over BLE. Testing identified a torque sensor hardware
fault appearing at temperatures around +10 C and below.

## Configuration / Конфигурация

RU: Экспериментальные настройки JavaConfigurator сохранены в Git.
Точное соответствие июньских INI-файлов бинарной сборке не подтверждено.

EN: Historical JavaConfigurator settings are preserved in Git.
The exact correspondence between June INI files and this binary
has not yet been established.
