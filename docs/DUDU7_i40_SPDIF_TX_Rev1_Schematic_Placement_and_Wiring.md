# DUDU7 / FYT7862 → Hyundai i40 OEM AMP S/PDIF TX Rev.1
## KiCad Symbol Placement and Pin Wiring Guide

- 기준일: 2026-11-11
- 상위 기준 문서: `docs/DUDU7_i40_SPDIF_TX_Rev1_BOM_for_KiCad_Agent.md`
- 용도: Rev.1 회로도 심볼 배치, 참조 번호 통일, 핀 연결 작업
- 상태: **회로도 작업 가이드이며 제조 승인 문서가 아님**

이 문서는 Rev.1의 logical reference에 실제 KiCad 참조 번호를 배정하고, 회로도 배선을 ASCII로 정리한다. 선정된 MPN과 FIT/DNP 상태를 임의로 바꾸지 않는다. 커넥터와 CMC의 물리 패드 번호는 실제 구매 부품과 제조사 도면으로 확인하기 전까지 확정하지 않는다.

> **중요:** 기존 Rev.A2 회로도와 U1~U4의 기능이 다르다. 기존 부품을 그대로 두고 Rev.1 부품을 추가하지 말고, 아래 참조 번호로 먼저 통일한다.

| 기능 | Rev.A2 | Rev.1 |
|---|---|---|
| 5V DC/DC | U4 LMR16006YQ5 | **U1 TSR 1-2450** |
| 3.3V LDO | U1 TPS7B8333 | **U2 TPS7B8333** |
| Comparator | U2 TLV3601 | **U3 TLV3601** |
| 차동 드라이버 | U3 MAX3295 | **U4 MAX3295** |

---

## 1. 배치할 심볼과 참조 번호

### 1.1 IC, 보호부품, CMC, 커넥터

| Ref | 배치할 심볼 / 값 | 실장 | 비고 |
|---|---|---|---|
| **U1** | `TSR 1-2450` | FIT | 12V→5V 모듈, SIP-3 |
| **U2** | `TPS7B8333QDCYRQ1` | FIT | 5V→3.3V LDO, SOT-223 |
| **U3** | `TLV3601QDBVRQ1` | FIT | Comparator, SOT-23-5 |
| **U4** | `MAX3295AUT+T` | FIT | 차동 드라이버, SOT-23-6 |
| **D1** | `SB360` | FIT | 직렬 역극성 보호 Schottky |
| **D2** | `1.5KE22CA` | FIT | 양방향 TVS, 전원–GND 병렬 |
| **F1** | `0891002.NXS`, 2A | FIT | LP Mini 블레이드 퓨즈 |
| **L1** | `ACM2012-900-2P-T001` | FIT | 2채널 CMC, 후보 부품 |
| **J1** | `KH-BNC75-3511` | FIT | 75Ω BNC 입력 |
| **J2** | 3핀 XH 계열 | FIT | `SPDIF+ / SPDIF− / GND`, 정확한 MPN 미정 |
| **J3** | `JST B2PS-VH` | FIT | `ACC 12V / GND` 전원 입력 |

`FH1 = Keystone 3557-2`는 F1의 PCB 홀더다. 회로도에서 퓨즈와 홀더를 각각 직렬로 배선하지 말고 **F1 하나로 표현하고 FH1 홀더 풋프린트를 연결**한다. `VHR-2N` 하우징과 `SVH-21T-P1.1` 크림프는 회로도 심볼이 아니다.

### 1.2 커패시터

일반 MLCC는 Rev.1 기준으로 **0805 imperial = 2012 metric**이다.

| Ref | 값 | 실장 | 연결 위치 |
|---|---|---|---|
| **C1** | 22µF / 50V 전해 | FIT | D1 이후 `VIN_PROTECTED ↔ GND` |
| **C2** | 100nF / 50V X7R | FIT | `VIN_PROTECTED ↔ GND` |
| **C3** | 10µF / 16V X7R | FIT | U1 5V 출력 `+5V ↔ GND` |
| **C4** | 1µF / 16V X7R | FIT | U2 입력 `+5V ↔ GND` |
| **C5** | 10µF / 16V X7R | FIT | U2 출력 `+3V3 ↔ GND` |
| **C6** | 10µF / 16V X7R | FIT | C5와 병렬, `+3V3 ↔ GND` |
| **C7** | 100nF / 16V X7R | FIT | U3 VCC 국소 디커플링 |
| **C8** | 100nF / 16V X7R | FIT | U4 VCC 국소 디커플링 |
| **C9** | 10nF / 50V X7R | FIT | 입력 AC coupling, 문서의 `C_AC` |
| **C10** | 1µF / 16V X7R | FIT | `VREF ↔ GND`, 문서의 `C_REF` |
| **C11** | TBD | **DNP** | Pre-CMC 차동선 사이, `C_EMI_PRE` |
| **C12** | TBD | **DNP** | Post-CMC `SPDIF_P ↔ SPDIF_N`, `C_EMI_POST` |
| **C13** | TBD | **DNP** | `SPDIF_P ↔ GND`, `C_EMI_PGND` |
| **C14** | TBD | **DNP** | `SPDIF_N ↔ GND`, `C_EMI_NGND` |

