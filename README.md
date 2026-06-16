# Kimchi HiFi Lid — SGTL PCB

This directory contains the KiCad hardware files for the **SGTL5000-based audio PCB variant** of the Kimchi HiFi Lid project.

The board is an audio codec / interface PCB intended to mate with the main Kimchi hardware through high-density board-to-board connectors. It includes the audio codec, clocking, analog I/O connectors, EEPROM, local power filtering, and supporting passives required for the SGTL audio path.

## Design overview

Primary design intent:

- Provide a compact audio codec board for the Kimchi HiFi Lid assembly.
- Interface the audio codec and related signals to the parent system through two 60-pin Hirose board-to-board connectors.
- Break out analog audio through RCA and 3.5 mm audio connectors.
- Provide local clocking, EEPROM, level/interface support, and power filtering around the codec.

## Main functional blocks

- **Audio codec:** `U2` — `SGTL5000XNLA3`, NXP SGTL5000 stereo audio codec.
- **Clock source:** `X1` — `ASFL1-12.288MHZ-EC-T`, 12.288 MHz oscillator.
- **EEPROM:** `U1` — `24C32` / `M24C32-FMC6TG` I²C EEPROM.
- **Board-to-board connectors:** `J2`, `J3` — `DF40HC(3.5)-60DS` 60-pin connectors.
- **Analog audio I/O:**
  - `J1`, `J4` — `RCJ-2123` RCA jacks.
  - `J5` — `54-00176` 3.5 mm audio jack.
  - `MIC_IN` and `MIC_BIAS` nets for microphone input support.
- **Logic support:** `U3` — `SN74LV1T34DBVRG4` single-bit buffer / level interface.
- **Power rails and filtering:** `VDD_5V`, `VDD_3V3`, `VDD_1V8`, ferrite beads, bulk capacitance, and local bypassing.

## Directory contents

| Path | Purpose |
|---|---|
| `kimchi_ulid.sch` | Main KiCad / Eeschema schematic. |
| `kimchi_ulid.sch-bak` | Backup copy of the schematic. |
| `kimchi_ulid.lib` | Local symbol library. |
| `kimchi_ulid-cache.lib` | Cached schematic symbols. |
| `kimchi_ulid.dcm` | KiCad symbol documentation metadata. |
| `kimchi_ulid.csv` | BOM / component export. See warning below before ordering. |
| `kimchi_ulid.xml` | KiCad-generated netlist / BOM source export. |
| `kimchi_ulid.pretty/` | Local KiCad footprints and related 3D assets. |
| `gerber/` | Exported manufacturing files. |
| `sym-lib-table` | KiCad symbol library table. |
| `fp-lib-table` | KiCad footprint library table. |

## KiCad version notes

This project uses the older KiCad / Eeschema file format, with the schematic stored as `kimchi_ulid.sch`.

For editing in a newer KiCad release:

1. Open the project/schematic in KiCad.
2. Let KiCad convert legacy schematic/library data if prompted.
3. Save the converted project on a new branch before overwriting the original files.
4. Re-run ERC, DRC, BOM export, and Gerber generation after conversion.

## Fabrication notes

The `gerber/` directory contains exported fabrication data, including copper, solder mask, silkscreen, fabrication, and edge-cut files.

Before sending this board out for manufacture, regenerate the manufacturing package from the current KiCad project and verify:

- Board outline and mounting holes.
- Layer stackup, especially the inner copper layers.
- DF40 connector orientation, stack height, and mating-board alignment.
- RCA and 3.5 mm jack mechanical clearance.
- Power rail assignments from the parent board.
- Audio routing, grounding, and codec analog supply filtering.
- I²C / digital audio signal mapping to the parent processor.

## Important BOM warning

The schematic in this SGTL folder uses `SGTL5000XNLA3` for `U2`, but the checked-in `kimchi_ulid.csv` may be stale and may still list `U2` as `WM8750CJLGEFL` from an earlier codec revision.

Do **not** order directly from the CSV without regenerating the BOM from the current schematic.

Recommended release process:

1. Open the schematic in KiCad.
2. Confirm `U2` is the intended SGTL5000 part.
3. Regenerate the BOM / CSV from the active schematic.
4. Compare the regenerated BOM against the committed `kimchi_ulid.csv`.
5. Update the CSV before fabrication or assembly.

## Bring-up checklist

Suggested first-power and validation flow:

- Inspect the board for solder bridges, especially around the codec QFN and DF40 connectors.
- Verify no shorts on `VDD_5V`, `VDD_3V3`, `VDD_1V8`, and ground before power-up.
- Power the board from a current-limited bench supply or through the intended parent board with current monitoring.
- Confirm local rails are present and stable.
- Confirm the 12.288 MHz oscillator output.
- Verify I²C communication with the codec and EEPROM.
- Confirm codec reset / enable behavior if controlled by the parent system.
- Test analog output at low level first, then validate full-scale playback.
- Test microphone input and bias only after confirming the audio codec is enumerating/configuring correctly.

## Status

Hardware design files and generated manufacturing outputs are present, but this directory should be treated as an engineering design package rather than a guaranteed production release. Regenerate and review all release outputs before ordering boards or parts.
