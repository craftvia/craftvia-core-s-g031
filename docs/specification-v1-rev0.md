# CraftVia Core-S G031 v1 Rev0 Specification

> [!WARNING]
> **Status: Frozen design, unverified hardware**  
> Rev0 has been ordered but has not been assembled, powered, or electrically validated. This document specifies the frozen implementation; it does not claim verified Core-S conformance.

## 1. Identity and authority

| Item | Value |
| --- | --- |
| Product | CraftVia Core-S G031 |
| Product version | v1 |
| PCB revision | Rev0 |
| MCU | STM32G031K8T6 |
| Package | LQFP32, 7 mm x 7 mm, 0.8 mm pitch |
| CAD | EasyEDA |
| Frozen editable source | [`../source/CraftVia-Core-S-G031-v1-Rev0.eprj2`](../source/CraftVia-Core-S-G031-v1-Rev0.eprj2) |
| Fabrication master | [`../fabrication/Gerber_PCB1_2026-09-30.zip`](../fabrication/Gerber_PCB1_2026-09-30.zip) |
| Release record | [`../RELEASE_NOTES.md`](../RELEASE_NOTES.md) |

The frozen source, fabrication master, authoritative BOM, and release record control Rev0. The schematic PDF is a human-readable reference and still contains historical draft labels.

