# CraftVia Station-S v1 Rev0 Specification

> [!WARNING]
> **Status: Frozen design, unverified hardware**  
> Rev0 has been ordered but has not been assembled, powered, or electrically validated. All operating modes and limits below require first-article verification.

## 1. Identity and authority

| Item | Value |
| --- | --- |
| Product | CraftVia Station-S |
| Product version | v1 |
| PCB revision | Rev0 |
| Primary supported Core | CraftVia Core-S G031 v1 Rev0 |
| CAD | EasyEDA |
| Frozen editable source | [`../source/CraftVia-Station-S-v1-Rev0.eprj2`](../source/CraftVia-Station-S-v1-Rev0.eprj2) |
| Fabrication master | [`../fabrication/Gerber_PCB1_2026-09-30.zip`](../fabrication/Gerber_PCB1_2026-09-30.zip) |
| Release record | [`../RELEASE_NOTES.md`](../RELEASE_NOTES.md) |

The frozen design, fabrication master, authoritative BOM, and release record control Rev0. The seven-page schematic PDF and assembly PDF are review references.

## 2. Purpose

Station-S is the common development and measurement platform for Core-S hardware. Rev0 is designed to provide:

- a DEV/DUT Core-S position;
- on-board STLINK-V3MODS SWD and VCP UART;
- selectable Station power input and regulated 3.3 V rails;
- separate control and target supply domains;
- target voltage and current measurement using INA226;
- manual selection between SWD and an N2/N3 USB path;
- user controls, breakout access, and prototyping space;
- footprints for a separate CTRL Core-S and display interface.

The CTRL Core and display are not required for basic DEV/DUT SWD use.

## 3. Physical construction

| Item | Rev0 implementation |
| --- | --- |
| Board outline | 140.0 mm x 90.0 mm rounded rectangle |
| Corner radius | Approximately 3.0 mm |
| Copper layers | 4 |
| Corner mounting holes | Four 3.2 mm NPTH at `(5,5)`, `(135,5)`, `(5,85)`, `(135,85)` mm |
| Additional NPTH | Four 3.2 mm and two 2.2 mm holes at the release-record coordinates |
| Bottom-side SMT | None |

Socket type, stack height, and clearance with Core-S headers remain verification items. H5-H7 are identified as TBD in the frozen design.

## 4. Core-S positions

### 4.1 DEV/DUT

H1-H4 expose N, W, E, and S respectively; H5-H7 are the three key/power contacts. `K-NW` and `K-NE` connect to GND and `K-SW` connects to `VDD_TARGET`.

For Core-S G031 Rev0:

| Core contact | Station function |
| --- | --- |
| N1 | `DEV_N1`; reset path through JP8 |
| N2 | `DEV_N2`; SWDIO or selected USB D- path |
| N3 | `DEV_N3`; SWCLK or selected USB D+ path |
| N4 | `DEV_N4`; manual BOOT selection through JP4 |
| N5 | User button through JP5 |
| N6 | User LED through JP6 |
| N7/N8 | Breakout; no fixed Rev0 debug driver |
| W1 | Core UART TX to STLINK input path through JP9 |
| W2 | STLINK output path to Core UART RX through JP10 |
| E/S and remaining W | Breakout access |

The GPIO breakout footprints are not populated by the authoritative BOM.

### 4.2 CTRL footprint

Rev0 includes CTRL Core-S footprints H20-H26 and associated breakout/control nets. These parts are excluded from the authoritative BOM and require a deliberate manual population plan.

The CTRL position is not connected to the on-board STLINK SWD interface. A CTRL Core must be programmed in the DEV/DUT position or by an external fixture before installation.

The frozen functional mapping includes:

| CTRL contacts | Function |
| --- | --- |
| N1-N4 | `CTRL_NRST`, `CTRL_SWDIO`, `CTRL_SWCLK`, `CTRL_BOOT` test/breakout paths only |
| N5/N6 | CTRL user button and status LED |
| E1/E2 | Reserved target-power enable and Core-identification nets |
| E5-E8 | LCD SPI signals |
| W3/W4/W5 | INA226 SCL, SDA, ALERT |
| W6/W7 | LCD D/C and reset |
| K-SW | `VDD_CTRL` |

Reserved nets do not imply that the associated control function has been implemented or validated.

## 5. Power architecture

### 5.1 Input selection

JP1 manually selects exactly one 5 V source:

| JP1 pair | Source |
| --- | --- |
| 1-2 | USB-C `USB_C_5V` |
| 3-4 | STLINK-V3MODS `STLINK_5V` |
| 5-6 | protected external `EXT_5V` |

**Never fit more than one JP1 source pair at the same time.** Rev0 does not provide automatic power-path ORing.

USB-C VBUS passes through a resettable fuse. EXT_5V uses a resettable fuse and P-channel MOSFET reverse-polarity stage. The exact protection behavior remains unverified.

