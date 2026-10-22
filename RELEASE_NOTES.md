# CraftVia Station-S v1 Rev0 Hardware Release

## Release status

- Revision: Rev0
- Release date: 2026-09-30
- Status: FROZEN for initial prototype PCB fabrication
- Intended use: Rev0 engineering prototype; not a general-production release
- CAD: EasyEDA v3.2.149.88089769
- Authoritative editable source: `source/CraftVia-Station-S-v1-Rev0.eprj2`
- Authoritative PCB fabrication upload: `fabrication/Gerber_PCB1_2026-09-30.zip`

This release was checked against the project opened in EasyEDA on 2026-09-30. The project, schematic and PCB identifiers, VCP correction, component population, PCB primitive counts and net count correspond to the frozen source package.

## Core-S USB profile mismatch

Rev0 physically routes its target USB path to Core-S N2/N3 according to an earlier pre-verification interface draft. The current [Core-S Debug, Programming, and USB Port v1](https://github.com/craftvia/craftvia-docs/blob/main/standards/core-s-debug-port-v1.md) retains N2/N3 for basic debug/data and assigns native USB to N7/N8.

- This mismatch does not affect the primary Rev0 ST-LINK/SWD use with Core-S G031.
- Rev0 does not conform to the current native USB profile in its frozen state.
- The N2/N3 USB path is excluded from current-profile conformance testing.
- USB may be evaluated only if an N7/N8 rework is first judged electrically and mechanically feasible, documented, inspected, and continuity-checked.
- Native N7/N8 USB routing is planned for Station-S v1 Rev1.
- Frozen source, Gerber, BOM, schematic, and assembly exports remain unchanged as the authoritative Rev0 implementation record.

## Release decision

The package is acceptable for an initial Rev0 PCB fabrication order, subject to the assembly and ordering conditions below.

- PCB strict DRC: PASS, 0 errors
- Schematic DRC: Fatal 0, Error 0, Warning 2
- The remaining schematic warnings are non-fatal. EasyEDA's bridge result exposes the count but not the detailed UI message; no PCB connectivity or clearance error is associated with them.
- Gerber ZIP integrity: PASS
- Board: 140.0 mm x 90.0 mm, approximately 3.0 mm corner radius, 4 copper layers
- PCB contents checked: 114 component/feature footprints, 736 routed line primitives, 50 vias, 7 copper pours and 108 nets

## VCP connection verified

The corrected STLINK-V3MODS VCP routing was checked in the frozen design:

- U3 pin 12 `T_VCP_TX` (STLINK input) -> R16 -> JP9 -> `DEV_W1` -> Core-S PA9/UART_TX
- U3 pin 15 `T_VCP_RX` (STLINK output) -> R17 -> JP10 -> `DEV_W2` -> Core-S PA10/UART_RX

The STLINK signal names use the target-side convention: `T_VCP_TX` is an input to STLINK and `T_VCP_RX` is an output from STLINK.

## Release contents

| Path | Purpose |
| --- | --- |
| `source/CraftVia-Station-S-v1-Rev0.eprj2` | Authoritative editable EasyEDA project |
| `source/ProPrj_CraftVia-Station-S-v1-Rev0_2026-09-30.epro` | Portable EasyEDA project archive |
| `fabrication/Gerber_PCB1_2026-09-30.zip` | Canonical PCB fabrication package |
| `assembly/BOM_Board1_PCB1_2026-09-30.csv` | Authoritative populated-part BOM |
| `assembly/PickAndPlace_PCB1_2026_09_30.csv` | Full-board coordinate export; filter before turnkey assembly upload |
| `assembly/PCB_PCB1_2026-09-30.pdf` | PCB, BOM and assembly/layer reference export |
| `docs/SCH_Schematic1_2026-09-30.pdf` | Human-readable seven-page schematic reference |
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
- NPTH, plated component-hole and plated-via drill data

No Bottom paste file is expected because there are no Bottom-side SMT parts.

Before submitting the order, load the exact ZIP into JLCDFM or the board manufacturer's Gerber preview and confirm:

- 140.0 mm x 90.0 mm rounded-rectangle outline
- four copper layers
- the four corner mounting holes at `(5,5)`, `(135,5)`, `(5,85)` and `(135,85)` mm are 3.2 mm NPTH
- four additional 3.2 mm NPTH holes at `(20.099,5.474)`, `(41.099,5.474)`, `(20.099,28.474)` and `(41.099,28.474)` mm
- two 2.2 mm NPTH holes at `(62.484,6.477)` and `(62.484,43.477)` mm
- header, breakout and universal-area holes are recognized as plated through holes
- Top/Bottom silkscreen, component labels and operating instructions are legible and correctly oriented
- no drill offset, duplicate-drill or unexpected slot warning is reported

The ZIP contains both `Drill_PTH_Through.DRL` and `Drill_PTH_Through_Via.DRL`. Use the complete ZIP and verify the manufacturer's preview rather than uploading selected drill files manually.

The manufacturer's order-time stack-up selection is authoritative. Use a normal 4-layer FR-4 prototype stack unless a later release specifies a controlled stack-up.

## Assembly population

The BOM contains 59 lines and 69 populated designators. Of these, the CPL identifies 46 as SMD and 23 as non-SMD/THT.

The following 23 BOM items are non-SMD and normally require THT service or manual assembly:

`H1-H7, H35-H37, J1, JP1-JP11, USB1`

The following PCB features are intentionally excluded from the BOM:

- GPIO and control breakout headers: `H8-H34`, `H38`
- DNP resistors: `R10`, `R11`
- Test points: `TP1-TP15`

### Pick-and-place warning

`assembly/PickAndPlace_PCB1_2026_09_30.csv` is an unfiltered 114-designator board export. It contains 45 excluded/DNP/test-point/breakout rows in addition to the 69 authoritative BOM designators.

- For PCB fabrication only, this does not affect the Gerber order.
- For turnkey assembly, do not upload the CPL unchanged without checking the import result.
- Filter the CPL to the 69 BOM designators, or verify that the assembler uses the BOM/CPL designator intersection and marks all other rows DNP.
- The BOM CSV takes precedence over the unfiltered CPL and the visual red-X markings in the assembly PDF.

Several Supplier Part fields use EasyEDA project/library aliases ending in `.1` instead of normal LCSC `C` numbers. Before paying for assembly, confirm the resolved manufacturer part for every imported BOM line, especially U1, U3, U10, USB1 and the headers.

### Jumper-cap warning

JP1-JP11 are header footprints in the BOM, but removable 2.54 mm jumper caps are not separate BOM items. Procure the required jumper caps separately or define the desired factory configuration explicitly.

Rev0 silkscreen/schematic defaults:

- JP1: fit one power-source pair only; 3-4 selects STLINK power
- JP2: fitted for the normal target-current path; remove before connecting an external ammeter
- JP3: fitted for SWD
- JP4: open for normal boot
- JP5 and JP6: fitted for the default user button/LED connection
- JP7: 1-2 for SWD, 2-3 for USB mode
- JP8-JP11: closed for STLINK operation; open for USB-only operation

Never select multiple JP1 power-source pairs simultaneously.

## Known Rev0 conditions

- The board contains both DEV/DUT and CTRL Core-S sockets. The CTRL Core-S is not connected to the STLINK-V3MODS SWD interface and must be programmed in the DEV/DUT socket before being moved to the CTRL socket.
- H5-H7 are marked `TBD`; confirm the final female contact type and mechanical stack before assembly.
- The assembly PDF is a reference export. Red-X overlays indicate excluded/DNP features and do not replace the BOM.
- USB indicator LEDs are populated by the current BOM even though the schematic notes that DNP is acceptable.

## First-article checks

1. Verify the 140.0 mm x 90.0 mm outline, all ten NPTH holes and both-side silkscreen before accepting manufacture.
2. Check for shorts among `STLINK_5V`, `SYS_5V`, `VDD_CTRL`, `VDD_TARGET` and GND before fitting jumpers.
3. Set only one JP1 power-source pair and power from a current-limited supply.
4. Verify the AP63203 3.3 V rail and measure target current through RS1/INA226.
5. Confirm JP2 behavior before using an external ammeter.
6. Verify SWDIO, SWCLK, NRST and target-voltage sensing through JP3, JP8 and JP11.
7. Verify VCP loopback and target communication through JP9/JP10, including TX/RX direction.
8. Inspect the frozen JP7, USB-C CC, ESD, VBUS and mux paths, and record that the N2/N3 USB output is a legacy-draft path excluded from current-profile conformance. Perform USB enumeration only after an approved and documented N7/N8 rework.
9. Exercise all DEV/DUT and CTRL socket pins with a continuity fixture before inserting Core-S boards.
10. Confirm all jumper-cap states against the intended test mode before every power-up.

## Change control and integrity

Verify every file listed in `SHA256SUMS.txt` before ordering. After this release is used for an order, do not overwrite any file captured by the `v1-rev0` tag or its GitHub Release assets. Electrical, footprint, routing, artwork, silkscreen or population changes require a new revision. Documentation-only corrections must be made in a later commit without moving or replacing the tag.
