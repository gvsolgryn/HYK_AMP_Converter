# DUDU7 / FYT7862 → Hyundai i40 OEM Amplifier S/PDIF TX Rev.1
## Component BOM and KiCad Agent Handoff — 2026-11-11

> **Document purpose:** This is the agreed component-selection baseline for a LOCAL KiCad / MCP coding agent. Reuse the listed MPNs and electrical values; do **not** independently substitute parts or silently make optional/DNP parts mandatory. This is a **design BOM**, not yet a procurement-ready BOM or validated production schematic.

## 0. Project and constraints

- **Source:** DUDU7 (or FYT7862 test head unit), coaxial S/PDIF RCA output; user plans BNC PCB input via an RCA-to-BNC cable/adapter.
- **Destination:** Hyundai i40 OEM external amplifier **96370-3Z101**, existing speakers retained. Amplifier input is differential `SPDIF+`, `SPDIF-`, and reference `GND`.
- **Signal processing:** 75-ohm coax termination → AC-coupled TI **TLV3601-Q1** comparator → ADI **MAX3295** differential line driver → OEM-derived passive network and connector.
- **Power:** vehicle **ACC 12 V**, fuse, shunt TVS, series reverse-polarity Schottky → TRACO switching module 5 V → TI LDO 3.3 V.
- **Assembly:** small PCB, hand solderable, normal SMD passives **0805 imperial = 2012 metric**, except specifically listed THT/IC/CMC packages. Parts in low quantities; DeviceMart / Eleparts preferred (including DigiKey procurement through local reseller).
- **Agreed scope:** no added OVP stage and no added external input LC filter for Rev.1. Preserve selectable jumpers and DNP EMC pads.
- **Important:** This source documents earlier design decisions, not a claim that all signal integrity, datasheet parameters, sourcing and PCB footprints have already been verified.

## 1. Selected active components and protection

| Ref proposal | Qty | Function | Manufacturer | **Exact part / order number** | Package / mounting | Selection status |
|---|---:|---|---|---|---|---|
| U1 | 1 | 12V to 5V switching regulator | TRACO POWER | **TSR 1-2450** | SIP-3, THT | SELECTED / FINAL |
| U2 | 1 | 5V to 3.3V LDO, 150mA class | Texas Instruments | **TPS7B8333QDCYRQ1** | **SOT-223 (DCY), 4 physical leads/tabs; verify pin assignment** | SELECTED / FINAL |
| U3 | 1 | High-speed signal comparator | Texas Instruments | **TLV3601QDBVRQ1** | SOT-23-5 | SELECTED / FINAL |
| U4 | 1 | 3.3V differential driver | Analog Devices / Maxim | **MAX3295AUT+T** | SOT-23-6 | SELECTED / FINAL |
| D1 | 1 | Reverse-polarity blocking, series | Diotec Semiconductor | **SB360** (3 A / 60 V Schottky) | Axial THT; confirm purchased lead spacing | SELECTED / FINAL |
| D2 | 1 | Input transient suppression, across supply and GND | Littelfuse | **1.5KE22CA** (bidirectional TVS) | Axial THT | SELECTED / FINAL |
| F1 | 1 | 2A vehicle blade fuse | Littelfuse | **0891002.NXS** (LP Mini, 2 A, 58V DC) | Insert into fuseholder | SELECTED / FINAL |
| FH1 | 1 | PCB blade fuseholder | Keystone | **3557-2** | THT | SELECTED / FINAL |
| L1 | 1 | Differential output common-mode choke | TDK | **ACM2012-900-2P-T001** | SMD, 2012-size CMC; verify land pattern | **CANDIDATE**, not proven equivalent to OEM F400 |

**KiCad rule:** Never infer pad/pin numbers from this table alone. Match symbol pin numbers to the **exact datasheet and actual footprint** for the specified MPN before ERC and fabrication.

### Verified pin mapping of selected MAX3295 (U4)

| IC pin | Function | Connection |
|---:|---|---|
| 1 | DI | Signal from TLV3601 output via R_OUT |
| 2 | VCC | +3V3 and local C8 100nF bypass to GND |
| 3 | DE | +3V3 via R_DE 0Ω (Rev.1 default) |
| 4 | Z | OEM-corresponding SPDIF- path via 120Ω series resistor |
| 5 | GND | GND |
| 6 | Y | OEM-corresponding SPDIF+ path via 120Ω series resistor |