### 5.2 Regulated domains

U1 is an AP63203WU-7 regulator generating the 3.3 V system rail. `VDD_CTRL` supplies Station control circuitry and any populated CTRL Core. The target path runs from `VDD_CTRL` through the JP2 external-ammeter bypass and RS1=0.1 ohm shunt to `VDD_TARGET`.

`VDD_TARGET` supplies the DEV/DUT Core and target-output connection. CTRL, INA226, LCD, and Station indicators are outside the measured target-current path.

The STLINK 5 V source is treated as having a 200 mA budget. Total load and regulator behavior must be measured before relying on that source.

## 6. Measurement path

U2 is an INA226AIDGSR supplied from `VDD_CTRL`. It measures the high-side voltage across RS1 and the target bus voltage. The design uses Kelvin sense routing and exposes measurement-related test points.

JP2 is normally shorted. To insert an external ammeter, remove power, remove the JP2 jumper, connect the ammeter in series across JP2, verify polarity/range, and only then reapply power. RS1 and INA226 remain in circuit for comparison.

Rev0 is not specified for precision microamp measurement. Use an external SMU or power analyzer for that purpose.

## 7. STLINK-V3MODS and VCP

U3 is an STLINK-V3MODS. SWD is connected only to the DEV/DUT position.

The frozen VCP routing uses the target-side signal convention:

- U3 pin 12 `T_VCP_TX` is an **input to STLINK**. It connects through R16 and JP9 to `DEV_W1`, the Core-S G031 PA9/UART_TX output.
- U3 pin 15 `T_VCP_RX` is an **output from STLINK**. It connects through R17 and JP10 to `DEV_W2`, the Core-S G031 PA10/UART_RX input.

This convention supersedes earlier planning notes that described the names from the probe side.

JP8 isolates NRST, JP9 and JP10 isolate the VCP paths, and JP11 isolates STLINK target-voltage sensing. They are normally closed for STLINK operation and opened for a USB-only Core path.

## 8. SWD, boot, and USB selection

| Jumper | Rev0 role | Default intent |
| --- | --- | --- |
| JP3 | SWCLK connection/isolation | Closed for SWD |
| JP4 | `DEV_N4` boot selection to GND or `VDD_TARGET` | Open for normal boot |
| JP5 | DEV user-button connection | Closed |
| JP6 | DEV user-LED connection | Closed |
| JP7 | 1-2 SWD mode; 2-3 USB mode | 1-2 for Core-S G031 |
| JP8 | NRST isolation | Closed for STLINK |
| JP9 | VCP input isolation | Closed for STLINK |
| JP10 | VCP output isolation | Closed for STLINK |
| JP11 | T_VCC isolation | Closed for STLINK |

USB1 is a USB4085-GF-A USB-C receptacle. CC1 and CC2 use 5.1 kOhm Rd resistors. D+/D- pass through ESD protection and U10 FSUSB42 before reaching the selected N2/N3 path. No logic-level translator is inserted in the USB data path.

The USB path is intended for a Core that explicitly supports USB on N2/N3. Core-S G031 Rev0 does not provide USB on those contacts.

Change jumpers and replace a Core only while unpowered.

## 9. User interface and expansion

- DEV reset button, user button, and user LED;
- CTRL user button and status LED paths;
- USB VBUS and USB-mode indicators;
- H27 ten-pin unpopulated LCD interface for shared I2C or SPI signals;
- unpopulated DEV and CTRL contact breakout footprints;
- independent 2.54 mm prototype areas.

An LCD module, CTRL Core, and breakout headers are not included in the authoritative Rev0 BOM unless explicitly added by the assembler.

## 10. Assembly state

The authoritative BOM contains 69 populated designators. The unfiltered CPL contains 114 designators and must be reconciled with the BOM.

The BOM includes the DEV socket parts, STLINK module, USB connector, power and measurement circuitry, and JP1-JP11 header positions. Removable jumper caps are separate items and are not represented as individual BOM lines.

Several supplier fields are EasyEDA aliases rather than normal LCSC numbers. Resolve the actual manufacturer parts before paying for assembly, especially for U1, U3, U10, USB1, and headers.

## 11. Known Rev0 limitations

- No assembled or powered sample has been tested.
- The CTRL socket footprints and LCD connector are excluded from the authoritative BOM.
- The CTRL position has no on-board SWD connection.
- H5-H7 socket selection and the Core-S mechanical stack are unresolved.
- Power-source protection, regulation, measurement accuracy, and thermal margin are unverified.
- USB/SWD mux behavior is unverified.
- Rev0 does not include automatic source selection, a USB hub, automatic probe selection, target power switching, overcurrent shutdown, or precise microamp measurement.

## 12. Verification

Use [`verification-plan-v1-rev0.md`](verification-plan-v1-rev0.md). The validation record must identify all jumper positions, installed optional parts, Core revision, firmware, instruments, and power source.