### 1.3 입력부 저항

| Ref | Rev.1 logical ref | 값 | 실장 | 연결 |
|---|---|---|---|---|
| **R1** | `R_TERM` | 75Ω / 1% | FIT | `COAX_SPDIF ↔ GND` |
| **R2** | `R_BIAS` | 2.2kΩ / 1% | FIT | `SPDIF_AC ↔ VREF` |
| **R3** | `R_REF1` | 2.2kΩ / 1% | FIT | `+3V3 ↔ VREF` |
| **R4** | `R_REF2` | 2.2kΩ / 1% | FIT | `VREF ↔ GND` |
| **R5** | `R_OUT` | 0Ω | FIT | U3 OUT → U4 DI, 필요 시 22Ω으로 변경 |
| **R6** | `R_HYS` | TBD | **DNP** | 미설계 히스테리시스 자리, **임의 배선 금지** |

R6은 자리만 남길 경우 **격리된 DNP placeholder**로 둔다. 양단을 임의의 신호에 연결하지 않으며, ERC 처리를 위해 no-connect 표시가 필요할 수 있다.

### 1.4 MAX3295 출력부 저항

| Ref | Rev.1 logical ref | 값 | 실장 | 연결 |
|---|---|---|---|---|
| **R7** | `R_SER_Y` | 120Ω / 1% | FIT | U4 `Y(6)` → 극성 선택기 |
| **R8** | `R_SER_Z` | 120Ω / 1% | FIT | U4 `Z(4)` → 극성 선택기 |
| **R9** | `R_DIFF` | 56Ω / 1% | FIT | CMC 뒤 `SPDIF_P ↔ SPDIF_N` |
| **R10** | `R_POL_YPLUS` | 0Ω | **FIT** | Y 경로 → CMC 채널1 |
| **R11** | `R_POL_ZMINUS` | 0Ω | **FIT** | Z 경로 → CMC 채널2 |
| **R12** | `R_POL_YMINUS` | 0Ω | **DNP** | Y 경로 → CMC 채널2 |
| **R13** | `R_POL_ZPLUS` | 0Ω | **DNP** | Z 경로 → CMC 채널1 |
| **R14** | `R_CMC_BYPASS1` | 0Ω | **DNP** | CMC 채널1 입력↔출력 우회 |
| **R15** | `R_CMC_BYPASS2` | 0Ω | **DNP** | CMC 채널2 입력↔출력 우회 |
| **R16** | `R_DE` | 0Ω | FIT | `+3V3 → U4 DE(3)` |

기본 조합은 **R10+R11만 실장**이다. R12/R13까지 함께 실장하면 차동 출력이 단락될 수 있다. R14/R15는 L1을 제거할 때만 대체 실장하는 부품이다.

### 1.5 테스트포인트

| Ref | Net | 측정 위치 |
|---|---|---|
| **TP1** | `VIN_PROTECTED` | 퓨즈·보호 이후 12V |
| **TP2** | `+5V` | U1 5V 출력 |
| **TP3** | `+3V3` | U2 3.3V 출력 |
| **TP4** | `GND` | 기준 접지 |
| **TP5** | `COAX_SPDIF` | BNC 동축 입력 |
| **TP6** | `SPDIF_AC` | U3 IN+ |
| **TP7** | `CMP_OUT` | U3 OUT |
| **TP8** | `DI` | U4 DI |
| **TP9** | `DE` | U4 DE |
| **TP10** | `Y_RAW` | U4 Y |
| **TP11** | `Z_RAW` | U4 Z |
| **TP12** | `SPDIF_P` | 최종 SPDIF+ |
| **TP13** | `SPDIF_N` | 최종 SPDIF− |