**Verified TRACO TSR 1-2450 module pinout:** pin 1 = +Vin, pin 2 = GND, pin 3 = +Vout. Independently check regulator body orientation and footprint numbering before PCB placement.

## 2. Connectors and mating parts

| Ref | Qty | Use | Selected MPN / definition | Mount | Status / caution |
|---|---:|---|---|---|---|
| J1 | 1 | 75Ω S/PDIF coax input | **Kinghelm KH-BNC75-3511** | Right-angle THT | SELECTED; check actual pin-to-footprint mapping |
| J2 | 1 | SPDIF+, SPDIF-, GND output | 3-way **XH-family right-angle THT** | Right-angle THT | **EXACT MPN OPEN**; genuine JST XH is 2.50mm pitch, generic clones may be 2.54mm. Do not assign a final footprint before the bought part is identified |
| J3 | 1 | 12V ACC and GND | **JST B2PS-VH** | Right-angle THT, 3.96mm pitch | SELECTED |
| J3 housing | 1 | Mating receptacle | **JST VHR-2N** | Cable | SELECTED |
| J3 crimp | 2 | Mating contacts | **JST SVH-21T-P1.1** | Cable crimp | SELECTED; verify wire conductor and insulation outside diameters |

Tentative harness: 22 AWG TXL for power; twisted pair around 24 AWG for SPDIF+/- plus signal GND. Actual connectors and enclosure dimensions must govern final mounting and cable clearance.

## 3. Power-stage capacitors (selected values; vendor MPNs still OPEN)

| Ref | Qty | Value | Rated voltage | Technology | Package | Net / purpose | Initial population |
|---|---:|---|---|---|---|---|---|
| C1 | 1 | 22µF | 50V | Aluminum electrolytic | THT, exact can diameter OPEN | post-Schottky 12V to GND | FIT |
| C2 | 1 | 100nF | 50V | X7R MLCC | 0805 | post-Schottky 12V to GND | FIT |
| C3 | 1 | 10µF | 16V | X7R MLCC | 0805 | U1 5V output to GND | FIT |
| C4 | 1 | 1µF | 16V | X7R MLCC | 0805 | U2 5V input to GND | FIT |
| C5, C6 | 2 | 10µF each | 16V | X7R MLCC | 0805 | U2 3.3V output to GND, parallel | FIT |
| C7 | 1 | 100nF | 16V | X7R MLCC | 0805 | U3 VCC near IC | FIT |
| C8 | 1 | 100nF | 16V | X7R MLCC | 0805 | U4 VCC near IC | FIT |

**Capacitance check:** Especially for 10µF / 0805 MLCC, verify actual effective capacitance under DC bias and LDO minimum effective output capacitance from TI datasheet; package/voltage/value alone do not establish stability.

## 4. TLV3601 coax receive / comparator front-end

| Logical ref | Qty | Value | Specs / package | Connection | Initial population |
|---|---:|---|---|---|---|
| R_TERM | 1 | 75Ω | 1%, 0805 | coax center node to GND, at PCB input | FIT |
| C_AC | 1 | 10nF | 50V X7R, 0805 | series: coax node → comparator IN+ node | FIT |
| R_BIAS | 1 | 2.2kΩ | 1%, 0805 | comparator IN+ node → VREF | FIT |
| R_REF1 | 1 | 2.2kΩ | 1%, 0805 | +3V3 → VREF | FIT |
| R_REF2 | 1 | 2.2kΩ | 1%, 0805 | VREF → GND | FIT |
| C_REF | 1 | 1µF | 16V X7R, 0805 | VREF → GND | FIT |
| R_OUT | 1 | **0Ω default**, 22Ω optional replacement | 0805 | U3 OUT → U4 DI | FIT 0Ω |
| R_HYS | 1 footprint | **TBD** | 0805 | Optional external hysteresis network, **not yet electrically defined** | DNP / DO NOT INVENT WIRING |

**Input path / logical net sketch:**

```text
J1.COAX_CENTER ────┬── R_TERM 75Ω ─── GND
                   │
                   └── C_AC 10nF ───┬──────── U3.IN+
                                    │
                               R_BIAS 2.2kΩ
                                    │
                                   VREF

+3V3 ── R_REF1 2.2kΩ ──┬── R_REF2 2.2kΩ ── GND
                        ├── C_REF 1µF ───── GND
                        ├── U3.IN−
                        └── R_BIAS (other end)

U3.OUT ── R_OUT 0Ω ── U4.DI
```

