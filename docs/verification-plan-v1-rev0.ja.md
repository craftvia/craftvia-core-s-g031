# CraftVia Core-S G031 v1 Rev0 検証計画

> 状態: 未検証Rev0ハードウェア用の検証計画Draftです。試験項目に含まれることは、結果を示すものではありません。

この文書は英語正本 [`verification-plan-v1-rev0.md`](verification-plan-v1-rev0.md) の日本語翻訳版です。解釈に差異がある場合は英語版を優先します。

## 1. 試験記録

試験前に次を記録します。

| 項目 | 記録 |
| --- | --- |
| 基板Serial／Sample ID |  |
| PCB表示 | `CraftVia Core-S G031 v1 Rev0` |
| 実装元・実装日 |  |
| BOM差異・Rework |  |
| 試験日・担当者 |  |
| 周囲条件 |  |
| 測定器・校正状態 |  |
| 電源の電流制限値 |  |
| Firmware Commit／Build |  |
| Option Byte |  |
| Station／Fixture Revision |  |

各試験は **Pass**、**Fail**、**Blocked**、**Not run** のいずれかで記録し、測定値とEvidenceへのLinkを添付します。

## 2. 安全条件と中止条件

- すべての電源を切った状態でのみCoreを挿入・取り外します。
- 初回通電には電流制限付き3.3 V電源を使用します。
- 想定外の発熱、発煙、異臭、電源の不安定、逆向き、電流制限動作があれば直ちに中止します。
- SWCLKが能動的に駆動されている間は`SYS_BOOT`を駆動しません。
- 標準実装の受入試験では任意の発振回路を実装しません。

## 3. 必要機材

- Microscopeまたは拡大鏡
- 抵抗・導通Modeを備えたDigital Multimeter
- 電流制限付き3.3 V Bench電源
- 必要に応じてOscilloscopeとLogic Analyzer
- SWD Probeまたは検証済みStation-S
- Core-S全接点用の導通FixtureまたはBreakout配線
- GPIO、UART、I2C、SPI、ADC、Timer、Low-power Modeを試験するFirmware

## 4. 試験項目

| ID | 試験 | 方法 | 合格基準 | Evidence |
| --- | --- | --- | --- | --- |
| `CSG-R0-001` | Release識別 | 基板表示、Source Release、BOM、Gerber名、SHA-256 Manifestを照合する。 | 全識別情報がv1 Rev0を示し、凍結FileがManifestと一致する。 | 写真、Checksum Log |
| `CSG-R0-002` | 外観検査 | U1とJ1-J7の向き、はんだ接合、短絡、断線、DNP Site、Pad損傷を確認する。 | 実装が正規BOMと一致し、目視欠陥がない。 | 注釈付き両面写真 |
| `CSG-R0-003` | 機械寸法 | 外形、厚さ、接点Pitch、Key位置、Pin突出量、Header整列を測定する。 | 外形・接点位置がCore-S v1を満たし、過大な力や衝突なく嵌合する。 | 測定表 |
| `CSG-R0-004` | 無通電電源検査 | `+3V3`／`VDD_CORE`からGNDへの抵抗を測定し、R7導通を確認する。 | 短絡がなく、実装回路として説明可能な安定値を示し、R7経路が導通する。 | 抵抗値 |
| `CSG-R0-005` | Key接点 | `K-NW`／`K-NE`からGND、`K-SW`からR7経由U1 VDD/VDDAを確認する。 | 2つのGND KeyがGNDへ導通し、電源KeyがR7経由でVDDへ到達し、相互短絡がない。 | 導通表 |
| `CSG-R0-006` | 信号導通・絶縁 | 全N/E/S/W接点を公開Mapおよび隣接接点絶縁と照合する。N8を含める。 | 全接点が意図したNodeへ到達し、N8は内部絶縁され、意図しない隣接短絡がない。 | 完成した接点Matrix |
| `CSG-R0-007` | 初回通電 | 外部Signalを駆動せず、保守的な電流制限で3.3 Vを印加し、電流と温度を監視する。 | 電源が安定し、電流制限が作動せず、異常発熱がない。 | 電流Trace、温度記録 |
| `CSG-R0-008` | Rail電圧 | `+3V3`、`VDD_CORE`、U1 VDD/VDDA、TP1を測定する。 | すべての給電Nodeが3.135–3.465 Vに収まり、測定不確かさの範囲で相互に整合する。 | 電圧表 |
| `CSG-R0-009` | Reset | N1／TP5を操作し、NRST Timingと再起動動作を観測する。 | ResetがLowへAssertされ、きれいに解除され、電源制限を逸脱せず確実にMCUを再起動する。 | Scope Capture |
| `CSG-R0-010` | SWD接続・復旧 | N2/N3/N1またはTP3/TP4/TP5から接続し、Device ID読出し、Erase、Program、Debug、Connect-under-resetを行う。 | 正しいDeviceを検出し、必要な全操作が繰り返し成功する。 | Tool Log、Version |
| `CSG-R0-011` | Boot選択 | SWCLKを切り離し、文書化されたN1/N4 Sequenceで対応InterfaceからSystem Memory Bootを試す。 | Normal BootとSystem Memory Bootをそれぞれ確実に選択でき、N3/N4競合が観測されない。 | Option Byte記録、Capture |
| `CSG-R0-012` | 標準Clock | HSI16と意図したPLL設定で動作させ、既知のClock由来出力を測定する。 | Firmwareが確実に起動し、測定周波数が設定公差内にある。 | Firmware、周波数記録 |
| `CSG-R0-013` | 標準UART | W1/W2をUSART1 TX/RXとして代表Baud Rateで試験する。 | FramingまたはMapping Errorなく双方向通信が成功する。 | Loopback／Log |
| `CSG-R0-014` | 標準I2C・SPI | W3/W4をI2C1、W5-W8をSPI1として、意図する場合は同時設定も含めて試験する。 | 転送が成功し、Pin Mappingが仕様と一致する。 | Bus Capture |
| `CSG-R0-015` | Analog入力 | E1-E8へ既知の安全な電圧を印加し、ADC結果を記録する。 | Channel対応が正しく、誤差・Noiseが記録され、Test Setupに対して許容できる。 | 校正表 |
| `CSG-R0-016` | Timer・GPIO | S1-S6とN5-N7を動作させ、Input、Output、Timer Waveformを確認する。 | 各接点が正しく対応し、想定外の競合がない。 | Waveform／接点Log |
| `CSG-R0-017` | PC14/PC15制約 | MCUの駆動・負荷制限内でS7/S8を動作させ、標準R3/R10経路を確認する。 | 2つの標準GPIO経路が動作し、発振Option部品が実装されていない。 | 導通、Waveform Log |
| `CSG-R0-018` | 電力Mode | 識別済みFirmwareとDebug状態で、Reset、Run、Sleep、Stopの電流を記録する。 | 測定に再現性があり、説明できないRail動作や回り込み給電がない。 | 電流表 |
| `CSG-R0-019` | Station嵌合 | 無通電でStation-Sへ挿入して整列を確認し、SWD、Reset、UART、電源試験を繰り返す。 | 衝突や過大な力がなく、電気機能がFixture単体結果と一致する。 | 写真、再試験Log |

## 5. 検証判定

Rev0を **Verified** と表示できるのは、少なくとも1枚の製品代表実装ですべての必須試験が合格し、安全な意図用途に影響する未解決Failureがなく、結果が公開された場合だけです。Failureは可視のまま残し、電気回路、Footprint、配線、実装対象を修正する場合は新しいPCB Revisionを使用します。
