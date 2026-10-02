# CraftVia Core-S G031 v1 Rev0 仕様書

> [!WARNING]
> **状態：凍結済み設計・未検証ハードウェア**  
> Rev0は発注済みですが未実装・未通電・未検証です。本書は凍結済み実装を規定するもので、Core-S適合検証済みという意味ではありません。[英語版](specification-v1-rev0.md)を正本とします。

## 1. 識別と正本

| 項目 | 内容 |
| --- | --- |
| 製品 | CraftVia Core-S G031 |
| Version | v1 |
| PCB Revision | Rev0 |
| MCU | STM32G031K8T6 |
| Package | LQFP32、7 mm x 7 mm、0.8 mm pitch |
| CAD | EasyEDA |
| 凍結済み編集可能source | [`../source/CraftVia-Core-S-G031-v1-Rev0.eprj2`](../source/CraftVia-Core-S-G031-v1-Rev0.eprj2) |
| PCB製造正本 | [`../fabrication/Gerber_PCB1_2026-09-30.zip`](../fabrication/Gerber_PCB1_2026-09-30.zip) |
| Release記録 | [`../RELEASE_NOTES.md`](../RELEASE_NOTES.md) |

凍結済みsource、Gerber、正本BOM、Release記録をRev0の正とします。回路図PDFは人が確認するためのReferenceで、過去のDraft表記が残っています。

