# CraftVia Station-S v1 Rev0 Verification Plan

> Status: Draft verification plan for unverified Rev0 hardware. Perform tests in order; later powered tests depend on earlier inspection and isolation checks.

The primary Rev0 development-interface target is ST-LINK/SWD with Core-S G031. The frozen N2/N3 USB route follows a withdrawn draft and is excluded from current Core-S USB-profile conformance testing. USB evaluation is optional and may proceed only after a safe N7/N8 rework is reviewed and documented.

## 1. Test record

| Field | Record |
| --- | --- |
| Board serial or sample ID |  |
| PCB marking | `CraftVia Station-S Rev0` |
| Assembly source and date |  |
| BOM deviations or rework |  |
| Test date and operator |  |
| Instruments and calibration state |  |
| Power source and current limit |  |
| DEV/DUT Core and revision |  |
| CTRL Core and revision, if fitted |  |
| Firmware commit/build |  |
| Initial JP1-JP11 state |  |

Every result is **Pass**, **Fail**, **Blocked**, or **Not run**, with measurements and evidence.

## 2. Safety and stop conditions

- Never install more than one JP1 source jumper.
- Change jumpers and insert/remove Core-S boards only with all input sources disconnected.
- Begin powered tests with no Core, LCD, external target, or CTRL Core installed.
- Use a current-limited source and stop for unexpected current, rail collapse, heating, smoke, odor, or USB host warnings.
- Remove JP2 only while unpowered and only when inserting an external ammeter.

## 3. Required equipment

- microscope or magnifier;
- digital multimeter;
- current-limited 5 V supply and suitable USB power source;
- oscilloscope and logic analyzer;
- external ammeter or source-measure instrument;
- USB host with STLINK tools and serial terminal;
- verified Core-S G031 or separately validated fixture;
- known loads for target-current measurement.

## 4. Tests