### 1.6 부품 번호가 없는 회로도 심볼

- 전원 심볼: `+5V`, `+3V3`, `GND`
- Net 라벨: `ACC_IN`, `ACC_FUSED`, `VIN_PROTECTED`, `COAX_SPDIF`, `SPDIF_AC`, `VREF`, `CMP_OUT`, `DI`, `DE`, `Y_RAW`, `Z_RAW`, `SPDIF_P`, `SPDIF_N`
- CMC 입력 측 보조 라벨: `PAIR_P`, `PAIR_N`
- `PWR_FLAG`: ERC 전원 공급 오류가 나오는 위치에만 추가
- DNP 및 점퍼 조합을 설명하는 텍스트 노트

### 1.7 배치 수량

| 분류 | 수량 |
|---|---:|
| IC·다이오드·퓨즈·CMC·커넥터 | 11 |
| 커패시터 C1~C14 | 14 |
| 저항 R1~R16 | 16 |
| 테스트포인트 TP1~TP13 | 13 |
| **총 회로도 배치 심볼** | **54개** |

DNP는 **R6, R12~R15, C11~C14**다.

---

## 2. ASCII 핀 연결 가이드

### 2.1 공통 표기

- `A`: 다이오드 Anode
- `K`: 다이오드 Cathode
- `FIT`: 초기 실장
- `DNP`: 풋프린트는 유지하되 초기 미실장
- J1~J3의 `CENTER`, `SHIELD`, `ACC`, `GND`, `SPDIF+` 등은 **논리 핀 이름**이다. 실제 커넥터의 물리 핀 번호는 제조사 도면과 풋프린트로 별도 확인한다.
- L1의 `CH1`, `CH2`도 논리 권선 채널이다. 물리 패드 번호를 이 그림만으로 추정하지 않는다.

### 2.2 ACC 12V 입력, 보호, 5V 모듈

```text
                                      D1 SB360
J3 ACC ── F1 2A ── ACC_FUSED ── A ──|>|── K ──●── VIN_PROTECTED
                                               │
J3 GND ────────────────────────────────────────┼────────────── GND
                                               │
                    ┌──────────────────────────┼──────────────────┐
                    │                          │                  │
                   D2                         C1                 C2
                1.5KE22CA                   22µF/50V           100nF/50V
               bidirectional               electrolytic           X7R
                    │                          │                  │
                    └──────────────────────────┴──────── GND ─────┘

C1은 극성 부품: `+ → VIN_PROTECTED`, `− → GND`
D2는 `VIN_PROTECTED ↔ GND` 사이 병렬 TVS다. 직렬로 넣지 않는다.

                         U1 TSR 1-2450
VIN_PROTECTED ─────────── pin 1 +VIN
GND ───────────────────── pin 2 GND
+5V ───────────────────── pin 3 +VOUT

+5V ──●────────────────────────────────────────────── 다음 U2 입력으로
      │
      ├── C3 10µF/16V X7R ── GND
      │
      └── TP2 +5V

VIN_PROTECTED ── TP1
GND ──────────── TP4
```

### 2.3 U2 — 3.3V LDO

```text
                              U2 TPS7B8333QDCYRQ1
+5V ────────────────────────── pin 1 IN
GND ────────────────────────── pin 2 GND
+3V3 ───────────────────────── pin 3 OUT
GND ────────────────────────── pin 4 TAB = GND

+5V ──●────────────── U2.1 IN
      │
      └── C4 1µF/16V X7R ── GND

U2.3 OUT ──●── +3V3
           │
           ├── C5 10µF/16V X7R ── GND
           ├── C6 10µF/16V X7R ── GND     ← C5와 병렬
           └── TP3 +3V3

U2.2 GND ── GND
U2.4 TAB ── GND                              ← OUT에 연결 금지
```

C5/C6은 모두 FIT다. MLCC의 DC bias 이후 실효 용량이 LDO의 최소 안정 조건을 만족하는지 부품 선정 시 확인한다.

### 2.4 J1 — 75Ω 동축 입력과 AC coupling

```text
J1 CENTER ──●── COAX_SPDIF
            │
            ├── R1 75Ω ── GND
            │
            ├── TP5 COAX_SPDIF
            │
            └── C9 10nF/50V ──●── SPDIF_AC ── U3.3 IN+
                               │
                               ├── R2 2.2kΩ ── VREF
                               │
                               └── TP6 SPDIF_AC

J1 SHIELD ── GND
```