`VREF` is nominally 1.65V. A + B approach means *AC coupling AND mid-supply bias*, not two alternative build modes. Keep high-speed comparator input layout short. Do not introduce input-presence gating or default extra threshold offset (the user explicitly chose to leave input-absent comparator behavior unmodified).

**U3 TLV3601QDBVRQ1 pin assignment from earlier design** (MUST CONFIRM against TI datasheet before schematic symbol lock): Pin 1 OUT, pin 2 VEE/GND, pin 3 IN+, pin 4 IN-, pin 5 VCC (+3V3).

## 5. MAX3295 output network — copy user reverse-engineered OEM topology

The user measured the original AVN board: 120Ω from each driver output, OEM choke F400, and a **56Ω differential shunt on the amplifier-facing side** of the choke. The Rev.1 layout should preserve this topology.

| Logical ref | Qty / footprints | Value | Package | Location | Initial population |
|---|---:|---|---|---|---|
| R_SER_Y | 1 | 120Ω, 1% | 0805 | U4.Y (pin 6) → polarity-selector input Y | FIT |
| R_SER_Z | 1 | 120Ω, 1% | 0805 | U4.Z (pin 4) → polarity-selector input Z | FIT |
| R_DIFF | 1 | 56Ω, 1% | 0805 | SPDIF+ ↔ SPDIF- **after CMC** | FIT |
| R_POL_YPLUS | 1 | 0Ω | 0805 | Y-path → CMC input channel 1 | FIT |
| R_POL_ZMINUS | 1 | 0Ω | 0805 | Z-path → CMC input channel 2 | FIT |
| R_POL_YMINUS | 1 | 0Ω | 0805 | Y-path → CMC input channel 2 | DNP |
| R_POL_ZPLUS | 1 | 0Ω | 0805 | Z-path → CMC input channel 1 | DNP |
| R_CMC_BYPASS1/2 | 2 footprints | 0Ω each | 0805 | Each across its OWN CMC signal channel, from respective input to respective output | DNP (when L1 installed) |
| R_DE | 1 | 0Ω | 0805 | +3V3 → U4.DE (pin 3) | FIT |
| C_EMI_PRE | 1 footprint | TBD | 0805 | Differential shunt between two lines **before CMC**; OEM C593 equivalent | DNP |
| C_EMI_POST | 1 footprint | TBD | 0805 | Differential shunt between SPDIF+ and SPDIF- **after CMC**; OEM C433 equivalent | DNP |
| C_EMI_PGND | 1 footprint | TBD | 0805 | SPDIF+ → GND; OEM C430 equivalent | DNP |
| C_EMI_NGND | 1 footprint | TBD | 0805 | SPDIF- → GND; OEM C431 equivalent | DNP |

**Exactly two polarity selector links must be populated.** Normal configuration: `Y→PLUS` and `Z→MINUS`; inverse links unpopulated. Reversed configuration: populate the two cross-links instead. Do **not** populate all four 0Ω links, as that would short differential outputs through the selector.

**CMC selection:** default FIT L1. When deliberately removing L1, FIT two 0Ω CMC bypass links instead. Do NOT treat bypass resistors as default parallel links. Verify source-to-destination channels on the 4-pad choke footprint: do not accidentally tie SPDIF+ to SPDIF-.

**Crucial DNP distinction:** All four EMC capacitors are shunt capacitors (line-to-line or line-to-GND), so leaving them DNP does **not** break either signal trace. The CMC, its bypass and the selected polarity jumper links ARE in the through-signal paths: an incorrect population can open or short the paths.

Simplified topological diagram (**not** a footprint-level physical wiring drawing):

```text
U4 MAX3295 pin 6 Y ── 120Ω ──[NORMAL/CROSS 0Ω SELECTOR]── CMC CH1 ──┬── SPDIF+ ── J2
                                                                       │
                                                                      56Ω
                                                                       │
U4 MAX3295 pin 4 Z ── 120Ω ──[NORMAL/CROSS 0Ω SELECTOR]── CMC CH2 ──┴── SPDIF- ── J2

                  Each CMC channel: optional 0Ω bypass, DEFAULT DNP
            DNP capacitors: differential pre-CMC; differential post-CMC;
                            each post-CMC signal line to GND.
```

