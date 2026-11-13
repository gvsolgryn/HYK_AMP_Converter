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

## 9. Decision log — 2026-11-12 (user-confirmed, reflected in `HYK_AMP_Converter.kicad_sch`)

Schematic reference mapping for the logical refs above: L1 → **FL1**; FH1 + fuse → **F1**; R_TERM R1, R_BIAS R2, R_REF1 R3, R_REF2 R4, R_OUT R5, R_HYS R6, R_SER_Y R7, R_SER_Z R8, R_DIFF R9, R_POL_YPLUS R10, R_POL_ZMINUS R11, R_POL_YMINUS R12, R_POL_ZPLUS R13, R_CMC_BYPASS1 R14, R_CMC_BYPASS2 R15, R_DE R16; C_AC C9, C_REF C10, C_EMI_* C11–C14. In the schematic, resistor **Value = electrical value**, **Description = logical name**.

| # | Item | Decision | Basis |
|---|---|---|---|
| 1 | FL1 CMC part | **TDK ACM2012-900-2P-T001** (standard type). H-type (ACM2012H, T00 obsolete / T06 current) not purchased | User decision; datasheet `references/datasheets/cmf_automotive_signal_acm2012_en.pdf` |
| 2 | FL1 pin mapping | Line 1 = pin 1↔4, Line 2 = pin 2↔3, no polarity. Y path enters pin 1 → SPDIF+ at pin 4; Z path enters pin 2 → SPDIF− at pin 3. R_DIFF across pins 3/4 side; bypass R14 = 1↔4, R15 = 2↔3 | TDK datasheet Circuit Diagram (user-verified image) |
| 3 | FL1 footprint | `ACM2012H-900-2P-T00:ACM20121022PT001` (pads 0.75×0.4 mm, gap 1.1 mm, pitch 0.8 mm = TDK recommended land pattern). The UL `ACM2012-900-2P-T001` footprint (pitch 1.0 mm) was **rejected** and unregistered | Footprint vs datasheet dimension check |
| 4 | F1 fuse + holder | **One symbol F1** with holder footprint `FUSE_3557-2:FUSE_3557-2`; fuse insert `0891002.NXS` recorded in the MPN field (must still be purchased). Wire pins 1_1+1_2 together and 2_1+2_2 together | User decision |
| 5 | 3.3 V rail name | KiCad power symbol `+3.3V` (logical `+3V3` in §7.4). TP3 Value `+3.3V_TP` is a display name only | User decision |
| 6 | C1 | Aluminum electrolytic, radial THT, polarized symbol `Device:C_Polarized` (+ to post-Schottky 12 V). Exact MPN / can size / footprint still OPEN (footprint currently a 0805 placeholder — must change before PCB) | §3 of this BOM |
| 7 | R_DE (R16) | **0 Ω to +3V3, FIT** (final). MAX3295 DE active-high, must not float; tZH ≤ 400 ns. Delayed enable stays undesigned; revisit only if power-up pop is observed in the car | MAX3293/5 datasheet |
| 8 | R_HYS (R6) | Footprint only, Value `TBD`, **DNP**, may be drawn OUT→IN+. **Never fit 0 Ω** (latches comparator). TLV3601 has internal hysteresis 1.5/3/5 mV (min/typ/max); external value to be calculated only if no-input chatter is observed | TLV3601-Q1 datasheet |
| 9 | C_EMI (C11–C14) | Value `TBD`, DNP | §5 of this BOM |
| 10 | DNP set | R6, R12, R13, R14, R15, C11–C14 | §4–§5 of this BOM |
| 11 | D1 SB360 | Symbol `Device:D` (1=K, 2=A) to match footprint `SB360:P2_DO-201` (pad 1 square + band = cathode). Earlier symbol had 1=A → would have reversed D1 on the PCB | Footprint inspection |
| 12 | Test points TP1–TP13 | `TestPoint:TestPoint_Pad_D1.5mm` (SMD pad, probe only) | User decision |
| 13 | U2/U3/U4 footprints | Hand-solder `-L` variants: `TPS7B8350QDCYRQ1:SOT_8350QDCYRQ1_TEX-L`, `TLV3601QDBVRQ1:SOT_DBVRQ1_TEX-L`, `MAX3295AUT-V-T:21-0058_U6CN-2_MXM-L` | AGENTS.md library rule |
| 14 | D2 placement (2026-11-13) | **D2 1.5KE22CA across `ACC_FUSED`–GND, i.e. after F1 and BEFORE D1.** C1/C2 stay post-Schottky on `VIN_PROTECTED`. Rev.A2 placed a unidirectional 1.5KE27A after the diode; Rev.1 changed to 22 V (clamp 30.6 V ≤ TSR 1-2450 max input 36 V) and bidirectional, which only matters ahead of D1: negative transients are clamped to about −30 V instead of reaching SB360 (V_RRM 60 V), C1 and U1, and reverse battery (−12 V < V_BR 20.9 V) does not conduct, so D1 still blocks. Known limit: 24 V jump start conducts D2 and opens F1 (same at either position). Not a guarantee of ISO 7637/16750 compliance | User decision; §0 order "fuse, shunt TVS, series Schottky"; Littelfuse 1.5KE and TRACO TSR 1 datasheets |
| 15 | J2/J3 pin order (2026-11-13) | **Name order, GND always last.** J3 (B2PS-VH): `1 = ACC_IN`, `2 = GND`. J2 (XH 3-pin): `1 = SPDIF+`, `2 = SPDIF−`, `3 = GND`. Matches the label order used in every doc and the OEM AMP pin names (`SPDIF+ / SPDIF− / SPDIF GND`), and the earlier HANDOFF definition. The square THT pad only marks pin 1; it does not imply GND. Harness: GND wire black, ACC red, SPDIF± twisted pair | User decision (self-made harness, memorability) |
| 16 | Enclosure (2026-11-13) | **CaseForU (케이스포유) RT-75, RT-7511: 75 × 30.9 × 110 mm** (body 74.9 × 30.5 × 107 extruded AL6061; front/rear panels 75 × 30.9 × 1.5 AL5052, screw holes 67.2 × 22.8), internal PCB slots: 1.70 gap for 1.6 mm board, 69.7 mm groove-to-groove, PCB bottom 4.5 mm above inner floor, inner height 25.5 mm → about 19.4 mm above the PCB top. **PCB outline 69.5 × 107.0 mm** (CaseForU PCB reference 70 × 107; width reduced 0.2 below the 69.7 groove width). Made in Korea (24,200 KRW). Replaces Hammond 1455C1201BK (23 mm outer height too low for the BNC and fuse holder). RT-7590 (same profile, body 87 mm) allowed if the layout fits. Rear panel (recommended, not final): one grommet/PG7 gland for the harness, J2/J3 stay inside on the PCB. Mounting (open): velcro/tape + cable tie or L-bracket. Panel machining, laser marking and silk print after PCB layout and a fit check. Drawings kept locally only (`references/case_file/`, git-ignored: CaseForU no-redistribution notice, public repo) | User decision; CaseForU RT-7511/RT-7590 drawings (PDF/DWG/STEP, 2011-07-27), download: https://www.case4u.co.kr/board/free/read.html?no=1060&board_no=7 |
| 17 | J1 BNC mounting (2026-11-13) | **KH-BNC75-3511 sits on the PCB front edge; its 1/2-28UNEF thread passes through the 1.5 mm front panel and is fixed with the supplied washer + nut.** No pigtail. Cable strain goes to the panel, not to the solder joints. Panel hole: Ø12.8 D-shape (to re-read from the datasheet when making the footprint), centre ≈ 19.8 mm above the outside bottom (PCB top ≈ 8.8 + BNC axis ≈ 11, estimate). Assembly: panel + nut on the BNC first, then slide PCB + front panel into the body. A dedicated J1 footprint must replace the temporary Amphenol 50 Ω footprint. Grounding policy (case bonded to GND via the thread vs insulated) and a J1 shield–GND 0 Ω option are still OPEN | User proposal, agreed; KH-BNC75-3511 datasheet |
| 18 | PCB length (2026-11-13, supersedes the 107.0 mm outline in #16) | **PCB outline 69.5 × 100.0 mm.** JLCPCB charges its special prototype price only for a single board ≤ 102 × 102 mm; 107 mm would fall back to engineering fee + board fee. The front edge stays flush with the body front end (J1 thread through the front panel, #17); the rear edge ends about 7 mm short of the rear panel. The board is located by the slots and the J1 panel nut, so the rear gap is free space for the harness plugs. 100 mm leaves 2 mm margin to the 102 mm limit | User decision; JLCPCB help "In what cases will there be charged extra?" §7 Board Fee (updated 2026-11-10) |
| 19 | J1 / D1 footprints (2026-11-13) | **J1: new project footprint `KH-BNC75-3511:KH-BNC75-3511`** from Kinghelm drawing KH-801-0039 rev B: pin 1 centre contact Ø0.90 hole (origin), pin 2 GND lead Ø0.90 at +2.5 mm across the axis, 2 × Ø2.00 fixing legs 10.1 apart (pad 2, GND) located 5.05 mm toward the front of the signal leads; flange (panel stop) 12.55 mm in front of pin 1 = PCB front edge; thread + bayonet Ø9.5 × 21.0 beyond the edge; body 14.7 wide. **GND-lead side verified against the drawing's rear view (GND left of the axis), front view (GND right of the axis) and PCB view (fixing-hole centre line = centre contact, GND 2.5 mm beside it): with the connector front pointing to −x, GND is at +y** — consistent in all three views. **D1: `SB360:P2_DO-201` drill 1.3 → 1.5 mm, pad 2.6 → 2.8 mm** (DO-201AD leads 1.20–1.30 mm) | KH-BNC75-3511 drawing (front, rear, PCB views); footprint review |
| 20 | C1 part and D1 sourcing (2026-11-13) | **C1 = Panasonic EEU-FR1H220** (FR series, 22 µF / 50 V, 105 °C, Ø5 × 11 mm, lead pitch 2.0 mm; DigiKey 2433586, buy through DeviceMart as DigiKey reseller). Footprint `Capacitor_THT:CP_Radial_D5.0mm_P2.00mm` (pad 1 = +, on `VIN_PROTECTED`). Alternatives with the same footprint: Rubycon 50YXJ22MCE5X11. **D1 SB360 is a multi-source generic part** (Diotec, Diodes Inc., onsemi, Vishay …); any 60 V / 3 A Schottky in JEDEC **DO-201AD** fits the footprint (lead Ø1.20–1.30, body Ø5.3 × 9.5 max per Diodes Inc. DS23023 in `references/datasheets/SB320-SB360.PDF`). Keep the datasheet of the maker actually bought | User decision; Diodes Inc. DS23023 |
| 21 | J2 footprint drill (2026-11-13) | **J2 uses project copy `JST_XH_S3B-XH-A:JST_XH_S3B-XH-A_1x03_P2.50mm_Horizontal_Drill1.0`** — identical to the KiCad library footprint except drill 0.95 → 1.0 mm (pads 1.7 × 1.95 unchanged). JST XH side-entry layout gives Ø0.9 +0.1/0 and advises a larger hole on glass-fibre PCBs; the 0.64 mm square pin has a 0.9 mm diagonal | User decision; JST eXH catalogue p.2 |
| 22 | BOM fields (2026-11-13, refines #4) | Purchase fields are `Manufacturer` + `MPN` on every symbol that has a fixed part. **F1**: Value `2A LP Mini`, `Manufacturer` Keystone Electronics, `MPN` 3557-2 (holder), `Fuse_Manufacturer` Littelfuse, `Fuse_MPN` 0891002.NXS (insert); SnapEDA import fields removed. **FL1**: `Manufacturer` TDK, `MPN` ACM2012-900-2P-T001, datasheet = TDK ACM2012 catalogue; Mouser/Arrow fields that pointed to the obsolete H-type T00 removed. **C5/C6** Value restored to `10µF / 16V X7R` | User request |
| 23 | J2 pin order (2026-11-13, supersedes the J2 part of #15) | **J2 (XH 3-pin): `1 = SPDIF−`, `2 = SPDIF+`, `3 = GND`.** With J2's entry facing the rear panel its pins run 3 (top) → 1 (bottom) on the board, while FL1 delivers SPDIF+ (pin 4) on top and SPDIF− (pin 3) below; this order lets both lines run straight into J2 on Top with no crossing, no via and no trace between pads, keeping the Bottom GND plane intact. GND stays last. Seen from the rear (harness side) left to right: SPDIF−, SPDIF+, GND. Harness: SPDIF± twisted pair in the user's CAN-style colours (exact colours to be recorded), GND black. J3 unchanged (`1 = ACC_IN` red, `2 = GND` black). Schematic: only J2.1/J2.2 swapped, netlist verified | User decision |
| 24 | PCB layout (2026-11-13) | Board 69.5 × 100.0 mm, R1 corners, origin (50, 30.5); 2-layer 1.6 mm. Rules (JLCPCB-safe): clearance/track/annular 0.15, via 0.6/0.3, hole-to-hole 0.5, **copper-to-edge 2.0 mm** (1.5 mm of each long edge sits in the aluminium groove, STEP-measured). Net classes: Default 0.25, Power12 0.8 (ACC_IN/ACC_FUSED/VIN_PROTECTED, clearance 0.3, via 0.8/0.4), Rail 0.5 (+5V/+3.3V), GND 0.5, SPDIF_OUT 0.3 (Y/Z_RAW, PAIR_P/N, SPDIF_P/N, R10/R11 nets). Top + Bottom GND pours, thermal relief 0.5/0.5, GND vias next to every SMD GND pad + 10 mm grid. EMI review: C7/C8 at 1.9/2.8 mm from U3/U4 VCC, C2 5 mm from U1 Vin, no signal or rail on B.Cu (Bottom plane unbroken), Z-path link routed between R12 (DNP) pads, no tracks under the TSR body, no acute/right corners. DNP parts carry a "DNP" silk mark. Case reference footprint `Case_RT75:CASE_RT7511` (MECH1, locked, board-only). DRC 0, schematic parity 0 | User routing + agent EMI review; RT-7511 STEP |

Still OPEN after this log: case grounding policy and J1 shield–GND 0 Ω option (#17), J2 exact XH part (footprint genuine JST `S3B-XH-A` 2.50 mm), enclosure mounting method and rear-panel harness exit, optional 0 Ω pads (C9→IN+ series, TSR input inductor), MLCC/resistor MPNs, `Manufacturer`/`MPN` fields still empty on U1–U4, D1, D2, J1–J3. F1 holder drill was widened to 1.8 mm (blade 1.57 × 0.40, diagonal 1.62 > Keystone min hole 1.60). PCB handoff: `docs/DUDU7_i40_SPDIF_TX_Rev1_PCB_Mechanical_Handoff_20261113.md`.