R1은 동축 입력의 75Ω 종단이다. C9는 직렬 AC coupling이며, C9를 DNP로 두면 신호 경로가 끊어지므로 초기 실장 대상이다.

### 2.5 VREF와 U3 TLV3601-Q1

```text
                         R3 2.2kΩ
+3V3 ────────────────────\/\/\/──┐
                                  │
                                  ●── VREF
                                  │
                         R4 2.2kΩ │
                    GND ─────────\/\/\/── VREF
                                  │
                                  ├── C10 1µF/16V ── GND
                                  │
                                  ├── R2 2.2kΩ ── SPDIF_AC
                                  │
                                  └── U3.4 IN−

                         U3 TLV3601QDBVRQ1
SPDIF_AC ───────────────── pin 3 IN+
VREF ───────────────────── pin 4 IN−
+3V3 ───────────────────── pin 5 VCC
GND ────────────────────── pin 2 VEE/GND
CMP_OUT ────────────────── pin 1 OUT

+3V3 ──●────────────── U3.5 VCC
       │
       └── C7 100nF/16V X7R ── GND          ← U3 바로 옆
```

VREF는 공칭 1.65V다. AC coupling과 mid-supply bias는 대안이 아니라 **함께 사용하는 구성**이다. 입력 부재 감지 회로나 추가 threshold offset을 넣지 않는다.

### 2.6 U3 출력 → U4 DI

```text
U3.1 OUT ── CMP_OUT ── R5 0Ω ── DI ── U4.1 DI
                          │
                          └── 필요 시 22Ω으로 교체 가능한 위치

CMP_OUT ── TP7
DI ─────── TP8
```

R6 `R_HYS`는 회로가 설계되지 않았으므로 DNP로 유지하고, U3 출력·입력 또는 VREF에 임의로 연결하지 않는다.

### 2.7 U4 MAX3295 전원과 Enable

```text
                         U4 MAX3295AUT+T
DI ────────────────────── pin 1 DI
+3V3 ───────────────────── pin 2 VCC
+3V3 ── R16 0Ω ── DE ───── pin 3 DE
Z_RAW ──────────────────── pin 4 Z
GND ────────────────────── pin 5 GND
Y_RAW ──────────────────── pin 6 Y

+3V3 ──●────────────── U4.2 VCC
       │
       └── C8 100nF/16V X7R ── GND          ← U4 바로 옆

DE ──── TP9
Y_RAW ─ TP10
Z_RAW ─ TP11
```

초기 Rev.1은 DE를 R16 0Ω을 통해 항상 HIGH로 둔다. 임의의 RC delayed-enable 회로를 추가하지 않는다.

### 2.8 120Ω 직렬저항과 극성 선택 매트릭스

```text
                         R7 120Ω
U4.6 Y ── Y_RAW ─────────\/\/\/──┬── R10 0Ω FIT ── PAIR_P
                                 │
                                 └── R12 0Ω DNP ── PAIR_N

                         R8 120Ω
U4.4 Z ── Z_RAW ─────────\/\/\/──┬── R13 0Ω DNP ── PAIR_P
                                 │
                                 └── R11 0Ω FIT ── PAIR_N
```

| 설정 | FIT | DNP | 결과 |
|---|---|---|---|
| **기본/순정 극성** | **R10 + R11** | R12 + R13 | `Y→SPDIF+`, `Z→SPDIF−` |
| 반전 시험 | R12 + R13 | R10 + R11 | `Y→SPDIF−`, `Z→SPDIF+` |

**R10~R13을 모두 실장하면 안 된다.** 극성 변경은 ACC OFF 상태에서 수행한다.

### 2.9 L1 CMC와 우회 점퍼

```text
PAIR_P ── L1 CH1 입력
                L1 CH1 출력 ──●── SPDIF_P
                               │
PAIR_N ── L1 CH2 입력          │
                L1 CH2 출력 ──●── SPDIF_N

R14 0Ω DNP: PAIR_P ↔ SPDIF_P       ← CH1 한 채널만 우회
R15 0Ω DNP: PAIR_N ↔ SPDIF_N       ← CH2 한 채널만 우회
```

- 초기 상태: **L1 FIT, R14/R15 DNP**
- CMC를 제거하고 직결하려는 시험 상태: **L1 DNP, R14/R15 FIT**
- L1과 우회 점퍼를 기본 병렬 연결로 함께 실장하지 않는다.
- CMC의 두 채널을 잘못 연결해 `SPDIF_P`와 `SPDIF_N`을 단락시키지 않도록 실제 권선·패드 매핑을 확인한다.

