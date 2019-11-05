# Smartwatch
Official website: https://wiki.pine64.org/index.php/PineTime

This repo is just my sandbox for playing around with PineTime firmware.
These notes are only tested on Ubuntu 18.04.3 LTS

# Compiling notes
 * `sudo apt install gcc-arm-none-eabi`
 * Download the [nRF5 SDK v16.0](https://www.nordicsemi.com/Software-and-Tools/Software/nRF5-SDK) and extract it somewhere. Set `$NRF_SDK` to that path.
 * Update the cross-compiler paths and version in `$NRF_SDK/components/toolchain/gcc/Makefile.posix`  :
```
GNU_INSTALL_ROOT ?= /usr/bin
GNU_VERSION ?= 6.3.1
GNU_PREFIX ?= arm-none-eabi
```

## Using the SDK
Looks like the SDK wants the custom board to be called `custom_board.h` and
then needs to see `#define BOARD_CUSTOM`

# Flashing notes

## initial unprotect + erase
from https://forum.pine64.org/showthread.php?tid=8143

```
source [find interface/cmsis-dap.cfg]
source [find target/nrf52.cfg]

init
nrf52.dap apreg 1 0x04 0x01
sleep 100
shutdown
```

# Schematic notes
(Just a few random notes recorded when parsing the schematic and datasheets.)

## SoC + flash
[nRF52832](https://www.nordicsemi.com/Products/Low-power-short-range-wireless/nRF52832)
 * ARM Cortex-M4F
 * crystals: 32.768 kHz and 32 MHz
 * external SPI flash: PM25LV512
   * 64 Kbyte (512 Kbit)
   * 256 byte pages, 4 Kbyte sectors, 32 Kbyte blocks
   * 2ms page program, 60ms sector erase
   * 200,000 program/erase cycles per sector

## Power scheme
Single-cell LiPo. Charge cradle powered by USB (5 VDC) feeds +5V through two
parallel Schottkey diodes into the charge IC. Battery powers two LDO's which
power everything except the vibration motor and heart-rate sensor.
 * charger IC: SGM40561
 * VCC33 (main LDO): ME6206
 * VDD33 (low-noise sensor LDO): SGM2036-3.3

## Sensors
 * accelerometer: Bosch BMA421
 * touch sensor (not populated?): tontek TTP233D-QA6

## LCD
 * connected via SPI
