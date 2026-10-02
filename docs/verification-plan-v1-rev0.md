# CraftVia Core-S G031 v1 Rev0 Verification Plan

> Status: Draft verification plan for unverified Rev0 hardware. No result is implied by inclusion of a test.

## 1. Test record

Record the following before testing:

| Field | Record |
| --- | --- |
| Board serial or sample ID |  |
| PCB marking | `CraftVia Core-S G031 v1 Rev0` |
| Assembly source and date |  |
| BOM deviations or rework |  |
| Test date and operator |  |
| Ambient conditions |  |
| Instruments and calibration state |  |
| Power-supply current limit |  |
| Firmware commit/build |  |
| Option bytes |  |
| Station or fixture revision |  |

Each test result must be marked **Pass**, **Fail**, **Blocked**, or **Not run**, with measurements and evidence links.

## 2. Safety and stop conditions

- Insert or remove the Core only while all supplies are off.
- Use a current-limited 3.3 V supply for first power.
- Stop immediately for unexpected heating, smoke, odor, unstable supply, reversed orientation, or current-limit operation.
- Do not drive `SYS_BOOT` while SWCLK is actively driven.
- Do not populate the optional oscillator network during the default-build acceptance run.

## 3. Required equipment

- microscope or magnifier;
- digital multimeter with resistance and continuity modes;
- current-limited 3.3 V bench supply;
- oscilloscope and logic analyzer as needed;
- SWD probe or verified Station-S;
- continuity fixture or breakout wiring for all Core-S contacts;
- firmware exercising GPIO, UART, I2C, SPI, ADC, timers, and low-power modes.

## 4. Tests

| ID | Test | Method | Pass criteria | Evidence |
| --- | --- | --- | --- | --- |
| `CSG-R0-001` | Release identity | Verify board marking, source release, BOM, Gerber name, and SHA-256 manifest. | All identifiers indicate v1 Rev0 and all frozen files match the manifest. | Photos and checksum log |
| `CSG-R0-002` | Visual inspection | Inspect U1 orientation, J1-J7 orientation, solder joints, shorts, opens, DNP sites, and damaged pads. | Population matches the authoritative BOM; no visible defect. | Annotated top/bottom photos |
| `CSG-R0-003` | Mechanical dimensions | Measure outline, thickness, contact pitch, key locations, pin projection, and header alignment. | Outline and contact locations meet Core-S v1; mating parts align without force or collision. | Measurement sheet |
| `CSG-R0-004` | Unpowered supply checks | Measure resistance from `+3V3`/`VDD_CORE` to GND and verify R7 continuity. | No short; stable readings consistent with the assembled circuit; R7 path continuous. | Resistance readings |
| `CSG-R0-005` | Key contacts | Check `K-NW` and `K-NE` to GND and `K-SW` through R7 to U1 VDD/VDDA. | Both ground keys are continuous to GND; supply key reaches VDD through R7; no cross-short. | Continuity table |
| `CSG-R0-006` | Signal continuity and isolation | Check every N/E/S/W contact against the published mapping and adjacent-contact isolation. Include N8. | All mapped contacts reach the intended node; N8 is isolated internally; no unintended adjacent short. | Completed contact matrix |
| `CSG-R0-007` | First power | Apply 3.3 V with a conservative current limit and no external signal drive. Monitor current and temperature. | Supply remains stable, current limit does not engage, and no component heats abnormally. | Current trace and thermal notes |
| `CSG-R0-008` | Rail voltage | Measure `+3V3`, `VDD_CORE`, U1 VDD/VDDA, and TP1. | All powered nodes are within 3.135-3.465 V and mutually consistent within measurement uncertainty. | Voltage table |
| `CSG-R0-009` | Reset | Exercise N1/TP5 and observe NRST timing and restart behavior. | Reset asserts low, releases cleanly, and reliably restarts the MCU without violating supply limits. | Scope capture |
| `CSG-R0-010` | SWD attach and recovery | Connect through N2/N3/N1 or TP3/TP4/TP5; read device ID, erase, program, debug, and test connect-under-reset. | Correct device is detected and all required operations succeed repeatedly. | Tool log and versions |
| `CSG-R0-011` | Boot selection | With SWCLK isolated, perform the documented N1/N4 sequence and attempt system-memory boot using a supported interface. | Normal boot and system-memory boot can each be selected reliably; no N3/N4 contention is observed. | Option-byte record and captures |
| `CSG-R0-012` | Default clock | Run from HSI16 and the intended PLL configuration; measure a known clock-derived output. | Firmware starts reliably and measured frequency is within the configured tolerance. | Firmware and frequency record |
| `CSG-R0-013` | Standard UART | Test W1/W2 as USART1 TX/RX at representative rates. | Bidirectional communication passes without framing or mapping errors. | Loopback/log |
| `CSG-R0-014` | Standard I2C and SPI | Test W3/W4 as I2C1 and W5-W8 as SPI1, including simultaneous configuration where intended. | Transfers pass and pin mapping matches the specification. | Bus captures |
| `CSG-R0-015` | Analog inputs | Apply known safe voltages to E1-E8 and record ADC results. | Channels map correctly; error and noise are recorded and acceptable for the test setup. | Calibration table |
| `CSG-R0-016` | Timers and GPIO | Exercise S1-S6 and N5-N7; verify expected inputs, outputs, and timer waveforms. | Each tested contact maps correctly with no unexpected conflict. | Waveform/contact log |
| `CSG-R0-017` | PC14/PC15 constraints | Exercise S7/S8 only within MCU drive/load limits; confirm default R3/R10 paths. | Both default GPIO paths work and no oscillator-option part is populated. | Continuity and waveform log |
| `CSG-R0-018` | Power modes | Record current in reset, run, sleep, and stop using identified firmware and debug state. | Measurements are repeatable; no unexplained rail behavior or back-power path is observed. | Current table |
| `CSG-R0-019` | Station mating | Insert into Station-S unpowered, inspect alignment, then repeat SWD, reset, UART, and power checks. | No collision or excessive force; electrical functions match fixture-only results. | Photos and repeat-test log |

## 5. Verification decision

Rev0 may be marked **Verified** only when all mandatory tests pass on at least one identified production-representative assembly, no unresolved failure affects safe intended use, and the result is published. Failures must remain visible and any electrical, footprint, routing, or population correction must use a new PCB revision.
