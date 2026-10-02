# CraftVia Core-S G031 v1 Rev0 Hardware Release

## Release status

- Revision: Rev0
- Release date: 2026-09-30
- Status: FROZEN for initial prototype PCB fabrication
- Intended use: Rev0 engineering prototype; not a general-production release
- CAD: EasyEDA v3.2.149.88089769
- Authoritative editable source: `source/CraftVia-Core-S-G031-v1-Rev0.eprj2`
- Authoritative PCB fabrication upload: `fabrication/Gerber_PCB1_2026-09-30.zip`

This release was checked against the project opened in EasyEDA on 2026-09-30. The schematic and PCB document UUIDs, component population, PCB primitive counts and net count correspond to the frozen source package.

## Release decision

The package is acceptable for an initial Rev0 PCB fabrication order, subject to the ordering and assembly conditions below.

- PCB strict DRC: PASS, 0 errors
- Schematic DRC: Fatal 0, Error 0, Warning 1
- The remaining schematic warning is the known EasyEDA Supplier Part attribute-normalization warning for J1-J7, R1-R3, R7 and R10. It is not an electrical-connectivity error.
- Gerber ZIP integrity: PASS
- Board: 25.4 mm x 25.4 mm, 4 copper layers
- PCB contents checked: 29 components, 287 routed line primitives, 32 vias, 2 copper pours and 40 nets

## Release contents

| Path | Purpose |
| --- | --- |
| `source/CraftVia-Core-S-G031-v1-Rev0.eprj2` | Authoritative editable EasyEDA project |
| `source/ProPrj_CraftVia-Core-S-G031-v1-Rev0.epro` | Portable EasyEDA project archive |
| `fabrication/Gerber_PCB1_2026-09-30.zip` | Canonical PCB fabrication package |
| `assembly/BOM_Board1_PCB1_2026-09-30.csv` | Authoritative populated-part BOM |
| `assembly/PickAndPlace_PCB1_2026_09_30.csv` | Full-board coordinate export; filter before turnkey assembly upload |
| `assembly/PCB_PCB1_2026-09-30.pdf` | PCB, BOM and assembly/layer reference export |
| `docs/SCH_Core-S-G031-Rev0_2026-09-30.pdf` | Human-readable schematic reference |
| `SHA256SUMS.txt` | SHA-256 integrity manifest |

## PCB fabrication package

Upload `fabrication/Gerber_PCB1_2026-09-30.zip` directly to the PCB manufacturer. Do not edit or recompress its contents.

The ZIP contains:

- Top, Inner 1, Inner 2 and Bottom copper
- Top and Bottom solder mask
- Top and Bottom silkscreen
- Top paste mask
- Top assembly and document layers
- Board outline
- PTH component-hole drill and PTH via drill

No NPTH drill file is present. This is expected because this Core board has no intentional non-plated mounting holes. No Bottom paste file is expected because there are no Bottom-side SMT parts.

Before submitting the order, load the exact ZIP into JLCDFM or the board manufacturer's Gerber preview and confirm:

- 25.4 mm x 25.4 mm outline
- four copper layers
- header and test-point holes are recognized as plated through holes
- Top/Bottom silkscreen and solder-mask openings are present
- no unexpected NPTH holes or drill offsets are reported

The manufacturer's order-time stack-up selection is authoritative. Use a normal 4-layer FR-4 prototype stack unless a later release specifies a controlled stack-up.

## Assembly population

The BOM contains 15 lines and 16 populated designators:

`C1, C2, C3, J1, J2, J3, J4, J5, J6, J7, R1, R2, R3, R7, R10, U1`

The following components are intentionally DNP in Rev0:

`C4, R4, R5, R6, R8, R9, R11, X1`

The following test points are physical PCB features but are not populated BOM items:

`TP1, TP2, TP3, TP4, TP5`

### Pick-and-place warning

`assembly/PickAndPlace_PCB1_2026_09_30.csv` is an unfiltered 29-designator board export. It includes the eight DNP options and five test points in addition to the 16 BOM designators.

- For PCB fabrication only, this does not affect the Gerber order.
- For turnkey assembly, do not upload the CPL unchanged.
- Filter the CPL to the 16 authoritative BOM designators, or verify that the assembler explicitly accepts extra DNP/test-point rows.
- BOM population status takes precedence over the unfiltered CPL and the visual red-X markings in the assembly PDF.

J1-J4 use the supplier reference `C49256`, but the library data does not define the required square-pin dimensions, mating-pin length or insulation height. Confirm the actual header against the Station-S socket stack before purchasing or assembling it. J5-J7 use `C81276` with a nominal 6 mm mating pin and 2.5 mm insulator height.

## Default electrical configuration

- Input supply: 3.3 V
- R7: fitted, 0 ohm; supplies `VDD_CORE`
- R3: fitted, 0 ohm GPIO link
- R10: fitted, 0 ohm
- External 8 MHz oscillator option: DNP
- Internal HSI is the default clock source
- Optional oscillator support parts remain DNP as listed above
- PA14 is shared by SWCLK and BOOT0; use the Station-S isolation/selection arrangement when changing boot state

## Known documentation note

The schematic PDF still contains the historical labels `SCHEMATIC DRAFT` and `DESIGN NOTES - NOT RELEASED FOR FABRICATION`. For this frozen folder, the schematic PDF is a circuit-review reference only; it is not a manufacturing master. This README and the SHA-256 manifest define the Rev0 release status, and the Gerber ZIP is the sole PCB fabrication master.

For a later revision or a cleaned documentation-only repack, remove those historical labels in the EasyEDA title/notes and regenerate the schematic PDF from an otherwise unchanged source.

## First-article checks

1. Inspect orientation and continuity of J1-J7 before inserting the Core into Station-S.
2. Power from a current-limited 3.3 V supply and confirm no short between `VDD_CORE` and GND.
3. Measure 3.3 V at U1 VDD/VDDA and verify the expected idle current.
4. Verify SWD connection through PA13/SWDIO, PA14/SWCLK and PF2/NRST.
5. Verify BOOT behavior with the PA14 shared-pin network.
6. Exercise all N/E/S/W edge pins with a continuity fixture or Station-S.
7. Leave X1 and its option network DNP for the initial internal-HSI bring-up.

## Change control and integrity

Verify every file listed in `SHA256SUMS.txt` before ordering. After this release is used for an order, do not overwrite any file captured by the `v1-rev0` tag or its GitHub Release assets. Electrical, footprint, routing, silkscreen or population changes require a new revision. Documentation-only corrections must be made in a later commit without moving or replacing the tag.