## 6. Deferred / unresolved

1. **U4 DE delayed-enable alternative:** Default is permanently high through 0Ω; the delayed-enable network has **NOT** been designed/validated. Do not place an arbitrary passive RC in the live DE path. An optional schematic note or clearly isolated DNP placeholder is acceptable.
2. **R_HYS:** Comparator external-hysteresis network has not been engineered. Leave DNP/unconnected until calculation; do not add arbitrary positive feedback.
3. **J2:** Select the exact 3-pin right-angle XH-family part BEFORE footprint finalization, with correct 2.50 vs 2.54mm pitch.
4. **C1:** Pick an actual THT radial electrolytic MPN, diameter, lead spacing, height. All MLCC and resistor supplier MPNs still need selection.
5. **L1:** TDK ACM2012-900-2P-T001 is a CANDIDATE; verify it against signal timing, winding arrangement and OEM F400 if possible.
6. **Enclosure** and exact PCB dimensions are not decided; aim for minimal size that allows connectors, fusing and placement under driver seat near OEM amp.
7. **ESD/EMC, power and amplitude:** Design is not yet electrically validated. Bench testing on FYT7862 found an approximate 80Ω temporary load gave ~1.25Vpk-pk and ~0.916V HIGH–LOW amplitude on an oscilloscope; this is **not** a controlled 75Ω coax compliance test and should not trigger silent redesign.
8. **Test points:** Need footprints on 12V (fused/protected), 5V, 3.3V, GND, S/PDIF coax input, U3 IN+, U3 OUT, U4 DI, DE, Y, Z, SPDIF+, SPDIF-. Keep probes accessible.
9. **Power sequencing and thermal:** Inspect TI regulator package orientation / pinout, heat and output-capacitance requirements, DC/DC module input/output recommendations, fuseholder footprint and physical clearances before layout.

## 7. Implementation requirements for the local agent

1. Create a real KiCad project and **editable schematic** (`.kicad_pro`, `.kicad_sch`); not only a PDF, drawing or ASCII rendering.
2. Preserve exact selected active-component MPNs. Mark unresolved choices visibly instead of guessing or silently changing them.
3. Use only verified library symbols/footprints and record missing custom footprints/symbols in a TODO table.
4. Use consistent net labels: `ACC_IN`, `ACC_FUSED`, `VIN_PROTECTED`, `+5V`, `+3V3`, `GND`, `COAX_SPDIF`, `VREF`, `SPDIF_AC`, `CMP_OUT`, `DI`, `DE`, `Y_RAW`, `Z_RAW`, `SPDIF_P`, `SPDIF_N`.
5. Protect normal polarity and CMC links against accidental conflicting population. In schematic notes, list the normal and reverse build states.
6. Mark all four EMC capacitors and two CMC bypass jumpers explicitly **DNP**. Do not remove footprints. Do not insert DNP components in series with signals.
7. The output resistor 56Ω is across the two **post-CMC** signal lines and is FIT, unlike DNP shunt capacitors.
8. Run KiCad ERC, and separately manually verify supply pin mapping, comparator polarity, MAX3295 pin numbers, cross-connect topology, TVS shunt placement and power path.
9. Export an updated BOM with MPN, value, quantity, footprint, populate/DNP status and purchase gaps. Do not label the entire BOM procurement-complete.
10. Ask for a design decision only where it blocks safe electrical interpretation; otherwise create labeled placeholders and continue.

## 8. Manufacturer references (start here; confirm revisions)

- TI TLV3601-Q1 product/datasheet: https://www.ti.com/product/TLV3601-Q1
- TI TPS7B83-Q1 product/datasheet: https://www.ti.com/product/TPS7B83-Q1
- ADI MAX3293/MAX3295 datasheet: https://www.analog.com/media/en/technical-documentation/data-sheets/max3293-max3295.pdf
- TRACO POWER TSR 1 series: https://www.tracopower.com/series/tsr-1
- Littelfuse, Diotec, JST, Keystone, Kinghelm, TDK: use exact MPN above to obtain mechanical and electrical manufacturer drawings prior to footprint lock.

**Source of agreed design choices:** Iterative user/assistant project discussion through 2026-11-11. This file is intended to make the existing decisions recoverable by an independent local agent.
