# CraftVia Core-S G031

> [!WARNING]
> **Unverified Rev 0 hardware**  
> The Rev 0 boards have been ordered and are awaiting delivery. They have not yet been assembled, powered, or electrically verified. Manufacturing or assembly is at your own risk, and the design may change in a later revision.

[日本語](README.ja.md) · [CraftVia](https://craftvia.org) · [Rev 0 release notes](RELEASE_NOTES.md)

![CraftVia Core-S G031 v1 Rev0 CAD rendering](docs/images/core-s-g031-v1-rev0-3d.webp)

*Rev0 CAD rendering — [front view](docs/images/core-s-g031-v1-rev0-front.webp) · [back view](docs/images/core-s-g031-v1-rev0-back.webp). Renderings are illustrative; the frozen design source and Gerber package are authoritative.*

CraftVia Core-S G031 is the first implementation of the CraftVia Core-S interface. It packages an STM32G031K8T6 and the circuitry necessary for reliable MCU operation into a compact Core intended for development on Station-S and later installation in a Carrier or other target device.

Development-only functions such as the debugger, USB interface, operator controls, and measurement circuitry are intentionally kept off the Core and provided by Station-S.

## Current status

| Item | Status |
| --- | --- |
| Product generation | v1 |
| PCB revision | Rev0 |
| Fabrication | Boards ordered; awaiting delivery |
| Assembly | Not started |
| Power-on test | Not started |
| Electrical verification | Not started |
| Release tag | `v1-rev0` planned |

## Rev 0 summary

- MCU: STM32G031K8T6, LQFP32
- Core outline: 25.4 mm × 25.4 mm
- PCB: 4 layers
- Core interface: four groups of eight signal contacts plus keyed power contacts
- Supply: 3.3 V
- Debug: SWD; PA14 is shared by SWCLK and BOOT0
- Default clock: internal HSI; external 8 MHz oscillator option is DNP
- CAD source: EasyEDA

The normative mechanical and electrical requirements belong in the [CraftVia common documentation](https://github.com/craftvia/craftvia-docs). See the Rev0 [`specification`](docs/specification-v1-rev0.md) and [`verification plan`](docs/verification-plan-v1-rev0.md) for the product-specific pin mapping, options, limitations, and acceptance tests.

## Repository layout

```text
source/                      Authoritative editable EasyEDA sources
fabrication/                 Canonical Gerber package
assembly/                    BOM, CPL, and assembly reference
docs/                        Schematic PDF and product documentation
product.json                 Machine-readable public product metadata
RELEASE_NOTES.md             Detailed Rev 0 notes and first-article checks
SHA256SUMS.txt               Integrity manifest
```

## Using the Rev 0 release

Read the complete [`v1-rev0` release notes](RELEASE_NOTES.md) before ordering or assembling boards. Important conditions include:

- The Gerber ZIP is the sole PCB fabrication master.
- The exported CPL is unfiltered and must not be used for turnkey assembly without reconciling it against the authoritative BOM.
- Header dimensions and the mechanical stack with Station-S must be confirmed before assembly.
- The schematic PDF contains historical draft labels and is a review reference, not the manufacturing master.
- Verify all release files against [`SHA256SUMS.txt`](SHA256SUMS.txt).

## First-article validation

Initial validation will cover visual inspection, supply shorts, current-limited power-up, rail voltage and current, SWD, BOOT behavior, and continuity of all Core interface contacts. Results will be published without moving or replacing the `v1-rev0` tag. Any design change will use a new PCB revision.

## License and brand

Hardware design files in this repository are licensed under [CERN-OHL-P-2.0](LICENSE), unless a file-specific notice says otherwise. See [CraftVia Licensing](LICENSES/README.md) for category boundaries and attribution guidance.

The CraftVia name and logos are owned by Kohei Ikeda and are not licensed under the hardware license. See the [CraftVia Trademark Policy](LICENSES/TRADEMARK_POLICY.md). Modified or third-party-manufactured hardware must not be presented as an official CraftVia product.