| ID | Test | Method | Pass criteria | Evidence |
| --- | --- | --- | --- | --- |
| `STS-R0-001` | Release identity | Verify board marking, source release, BOM, Gerber, and SHA-256 manifest. | All files and markings identify v1 Rev0 and match the manifest. | Photos and checksum log |
| `STS-R0-002` | Visual and population inspection | Inspect polarity, IC orientation, USB/STLINK connectors, headers, DNP sites, soldering, and jumper headers. | Population matches the BOM and no visible defect is found. | Annotated photos |
| `STS-R0-003` | Outline and drills | Measure the 140 x 90 mm outline, corner radius, four corner holes, four additional 3.2 mm NPTH holes, and two 2.2 mm NPTH holes. | Dimensions and hole types/locations match the release record within fabrication tolerance. | Measurement sheet |
| `STS-R0-004` | Core socket mechanics | Check DEV socket/key orientation and unpowered mating with the intended Core headers. Inspect H5-H7 contact choice. | Correct orientation, full seating, no collision, acceptable force, and no bent contacts. | Photos and measurements |
| `STS-R0-005` | Unpowered rail isolation | With all jumpers removed, measure resistance among `USB_C_5V`, `STLINK_5V`, `EXT_5V`, `SYS_5V`, `VDD_CTRL`, `VDD_SHUNT_IN`, `VDD_TARGET`, and GND. | No unintended short or parallel source path; readings are stable and explainable. | Resistance matrix |
| `STS-R0-006` | JP1 truth table | Check continuity for JP1 pairs 1-2, 3-4, and 5-6 one at a time. | Each pair connects only its named source to `SYS_5V`; no pair bridges another source. | Continuity table |
| `STS-R0-007` | Regulator startup | Power each available input source separately with no Core fitted; measure `SYS_5V` and `VDD_CTRL`. | Stable startup, no current-limit event, `VDD_CTRL` within 3.135-3.465 V, no abnormal heating. | Waveforms and voltage/current table |
| `STS-R0-008` | Target path | Fit JP2 and measure `VDD_CTRL`, `VDD_SHUNT_IN`, both sides of RS1, and `VDD_TARGET` with no load and known loads. | Correct polarity and continuity; voltage drop agrees with load and 0.1 ohm shunt within component/instrument tolerance. | Load table |
| `STS-R0-009` | External ammeter mode | Power off, remove JP2, insert an ammeter, then repeat a known-load measurement. | Safe series connection; external reading and INA226 path can be compared without bypassing RS1. | Setup photo and readings |
| `STS-R0-010` | INA226 communication | Using a fitted/programmed CTRL Core or external I2C controller, scan and read U2. | Expected address responds; bus voltage and shunt readings are stable. | I2C log |
| `STS-R0-011` | INA226 measurement response | Compare zero or near-zero load and multiple safely distinguishable known loads against an external meter. This is a functional characterization, not a precision calibration. | INA226 bus-voltage and shunt/current readings are stable, use the expected polarity, and change consistently with applied load. Record the difference from the external meter without claiming a Rev0 accuracy rating. | Comparison table and raw readings |
| `STS-R0-012` | STLINK enumeration | Select only JP1 3-4 and connect U3 to a PC. | STLINK enumerates without USB faults and reports its expected interfaces. | Host log |
| `STS-R0-013` | DEV supply and T_VCC | Fit JP2/JP11, install the Core unpowered, then power and measure `VDD_TARGET` and STLINK target voltage. | Core receives 3.3 V and STLINK senses the same target domain after the shunt. | Voltage table |
| `STS-R0-014` | SWD | Set JP3 for SWD, JP4 open, JP7 1-2, JP8/JP11 closed; program and debug Core-S G031. | Detect, erase, program, run, halt, step, reset, and connect-under-reset all succeed. | STLINK log |
| `STS-R0-015` | Reset and boot controls | Exercise DEV reset and the documented JP3/JP4 isolation sequence for normal and system-memory boot. | Reset is reliable; boot modes select without N3/N4 contention. | Scope capture and boot log |
| `STS-R0-016` | VCP direction | With JP9/JP10 closed, send data Core PA9/W1 to STLINK input and STLINK output to Core PA10/W2. | Bidirectional traffic passes; U3 pin 12 behaves as STLINK input and pin 15 as STLINK output. | Serial log and captures |
| `STS-R0-017` | STLINK isolation | Open JP8-JP11 and check the DEV nets with STLINK unpowered and separately powered. | Isolated paths do not clamp or back-power the DEV Core beyond documented leakage. | Voltage/resistance log |
| `STS-R0-018` | User controls | Close JP5/JP6 and exercise reset, DEV user button, and DEV user LED. | Button polarity and LED polarity match the specification; opening JP5/JP6 isolates them. | Firmware/log |
| `STS-R0-019` | USB-C power | Select only JP1 1-2 and power through USB-C. Inspect CC/VBUS/protection behavior. | No host warning; regulated rails meet limits; no other 5 V source is back-powered. | USB and rail log |
| `STS-R0-020` | USB rework disposition, optional | Inspect the frozen N2/N3 USB route and record that it is outside current-profile conformance. If an engineering review finds a safe rework feasible, document trace cuts and jumpers that isolate N2/N3 and connect connector D-/D+ to N7/N8; inspect and continuity-check before power. USB enumeration and transfer may then be attempted with a compatible Core. | A no-rework decision is recorded as `Not run` and USB remains unverified; or the approved rework record, isolation checks, enumeration, transfer, and retained ST-LINK/SWD recovery all pass. | Review record, annotated photos, continuity matrix, host and signal-integrity log if run |
| `STS-R0-021` | Breakout continuity | Verify every DEV contact and relevant CTRL/LCD/test-point footprint against the published net map. | All intended nodes are continuous with no adjacent short. | Completed matrix |
| `STS-R0-022` | Source and load budget | Measure input current and regulator temperature with representative DEV, CTRL, LCD, indicator, and target loads. | Each source stays within its declared budget; rails stay in tolerance; temperature is acceptable and recorded. | Load/thermal report |
| `STS-R0-023` | Repeated power-cycle | Repeat cold start, reset, programming, UART, and measurement tests. | No intermittent connector, jumper, startup, or enumeration failure. | Cycle count and log |

## 5. Verification decision

Basic Station-S verification requires all safety, power, DEV socket, ST-LINK/SWD, reset, VCP, breakout, and measurement tests to pass. `STS-R0-020`, USB, and CTRL/LCD functions are optional and may be reported separately; a skipped or unavailable USB rework does not block basic Rev0 verification and USB remains explicitly unverified. Design changes require a new PCB revision; failed Rev0 evidence is retained.