The common interface is defined by the [Core-S Interface v1](https://github.com/craftvia/craftvia-docs/blob/main/standards/core-s-interface-v1.md).

## 2. Intended role

Core-S G031 is the first Core-S implementation and is intended for small control systems, sensor interfaces, logging, fixtures, basic ADC and timer work, and UART/I2C/SPI prototyping. Development-only features are kept on Station-S.

It is not intended as a universal USB, CAN, high-performance motor-control, or large-memory module.

## 3. Hardware summary

| Item | Rev0 implementation |
| --- | --- |
| PCB | 25.4 mm x 25.4 mm, 4 copper layers |
| Supply | 3.3 V through `K-SW`; GND through `K-NW` and `K-NE` |
| CPU | Arm Cortex-M0+, up to 64 MHz |
| Memory | 64 KiB Flash, 8 KiB SRAM |
| Debug | SWD |
| Default clock | Internal HSI16; PLL may be used |
| Optional clock | 8 MHz CMOS oscillator footprint, DNP in Rev0 |
| Core-S contacts | 32 signal contacts and 3 key/power contacts |
| Test points | VTref, GND, SWDIO, SWCLK/BOOT0 node, NRST |

## 4. Contact mapping

Package-pin numbers refer to the STM32G031K8T6 LQFP32 package. Representative functions are not a substitute for the STMicroelectronics datasheet and alternate-function tables.

| Contact | Frozen net | MCU pin | Package pin | Representative role or constraint |
| --- | --- | --- | ---: | --- |
| `K-NW` | GND | VSS/VSSA | 5 | Shared ground |
| `K-NE` | GND | VSS/VSSA | 5 | Shared ground |
| `K-SW` | `+3V3` | VDD/VDDA through R7 | 4 | 3.3 V input |
| `N1` | `SYS_NRST` | PF2-NRST | 6 | Active-low reset |
| `N2` | `SYS_SWDIO` | PA13 | 24 | SWDIO; ADC_IN17 |
| `N3` | `SYS_SWCLK` | PA14-BOOT0 through R1 | 25 | SWCLK; shared boot node |
| `N4` | `SYS_BOOT` | PA14-BOOT0 through R2 | 25 | Boot request; not an independent GPIO |
| `N5` | `CV_N5_PA11` | PA11 | 22 | GPIO/AUX |
| `N6` | `CV_N6_PA12` | PA12 | 23 | GPIO/AUX |
| `N7` | `CV_N7_PB8` | PB8 | 32 | GPIO/AUX |
| `N8` | NC | - | - | Internally unconnected; do not drive as a required function |
| `E1` | `CV_E1_PA0` | PA0 | 7 | ADC_IN0 and alternate functions |
| `E2` | `CV_E2_PA1` | PA1 | 8 | ADC_IN1 and alternate functions |
| `E3` | `CV_E3_PA2` | PA2 | 9 | ADC_IN2; USART2_TX candidate |
| `E4` | `CV_E4_PA3` | PA3 | 10 | ADC_IN3; USART2_RX candidate |
| `E5` | `CV_E5_PA4` | PA4 | 11 | ADC_IN4 and alternate functions |
| `E6` | `CV_E6_PA5` | PA5 | 12 | ADC_IN5 and alternate functions |
| `E7` | `CV_E7_PA6` | PA6 | 13 | ADC_IN6 and alternate functions |
| `E8` | `CV_E8_PA7` | PA7 | 14 | ADC_IN7 and alternate functions |
| `S1` | `CV_S1_PA8` | PA8 | 18 | TIM1_CH1 candidate |
| `S2` | `CV_S2_PB0` | PB0 | 15 | ADC_IN8; timer functions |
| `S3` | `CV_S3_PB1` | PB1 | 16 | ADC_IN9; timer functions |
| `S4` | `CV_S4_PC6` | PC6 | 20 | Timer functions |
| `S5` | `CV_S5_PB2` | PB2 | 17 | GPIO/ADC |
| `S6` | `CV_S6_PB9` | PB9 | 1 | GPIO/AUX |
| `S7` | `CV_S7_PC14` | PC14-OSC32_IN through R3 | 2 | GPIO in default build; clock-option conflict |
| `S8` | `CV_S8_PC15` | PC15-OSC32_OUT through R10 | 3 | GPIO in default build; clock-option conflict |
| `W1` | `CV_W1_PA9` | PA9 | 19 | USART1_TX recommended |
| `W2` | `CV_W2_PA10` | PA10 | 21 | USART1_RX recommended |
| `W3` | `CV_W3_PB6` | PB6 | 30 | I2C1_SCL recommended |
| `W4` | `CV_W4_PB7` | PB7 | 31 | I2C1_SDA recommended |
| `W5` | `CV_W5_PB3` | PB3 | 27 | SPI1_SCK recommended |
| `W6` | `CV_W6_PB4` | PB4 | 28 | SPI1_MISO recommended |
| `W7` | `CV_W7_PB5` | PB5 | 29 | SPI1_MOSI recommended |
| `W8` | `CV_W8_PA15` | PA15 | 26 | SPI1_NSS recommended |

The PA9/PA10 remap to the PA11/PA12 package positions is disabled in the standard profile. Firmware enabling that remap is not compatible with this published mapping.

## 5. SWD, reset, and boot

- `N2` connects directly to PA13/SWDIO.
- `N1` connects to PF2-NRST and C3=100 nF to GND. Optional R6=10 kOhm pull-up is DNP.
- `N3` reaches PA14-BOOT0 through R1=33 ohm.
- `N4` reaches the same PA14-BOOT0 node through R2=1 kOhm.
- A host must not actively drive N3 and N4 against each other.
- Before asserting `SYS_BOOT`, isolate or high-impedance the SWCLK driver, assert reset, set the boot level, release reset, then return `SYS_BOOT` to high impedance.
- Boot option-byte assumptions must be recorded during validation; they are not proven by the frozen PCB alone.

TP1-TP5 provide VTref, GND, SWDIO, the PA14 SWCLK/BOOT0 node, and NRST for recovery and measurement.

## 6. Power and default population

`K-SW` supplies `+3V3`. R7=0 ohm connects `+3V3` to `VDD_CORE`; substituting a compatible ferrite bead is an option for a future evaluated population. C1=100 nF and C2=4.7 uF decouple the combined VDD/VDDA supply.

The Core contains no input regulator and does not provide reverse-polarity, overvoltage, overcurrent, surge, or hot-plug protection.

### Populated functional parts

The authoritative BOM contains 16 populated designators: `C1`, `C2`, `C3`, `J1-J7`, `R1`, `R2`, `R3`, `R7`, `R10`, and `U1`.

### DNP options

`C4`, `R4`, `R5`, `R6`, `R8`, `R9`, `R11`, and `X1` are DNP. TP1-TP5 are bare test-point features and are not BOM items.

The exported CPL contains all 29 designators and must be filtered against the authoritative BOM before turnkey assembly.

## 7. Clock configuration

The Rev0 default uses HSI16 and leaves the 8 MHz ASEDVN oscillator option unpopulated. R3 and R10 connect S7/S8 to PC14/PC15 in the default build.

The optional oscillator population is mutually exclusive with the corresponding GPIO use and has not been validated. Do not populate or enable it using assumptions from the footprint alone; use a documented population variant and a new validation record.

PC14 and PC15 have restrictive drive and load characteristics. Do not use them as LED current sources; firmware and external loading must follow the MCU datasheet.

## 8. Manufacturing and mechanical conditions

- J1-J4 are 2.54 mm, 1x8 through-hole male headers in the reference design.
- J5-J7 are single-pin key/power headers.
- The exact square-pin dimensions, mating length, insulator height, Station socket, assembled stack height, and insertion force remain unverified.
- The Gerber ZIP is the sole PCB fabrication master.
- The schematic and assembly PDFs are review references.

## 9. Known Rev0 limitations

- No assembled or powered sample has been tested.
- N3/N4 shared-pin boot behavior and resistor values are unverified.
- Connector fit and mechanical stack are unverified.
- Current consumption and thermal behavior are unknown.
- The optional external oscillator population is unverified.
- No STM32CubeMX `.ioc` or bring-up firmware is included in this hardware release yet.
- The schematic PDF retains historical draft labels.

## 10. Verification

Verification is controlled by [`verification-plan-v1-rev0.md`](verification-plan-v1-rev0.md). Passing results must identify the physical sample, assembly population, firmware, instruments, and evidence. Do not change this document to “Verified” until all mandatory acceptance tests pass or explicit exceptions are approved and recorded.