### 2.10 출력 종단, EMC 커패시터, J2

```text
                         R9 56Ω FIT
SPDIF_P ────────────────\/\/\/──────────────── SPDIF_N

C11 TBD DNP: PAIR_P ↔ PAIR_N          ← Pre-CMC 차동 분기
C12 TBD DNP: SPDIF_P ↔ SPDIF_N        ← Post-CMC 차동 분기
C13 TBD DNP: SPDIF_P ↔ GND
C14 TBD DNP: SPDIF_N ↔ GND

SPDIF_P ── TP12 ── J2 SPDIF+
SPDIF_N ── TP13 ── J2 SPDIF−
GND ────────────── J2 GND
```

R9는 CMC 뒤 AMP 쪽 차동선 사이의 FIT 부품이다. C11~C14는 모두 병렬 분기이므로 DNP여도 신호 경로는 유지된다.

---

## 3. 최종 연결 요약

```text
J3 ACC/GND
   │
  F1 → D1 → VIN_PROTECTED
              │
              ├── D2, C1, C2 → GND
              │
             U1 TSR 1-2450 → +5V
                              │
                              ├── C3 → GND
                              │
                             U2 TPS7B8333 → +3V3
                                             │
                                             ├── C4: +5V 입력측
                                             ├── C5+C6: +3V3 출력측
                                             │
J1 COAX → R1 75Ω 종단 → C9 → U3 TLV3601 ── R5 ── U4 MAX3295
                            ▲                              │
                            └── VREF: R2/R3/R4/C10         │
                                                           ├─ Y → R7 → 극성 선택 → L1 CH1 → SPDIF_P
                                                           └─ Z → R8 → 극성 선택 → L1 CH2 → SPDIF_N

SPDIF_P ↔ SPDIF_N: R9 56Ω FIT + C12 DNP
PAIR_P ↔ PAIR_N: C11 DNP
SPDIF_P/N ↔ GND: C13/C14 DNP
최종 출력: J2 SPDIF+ / SPDIF− / GND
```

---

## 4. ERC 전 수동 점검

- [ ] U1 `1=+VIN`, `2=GND`, `3=+VOUT`
- [ ] U2 `1=IN`, `2=GND`, `3=OUT`, `4=TAB=GND`
- [ ] U3 `1=OUT`, `2=GND`, `3=IN+`, `4=IN−`, `5=VCC`
- [ ] U4 `1=DI`, `2=VCC`, `3=DE`, `4=Z`, `5=GND`, `6=Y`
- [ ] D1 `A=ACC_FUSED`, `K=VIN_PROTECTED`
- [ ] D2, C1, C2는 전원과 GND 사이 병렬 연결
- [ ] C1 전해커패시터 극성 확인
- [ ] C7/C8은 U3/U4 전원 핀 바로 옆에 배치
- [ ] R1 75Ω은 BNC 입력에서 GND로 종단
- [ ] VREF는 R3/R4로 생성하고 R2/C10/U3 IN−에 연결
- [ ] R6은 DNP이며 임의의 hysteresis 배선이 없음
- [ ] 기본 극성은 R10/R11만 FIT, R12/R13은 DNP
- [ ] L1 FIT 시 R14/R15는 DNP
- [ ] R9 56Ω은 CMC 뒤 `SPDIF_P ↔ SPDIF_N`에 FIT
- [ ] C11~C14는 모두 DNP
- [ ] J2/J3 및 L1의 물리 핀 번호는 실제 부품 도면으로 별도 확인

## 5. PCB 풋프린트 확정 전에 남은 결정

1. J2 XH 계열 커넥터의 정확한 MPN과 2.50/2.54mm pitch
2. C1의 실제 MPN, 직경, 리드 간격, 높이
3. 일반 저항과 MLCC의 구매 MPN 및 DC bias 실효 용량
4. D1/D2의 리드 성형 간격과 패드 간격
5. U1 TRACO 모듈의 방향, 높이, 핀 간격
6. J1 BNC와 FH1 퓨즈홀더의 실제 패드 번호·기구 치수
7. L1 CMC의 land pattern과 권선 채널 매핑
8. 케이스 납입, 커넥터 방향, 테스트포인트 접근성

이 항목들은 회로도 작업을 막지 않지만, **PCB 풋프린트 lock과 제조 전에는 반드시 해결**한다.