共通インターフェースは[Core-S Interface v1](https://github.com/craftvia/craftvia-docs/blob/main/standards/core-s-interface-v1.md)で定義します。

## 2. 位置づけ

小規模制御、Sensor、Logger、治具、基本ADC／Timer、UART／I2C／SPI試作を主対象とします。開発専用機能はStation-Sへ分離します。汎用USB、CAN、高性能Motor制御、大容量Memoryを主対象とはしません。

## 3. Hardware概要

| 項目 | Rev0実装 |
| --- | --- |
| PCB | 25.4 mm x 25.4 mm、4層 |
| 電源 | `K-SW`から3.3 V、`K-NW`と`K-NE`がGND |
| CPU | Arm Cortex-M0+、最大64 MHz |
| Memory | Flash 64 KiB、SRAM 8 KiB |
| Debug | SWD |
| 標準Clock | HSI16、必要に応じてPLL |
| 任意Clock | 8 MHz CMOS oscillator footprint、Rev0ではDNP |
| 接点 | 32信号接点＋3 Key／電源接点 |
| Test point | VTref、GND、SWDIO、SWCLK/BOOT0 node、NRST |

## 4. 端子割り当て

Package pinはSTM32G031K8T6 LQFP32の番号です。代表機能はdatasheetとAlternate Function表の代替ではありません。

| 端子 | 凍結済みnet | MCU pin | Package pin | 代表用途・制約 |
| --- | --- | --- | ---: | --- |
| K-NW | GND | VSS/VSSA | 5 | 共通GND |
| K-NE | GND | VSS/VSSA | 5 | 共通GND |
| K-SW | `+3V3` | R7経由VDD/VDDA | 4 | 3.3 V入力 |
| N1 | `SYS_NRST` | PF2-NRST | 6 | Active Low RESET |
| N2 | `SYS_SWDIO` | PA13 | 24 | SWDIO、ADC_IN17 |
| N3 | `SYS_SWCLK` | R1経由PA14-BOOT0 | 25 | SWCLK、BOOT共有 |
| N4 | `SYS_BOOT` | R2経由PA14-BOOT0 | 25 | BOOT要求。独立GPIOではない |
| N5 | `CV_N5_PA11` | PA11 | 22 | GPIO/AUX |
| N6 | `CV_N6_PA12` | PA12 | 23 | GPIO/AUX |
| N7 | `CV_N7_PB8` | PB8 | 32 | GPIO/AUX |
| N8 | NC | - | - | MCU内部未接続 |
| E1 | `CV_E1_PA0` | PA0 | 7 | ADC_IN0等 |
| E2 | `CV_E2_PA1` | PA1 | 8 | ADC_IN1等 |
| E3 | `CV_E3_PA2` | PA2 | 9 | ADC_IN2、USART2_TX候補 |
| E4 | `CV_E4_PA3` | PA3 | 10 | ADC_IN3、USART2_RX候補 |
| E5 | `CV_E5_PA4` | PA4 | 11 | ADC_IN4等 |
| E6 | `CV_E6_PA5` | PA5 | 12 | ADC_IN5等 |
| E7 | `CV_E7_PA6` | PA6 | 13 | ADC_IN6等 |
| E8 | `CV_E8_PA7` | PA7 | 14 | ADC_IN7等 |
| S1 | `CV_S1_PA8` | PA8 | 18 | TIM1_CH1候補 |
| S2 | `CV_S2_PB0` | PB0 | 15 | ADC_IN8、Timer |
| S3 | `CV_S3_PB1` | PB1 | 16 | ADC_IN9、Timer |
| S4 | `CV_S4_PC6` | PC6 | 20 | Timer |
| S5 | `CV_S5_PB2` | PB2 | 17 | GPIO／ADC |
| S6 | `CV_S6_PB9` | PB9 | 1 | GPIO/AUX |
| S7 | `CV_S7_PC14` | R3経由PC14-OSC32_IN | 2 | 標準実装はGPIO、Clock optionと競合 |
| S8 | `CV_S8_PC15` | R10経由PC15-OSC32_OUT | 3 | 標準実装はGPIO、Clock optionと競合 |
| W1 | `CV_W1_PA9` | PA9 | 19 | USART1_TX推奨 |
| W2 | `CV_W2_PA10` | PA10 | 21 | USART1_RX推奨 |
| W3 | `CV_W3_PB6` | PB6 | 30 | I2C1_SCL推奨 |
| W4 | `CV_W4_PB7` | PB7 | 31 | I2C1_SDA推奨 |
| W5 | `CV_W5_PB3` | PB3 | 27 | SPI1_SCK推奨 |
| W6 | `CV_W6_PB4` | PB4 | 28 | SPI1_MISO推奨 |
| W7 | `CV_W7_PB5` | PB5 | 29 | SPI1_MOSI推奨 |
| W8 | `CV_W8_PA15` | PA15 | 26 | SPI1_NSS推奨 |

標準ProfileではPA9/PA10のPA11/PA12 package位置へのremapを無効とします。Remapを有効にしたFirmwareは公開マップと非互換です。

## 5. SWD、RESET、BOOT

- N2はPA13/SWDIOへ接続
- N1はPF2-NRSTへ接続し、C3=100 nFでGNDへ接続。R6=10 kOhm pull-upはDNP
- N3はR1=33 ohm経由でPA14-BOOT0へ接続
- N4はR2=1 kOhm経由で同じPA14-BOOT0へ接続
- N3とN4を相反するLevelで同時駆動しない
- `SYS_BOOT`を駆動する前にSWCLKを分離／High-Zとし、RESETをassert、BOOT level設定、RESET解除後に`SYS_BOOT`をHigh-Zへ戻す
- Option byte条件は検証時に記録する

TP1～TP5はVTref、GND、SWDIO、PA14 SWCLK/BOOT0 node、NRSTを提供します。

## 6. 電源と標準実装

`K-SW`の`+3V3`はR7=0 ohmを介して`VDD_CORE`へ供給されます。C1=100 nFとC2=4.7 uFでVDD/VDDAをdecoupleします。CoreにはRegulator、逆接、過電圧、過電流、Surge、Hot-plug保護を搭載しません。

### 実装済み機能部品

正本BOMの16 designatorは`C1`、`C2`、`C3`、`J1-J7`、`R1`、`R2`、`R3`、`R7`、`R10`、`U1`です。

### DNPオプション

`C4`、`R4`、`R5`、`R6`、`R8`、`R9`、`R11`、`X1`はDNPです。TP1～TP5はBOM対象外のTest pointです。CPLは29 designatorを含むため、Turnkey assembly前にBOMでfilterしてください。

## 7. Clock

Rev0標準はHSI16を使用し、8 MHz ASEDVN oscillator optionはDNPです。標準実装ではR3/R10によりS7/S8をPC14/PC15へ接続します。

Oscillator optionはGPIO利用と排他で未検証です。専用の実装variantと検証記録なしに実装しないでください。PC14/PC15の出力・負荷制約に従い、LED電流源として使用しません。

## 8. 製造・機械条件

- J1-J4は2.54 mm 1x8 through-hole male header
- J5-J7は1 pin Key／電源header
- Pin断面、嵌合長、insulator高さ、Station socket、stack高さ、挿抜力は未検証
- Gerber ZIPだけをPCB製造正本とする
- 回路図・組立PDFはReference

## 9. Rev0既知制約

- 組立・通電sampleなし
- N3/N4共有BOOTと抵抗値未検証
- Connector fitとmechanical stack未検証
- 消費電流・温度未測定
- Optional oscillator未検証
- STM32CubeMX `.ioc`とBring-up Firmwareは本Releaseに未収録
- 回路図PDFに過去のDraft表記あり

## 10. 検証

[`verification-plan-v1-rev0.ja.md`](verification-plan-v1-rev0.ja.md)に従います。実機、実装、Firmware、測定器、証拠を特定し、必須試験完了までVerifiedへ変更しません。
