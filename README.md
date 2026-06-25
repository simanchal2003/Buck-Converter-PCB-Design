# 12V to 5V DC-DC Buck Converter — PCB Design

A complete schematic-to-fabrication design of a non-isolated step-down (buck) converter that regulates a 12V input down to a stable 5V output, built in KiCad. This repository contains the schematic, PCB layout, validation reports, and manufacturing files for the project.

![Status](https://img.shields.io/badge/status-complete-brightgreen) ![Tool](https://img.shields.io/badge/EDA-KiCad-orange) ![Layers](https://img.shields.io/badge/PCB-2--layer-blue)

---

## Overview

This project implements a buck converter using the **LM2596S-5.0** fixed-output switching regulator to convert a 12V DC input (e.g. from a wall adapter or battery pack) to a regulated 5V DC output, suitable for powering microcontroller boards, sensors, and other 5V logic-level circuits.

| Parameter | Value |
|---|---|
| Input voltage | 12V DC (nominal) |
| Output voltage | 5V DC (fixed) |
| Max output current | 3A |
| Switching frequency | 150 kHz (internal oscillator) |
| Topology | Non-synchronous buck (step-down) |
| PCB layers | 2 |
| Design tool | KiCad 8.x |

---

## Features

- Fixed 5V output buck regulator built around the LM2596S-5.0, requiring no external feedback resistor network
- Component values (inductor, capacitors, catch diode) selected directly from manufacturer datasheet design tables, not arbitrary defaults
- 2-layer PCB with a dedicated ground plane and short, wide power loop to minimize switching noise and EMI
- Full design-rule and electrical-rule validation before fabrication
- Manufacturing-ready output: Gerbers, drill file, BOM, and pick-and-place data

---

## Schematic Design

The circuit follows the standard application schematic recommended in the LM2596 datasheet, consisting of:

- **U1 — LM2596S-5.0**: Fixed 5V, 3A step-down switching regulator
- **L1 — 68 µH power inductor**: Sets the energy storage/ripple current for the 150 kHz switching frequency at this Vin/Vout/Iout combination
- **D1 — 1N5822 Schottky diode**: Catch (freewheeling) diode, rated for 3A forward current and 40V reverse voltage with low forward drop to minimize conduction losses
- **C1 — 100 µF / 25V electrolytic**: Input bulk/decoupling capacitor, placed close to the Vin pin
- **C2 — 220 µF / 25V low-ESR electrolytic**: Output filter capacitor, sized to keep output ripple low
- **ON/OFF control**: Tied to ground for always-on operation (regulator enabled by default)

### Key Design Calculations

For Vin = 12V, Vout = 5V, Iout(max) = 3A:

```
Duty Cycle:        D = Vout / Vin = 5 / 12 ≈ 0.417  (41.7%)
Inductor:          L = 68 µH  (per LM2596 datasheet selection chart for this Vin/Vout/Iout)
Output Capacitor:  220 µF, low-ESR  (per datasheet recommendation table)
Input Capacitor:   100 µF, standard electrolytic
Catch Diode:       1N5822 — must exceed Vin in reverse rating and Iout(max) in forward current
```

> Component values were taken directly from the LM2596 datasheet's component selection guide rather than calculated from first principles, in line with the manufacturer's recommended design procedure for this IC.

**Schematic file:** [`hardware/buck_converter.kicad_sch`](hardware/buck_converter.kicad_sch)

---

## PCB Layout

The board is a 2-layer design with the following layout practices applied:

- **Dedicated ground plane** on the bottom copper layer to provide a low-impedance return path
- **Short, wide traces** for the high-current switching loop (U1 SW pin → L1 → C2 → load → back to GND) to reduce loop inductance and EMI radiation
- **Input/output capacitors placed close** to their respective IC pins to minimize parasitic inductance
- **Thermal considerations**: adequate copper pour and via stitching near the regulator IC for heat dissipation
- **Net separation**: power and signal/feedback paths kept apart to avoid switching noise coupling into sensitive nets

**PCB layout file:** [`hardware/buck_converter.kicad_pcb`](hardware/buck_converter.kicad_pcb)

---

## Validation

Before generating fabrication outputs, the design was validated using KiCad's built-in checks:

| Check | Tool | Result |
|---|---|---|
| Electrical Rule Check (ERC) | KiCad Schematic Editor | No errors/warnings outstanding |
| Design Rule Check (DRC) | KiCad PCB Editor | No clearance, connectivity, or footprint violations |

Reports: [`validation/ERC_report.pdf`](validation/ERC_report.pdf) · [`validation/DRC_report.pdf`](validation/DRC_report.pdf)

---

## Repository Structure

```
buck-converter-pcb/
├── hardware/
│   ├── buck_converter.kicad_pro      # KiCad project file
│   ├── buck_converter.kicad_sch      # Schematic
│   └── buck_converter.kicad_pcb      # PCB layout
├── manufacturing/
│   ├── gerbers/                      # Gerber files (RS-274X)
│   ├── buck_converter.drl            # Excellon drill file
│   ├── BOM.csv                       # Bill of Materials
│   └── pick_and_place.csv            # Component placement data
├── validation/
│   ├── ERC_report.pdf
│   └── DRC_report.pdf
└── docs/
    └── Buck_Converter_Design_Report.docx
```

---

## Bill of Materials (Summary)

| Ref | Component | Value / Part Number | Package |
|---|---|---|---|
| U1 | Switching Regulator | LM2596S-5.0 | TO-263-5 |
| L1 | Power Inductor | 68 µH | Through-hole radial |
| D1 | Schottky Diode | 1N5822 | DO-201AD |
| C1 | Input Capacitor | 100 µF / 25V, electrolytic | Radial THT |
| C2 | Output Capacitor | 220 µF / 25V, low-ESR electrolytic | Radial THT |
| J1 | Input Connector | 2-pin terminal block | THT |
| J2 | Output Connector | 2-pin terminal block | THT |

Full BOM with manufacturer part numbers and quantities: [`manufacturing/BOM.csv`](manufacturing/BOM.csv)

---

## Tools & Skills Used

- **KiCad** — schematic capture, PCB layout, ERC/DRC, Gerber/BOM generation
- Datasheet-driven component selection (Texas Instruments LM2596 datasheet)
- PCB layout best practices for switching power supplies (grounding, loop area minimization, thermal relief)

---

## Possible Future Improvements

- Add an output voltage/current test point header for bench validation
- Move to a synchronous buck topology (e.g. with a MOSFET + driver) to improve efficiency at lower output currents
- Add reverse-polarity and overcurrent protection on the input
- Design a compact SMD variant of the same circuit for a smaller form factor

---

## License

This project is released under the [MIT License](LICENSE).

## Author

Add your name, GitHub profile link, and contact/portfolio link here.
