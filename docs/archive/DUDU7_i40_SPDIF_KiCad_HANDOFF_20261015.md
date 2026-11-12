# DUDU7 → Hyundai i40 순정 AMP S/PDIF 물리계층 변환기

> **KiCad / 로컬 에이전트 인수인계 문서**  
> 작성 기준일: 2026-10-15  
> 프로젝트: `DUDU7-i40-SPDIF-Bridge`  
> 설계 상태: **Rev.A 회로 설계 착수 전 / 미제작 / 부품 미구매 / 차량 실측 미완료**  
> **주의: 이 문서, SVG, 과거 BOM/CSV는 제조 승인(Production Release) 자료가 아니다.** 데이터시트·회로·풋프린트·ERC·전원·신호·열·EMI 검증 후 제작한다.

## 0. 로컬 에이전트에게 줄 핵심 지시

사용자는 Android 헤드유닛 **DUDU7의 RCA Coaxial S/PDIF 출력**을 현대 **i40 순정 AMP(부품번호 `96370-3Z101`)의 `SPDIF+ / SPDIF− / SPDIF GND` 입력**에 연결하는 **외장형 능동 변환기**를 직접 설계/제작하려 한다.

KiCad에서 먼저 **실제 `.kicad_sch` 회로도**를 작성하고 검증한 뒤, 선정된 **소형 검정 알루미늄 케이스**에 맞춘 **2-layer PCB**를 만든다. 회로도·PCB의 모든 소자는 **Top 면 실장**, Bottom은 가능한 연속적인 GND plane으로 유지한다. 차량 IBS(배터리 센서) 학습에 영향을 주는 상시 부하는 피해야 하므로 **상시 B+는 연결하지 않는다. ACC 12V만 공급한다.**

**최신 우선 전원 구조는 `ACC 12V → 국부 퓨즈/역극성/TVS → 5V Buck → 3.3V LDO`이다.** 과거 `POWER.svg`의 12V → 3.3V LDO 단독 구조는 **구버전**으로 간주한다. `5V Buck + 3.3V LDO`는 발열 개선을 위한 **우선 검토안**이며 자동차 과도전압, 스위칭 EMI, 출력 리플, 부품 정격을 검증해야 확정 가능하다.

**실제 제조용 Gerber를 만들거나 부품을 주문하기 전에:** MCU가 필요 없는 회로임을 확인하고, 전원·부품 핀맵·비교기 입력·MAX3295 출력단 부하·차량 보호 회로·케이스 간섭을 검토한다. 사용자가 단순한 예시 도면을 그대로 복제하지 않도록 미확정 회로값은 `DNP/TBD`로 관리한다.

---

## 1. 목표와 시스템 경로

```text
DUDU7 Android Head Unit
  RCA Female: Coaxial S/PDIF Out
          │
          │ RCA Male ── [75 Ω 동축 케이블, 3~5 m] ── BNC Male
          │ (차체 내부를 우회해 운전석 아래로 이동)
          ▼
  75 Ω BNC Female (알루미늄 케이스 엔드패널 고정)
          │ 짧은 75 Ω 동축 피그테일 / 기판 입력
          ▼
  75 Ω termination + AC coupling / input bias
          ▼
  TLV3601-Q1 고속 Comparator (S/PDIF slicing)
          ▼ 3.3 V logic BMC stream
  MAX3295 differential driver
          ▼
  2 × series resistor + dual-line common-mode choke + optional EMI capacitors
          ▼
  J3: SPDIF+ / SPDIF− / SPDIF GND
          ▼
  Hyundai i40 OEM AMP (96370-3Z101)
```

이 제품은 **S/PDIF 프로토콜 디코더가 아니다.** 75 Ω 단일종단 S/PDIF 파형을 정형해 순정 AMP가 기대하는 차동 물리 신호로 변환하는 **물리계층 어댑터**다. S/PDIF BMC 인코딩은 유지한다. 두 기기의 전기적 규격이 실제로 일치하는지는 확인해야 한다.

## 2. 확정된 기구·차량 장착 조건

| 항목 | 사용자 확정/현재 계획 | 확인할 사항 |
|---|---|---|
| 차량 | Hyundai i40, OEM amplifier `96370-3Z101` | 정확한 AMP 커넥터 핀은 차량 배선을 직접 확인 예정 |
| 전원 | AMP 하네스에서 **ACC/ON + GND** 분기 | 시동 OFF에서 ACC 0V, 시동/ACC 상태에서 전압 실측 |
| 상시 B+ | **사용하지 않음** | 전원 외 신호선을 통한 역급전 여부 확인 |
| IBS | 차량 IBS 학습·초기화가 방해되지 않도록 시동 OFF 시 추가 전원부하 최소화 | 차량 전체 암전류 변화 및 휴지 상태 측정 |
| 외함 | 기성 소형 **검정 알루미늄 케이스** 선호 | `Hammond 1455C1201BK` (약 120×54×23mm) 우선 후보. 실제 내부 치수 재확인 |
| PCB | 2층, **부품 전부 Top**, Bottom GND plane | 케이스의 PCB 삽입 홈 및 높이와 부품 간섭 확인 |
| 장착 | 순정 AMP 상단에 보유 중인 **3M 차량용 내열 양면테이프** 부착 | AMP 표면 온도, 박리, 케이블 응력, 진동 |
| 전원 단자 | 분리 가능한 2핀 커넥터 | 실제 엔드패널 고정 방식·온도 등급·높이 |
| 출력 단자 | 분리 가능한 3핀 커넥터 | SPDIF±/GND 배선, 꼬임쌍, 쉴드 접지 정책 |
| S/PDIF 입력 단자 | **75 Ω BNC Female**, 케이스 패널 체결 | BNC 절연형/비절연형 및 케이스 접지 정책 결정 |
| RCA→BNC 케이블 | **75 Ω**, 3~5m. 설치 여유 때문에 **5m 선호** | 굵고 뻣뻣한 5C 케이블보다 3C급 유연한 케이블 검토 |
| 납땜 | 손납땜 가능한 패키지 우선 | 대형 TVS, 파워 인덕터, SOT-23, SOT-223 등 작업성 고려 |
| 구매 | **아직 어떤 부품도 구매하지 않음** | BOM 최종화 후 소량 구매 가능한 국내 판매처 우선 |

기존에 검토한 RCA Male ↔ BNC Male 5m 제품: HDTOP HT-ZYGCB050, MachLink MLZ-WCB050, Canare L-3C2VS 계열의 주문 제작 케이블. 링크·재고·실제 특성·커넥터 성별은 주문 직전에 확인할 것.

### 기존 AMP 회로도에서 확인된 사실

사용자 제공 `A/V & 내비게이션 회로(5)` 도면상 AMP에는 **상시전원 3개(F01-A 1,2,3: AMP 25A 계통)**와 **ACC/ON(F01-A 7: 10A 퓨즈 계통)**가 따로 존재한다. 프로젝트는 **ACC/ON만 사용**한다. AMP의 상시전원선에 변환기를 물리지 않는다. AMP 자체의 ACC 입력은 사용자 차량에서 정상적으로 작동하고 있음.

차량 AMP 하네스의 `SPDIF+`, `SPDIF−`, `SPDIF GND`는 차량별 커넥터 도면 간 핀 배열 차이 가능성이 있으므로 **사용자가 직접 실차 배선 확인 후 매칭한다.** 과거 참고도면의 표기를 특정 실차의 절대적인 핀배열로 간주하지 말 것. **SPDIF−는 GND가 아니다.**

## 3. OEM 회로 분석 기록 (실측/사진 근거 + 불확실성)

기존 i40 AVN PCB에서 다음이 관찰되었다.

- `U400`: **MAX3295** 차동 드라이버로 식별됨. OEM 출력과 동일 계열 칩을 사용하려는 가장 큰 근거.
- `R419`, `R420`: 각각 `121` 마킹으로 **120 Ω**의 후보. 다만 **실제 회로상 위치(직렬/병렬)는 PCB 패턴 연속성 실측 전에는 미확정**.
- `F400`: 4패드 소자로 **2라인 공통모드 초크**로 추정.
- `C430`, `C431`: 각 출력선과 GND 사이의 커패시터로 추정, **값 미확정**.
- `C433`: 출력선 사이 커패시터로 추정, **값 미확정**.
- `R423`, `C593`: 구체적인 연결·값·용도 미확정.

따라서 **OEM의 미검증 부품값과 배선구조를 완벽히 복제했다고 주장하지 않는다.** Rev.A에서는 기성 고속 차동 EMI 필터 구조를 **시험용 시작값**으로 사용한다.

원본 참고 자료 예: `Avn_bottom(1).zip`, `Max3295_회로(1).zip`, `board_photos/` 사진 자료 (대화 파일에 존재하면 로컬 에이전트에 함께 제공).

---

## 4. Rev.A 전원 설계 — 우선안 (5V Buck + 3.3V LDO)

### 4.1 전원 설계가 수정된 이유

이전 그림 `DUDU7_i40_RevA_SCH_01_POWER.svg`은 **ACC 12V → TPS7B8333-Q1 단독 → 3.3V**였으나, 사용자는 투명 열수축 튜브 대신 알루미늄 케이스를 선택할 가능성이 높고, AMP 위 부착·여름철 고온을 고려해 **발열을 최소화**하기로 논의했다.

LDO 발열 1차 근사: `P_LDO ≈ (VIN − VOUT) × IOUT`. 엔진 ON `14.4V`, 40mA 부하 가정 시:

| 구성 | LDO 자체 발열 (40mA) | 설명 |
|---|---:|---|
| 14.4V → 3.3V LDO | `(14.4−3.3)×0.04 ≈ 0.444 W` | 좁은 공간에서 고온 가능 |
| 14.4V → **5V Buck** → 3.3V LDO | `(5−3.3)×0.04 ≈ 0.068 W` | **LDO 발열 약 85% 감소** |

Buck 자체 손실을 제외한 **LDO 열량 비교**이며, 전체 시스템 효율/발열 개선량을 85%라고 표현해서는 안 된다. 자동차 과도전압과 **2.1MHz Buck 스위칭이 S/PDIF에 주는 EMI**도 별도 확인해야 한다. 3.3V 직결 Buck 방식도 대안이지만 현재는 **저잡음 LDO 후단 구조를 우선 검토**한다.

### 4.2 우선 구성

```text
J2-1 ACC_12V
   │
  F1   0.5A 국부 퓨즈 [잠정, 실제 배선·기동전류 검증]
   │
  D1   1N4007-TP 직렬 역극성 보호 [잠정]
   │
   ├── D2 1.5KE27A TVS ─ GND [서지 보호 정격 검증]
   ├── C3 10µF / 50V (Bulk) ─ GND
   ▼
U4 = LMR16006YQ5DDCRQ1 (고정 5.0V Buck, Automotive, 2.1MHz)
   │ + 외부 C10, C12(bootstrap), L1(6.8µH 시작값), D3(Schottky), C11 등
   ▼
+5V_BUCK
   ├── TP_5V
   ├── C13 (LDO 입력 디커플링)
   ▼
U1 = TPS7B8333QDCYRQ1 (고정 3.3V / 150mA LDO)
   ├── C4 >=2.2µF 출력 커패시터
   ▼
+3V3
   ├── TLV3601-Q1 VCC
   ├── MAX3295 VCC
   └── 1.65V 바이어스 / 기준전압 분배기

J2-2 GND → 공통 GND plane (패널/차체 접지는 별도 정책 결정)
```

**U4 실제 핀맵 (TI 데이터시트 기준, SOT-23-6 DDC):**

| 핀 | 기능 | Net / 메모 |
|---:|---|---|
| 1 | `CB` | `BOOT`, bootstrap cap C12를 SW(6번)와 연결 |
| 2 | `GND` | `GND` |
| 3 | `FB` | **고정 5V형은 Buck 출력 +5V 감지점에 직접 연결** |
| 4 | `SHDN` | **입력 VIN에 연결하여 ACC ON 시 자동 활성화** (데이터시트 예제). UVLO 옵션 재검토 가능 |
| 5 | `VIN` | `VIN_PROTECTED` |
| 6 | `SW` | `SW_BUCK`; L1 입구, D3 Cathode, C12 SW측 |

Buck 필수 외장소자 및 초기값(고정 5V형 TI Figure 14 기준):

| Ref | 역할 | 초기값/기준 | 상태 |
|---|---|---|---|
| C10 | U4 근처 입력 세라믹 | `2.2µF / >=50V`, X7R (DC bias 고려) | 후보 |
| C12 | CB–SW bootstrap | `100nF / >=10V`, X7R | 참고값 |
| L1 | SW→+5V_BUCK | `6.8µH`부터 검토 (포화전류·DCR·크기 확인) | 후보 |
| D3 | 비동기 Buck 프리휠 Schottky | **Anode=GND, Cathode=SW**, 역전압/전류/열 정격 선정 | 모델 미정 |
| C11 | Buck 출력 | `10µF / >=10V` (유효 정전용량 검증) | 참고값 |
| C13 | U1 입력 | `1~2.2µF / >=10V`, U1에 근접 | 후보 |
| C4 | U1 출력 | `>=2.2µF / >=10V`, U1에 근접 | 필수 최소값 |
| C3 | 서지 보호 후 VIN 벌크 | `10µF / 50V` | 잠정 |

TI Figure 14는 **FB를 +5V에 연결**하며 **고정 5V형에는 별도의 출력 분압 저항이 필요하지 않다.** U4의 `SHDN`을 VIN에 직접 연결하는 것은 TI 애플리케이션 예시이며, 차량 시동 중 전압 강하와 UVLO를 포함한 실제 적용은 검토한다.

**U1 핀맵 (TPS7B8333QDCYRQ1, SOT-223):** 1=IN(+5V_BUCK), 2=GND, 3=OUT(+3V3), 4=TAB(GND). TAB를 넓은 GND copper와 Thermal Via로 연결할 것.

### 4.3 차량 전원 보호/차량 IBS

- 입력에는 이미 순정 퓨즈가 있지만, AMP 계통의 큰 퓨즈가 소형 분기 배선을 보호하지 않을 수 있으므로 **분기 지점의 소형 F1 퓨즈**를 유지한다.
- D1 1N4007 직렬 방향: **Anode → ACC+, Cathode → 보호 VIN** (전압강하·열·작업성 재평가 가능).
- D2 1.5KE27A 단방향 TVS: **Cathode=보호 VIN, Anode=GND**. 이는 이전 검토 후보이며, **자동차 load-dump/ISO 7637 과도상황을 모두 보장하는 보호 회로가 아니다.** 실제 서지 파형, 배선 길이, 입력 커패시터, 부품 제한전압과 안전 마진을 확인한다.
- 차량 ACC가 OFF에서 0V인지 실측. OFF에서 `+5V_BUCK`, `+3V3`가 완전히 방전되는지 확인. **코액셜 신호선·GND·순정 AMP 출력선을 통한 역급전** 가능성도 검증. 배터리 IBS 센서가 휴지 상태 학습을 할 수 있도록 시동 OFF 추가 암전류를 최소화한다.
- BNC 실드, 케이스, 차량 GND와 AMP `SPDIF GND`는 실제 시스템 접지 정책에 따라 연결할 것. 금속 하우징에 불필요한 접지루프·차체단락을 만들지 않는다.

---

## 5. S/PDIF 전기적 경로 — Rev.A 잠정 설계

### 5.1 RCA → BNC 75Ω 동축 입력

- DUDU7 출력은 **일반적인 S/PDIF Coax (75Ω 부하 기준 약 0.5Vpp)**라고 **가정**하고 있다. **실측 아님.**
- DUDU7 후면 RCA Female → RCA Male/75Ω 동축 5m/BNC Male → 외함 패널의 **75Ω BNC Female**.
- BNC는 PCB 가장자리에 큰 힘을 전달하지 않도록 **패널 체결형**을 우선 사용하고, 짧은 75Ω 피그테일로 PCB의 신호/GND 입력 패드에 연결한다. 50Ω BNC 부품과 혼용하지 않도록 확인.
- J1 센터 → R1 75Ω (센터-GND 종단, **PCB 입력단 바로 근처**) → C1 10nF 직렬 AC coupling → 비교기 입력 바이어스.
- 75Ω 종단이 반영된 상태에서 DUDU7 S/PDIF 출력 전압·파형을 가능하면 오실로스코프로 확인한다. 멀티미터와 Pico 2 로직애널라이저만으로 아날로그 0.5Vpp·차동 아이 특성을 검증할 수 없다.

### 5.2 TLV3601-Q1 comparator (U2)

| U2 핀 | 이름 | Net |
|---:|---|---|
| 1 | OUT | `SPDIF_CMOS` → U3 DI |
| 2 | VEE | GND |
| 3 | IN+ | `SPDIF_AC_BIASED` |
| 4 | IN− | `VREF_1V65` |
| 5 | VCC | +3V3 |

예시 바이어스:

- R2=10k (+3V3→SPDIF_AC_BIASED), R3=10k (SPDIF_AC_BIASED→GND).
- R4=10k (+3V3→VREF_1V65), R5=10k (VREF_1V65→GND); C2=100nF (VREF_1V65→GND).
- C1을 통해 들어온 SPDIF 신호가 1.65V 중심으로 바이어스되도록 의도한 구성.
- **주의:** 기준전압과 신호 바이어스의 차이, 입력 잡음, 내부 히스테리시스, 전원 시작/종료 타이밍에 따라 가짜 edge·지터가 발생할 수 있다. 이는 확정된 수신회로가 아니다. 실제 비교기 데이터시트와 시뮬레이션, 오실로스코프 측정으로 검증한다.
- U2 VCC–GND에 **100nF 디커플링**, 가능하면 1µF 근접 배치. 테스트포인트 TP1 `SPDIF_CMOS`.

### 5.3 MAX3295 differential driver (U3)

| U3 핀 | 이름 | 연결 |
|---:|---|---|
| 1 | DI | `SPDIF_CMOS` (U2 OUT) |
| 2 | VCC | +3V3 |
| 3 | DE | +3V3 (출력 항상 Enabled; 옵션 저항/점퍼 가능) |
| 4 | Z | 반전 출력 → `SPDIF−` |
| 5 | GND | GND |
| 6 | Y | 비반전 출력 → `SPDIF+` |

- TI comparator의 `IN−`와 MAX3295의 차동 `Z` 출력 핀을 혼동하지 말 것.
- 현재 순정 AVN에서 **MAX3295**가 관찰되어 이 칩을 우선 선택. 그럼에도 이것은 **RS-422/RS-485 driver이지 RCA S/PDIF driver가 아니다.** AMP에서 허용하는 차동 입력 수준과 공통모드 범위를 실제 확인한다.
- MAX3295 데이터레이트 **최대 20Mbps급**. 48kHz S/PDIF부터 시험. **192kHz S/PDIF에서는 BMC 전이 간격이 짧아 속도/지터 마진 미확보**이므로 최대 지원 샘플링레이트를 확정하지 않는다.
- U3 VCC에 100nF 디커플링과 전원 측정 TP. TP2=Y, TP3=Z (특히 차동 출력은 GND 대비만 보지 말고 차동으로도 측정 필요).

### 5.4 EMI 및 AMP 출력

```text
U3 Y → R7(22Ω 시작값) → FL1 common mode choke (line 1) → SPDIF+
U3 Z → R8(22Ω 시작값) → FL1 common mode choke (line 2) → SPDIF−

선택적 EMI 필터:
C430 22pF (SPDIF+→GND)  [DNP에서 시작 권장]
C431 22pF (SPDIF−→GND)  [DNP에서 시작 권장]
C433 10pF (SPDIF+↔SPDIF−) [DNP에서 시작 권장]
```

- FL1 후보 `TDK ACM2012-900-2P-T002`: **90Ω @100MHz는 common-mode 임피던스이며 직렬 저항 90Ω가 아님**.
- R7/R8는 22Ω을 **초기값**으로 둔 것. 순정 AVN R419/R420의 실제 회로연결이 밝혀지면 재설계. 0Ω·22Ω·다른 저항 값을 실험할 수 있게 풋프린트 선택.
- C430/431/433은 우선 **미실장(DNP)**하고 EMI/파형 평가 후 장착. 용량이 과하면 고속 에지를 심하게 약화할 수 있다.
- 차동 종단저항을 출력 보드에 **무조건 추가하지 말 것**. 수신 AMP 내부에 이미 있을 가능성이 있으며, AMP에서 종단과 신호 수준을 확인해야 한다.
- J3 3핀 출력: `1=SPDIF+`, `2=SPDIF−`, `3=SPDIF GND`를 **우리 PCB의 편의상 정의**. 차량 AMP 커넥터의 번호라는 뜻이 아니다.

---

## 6. 부품/스펙 후보 (구매 전 반드시 재검증)

| Ref | 부품 | 제조사/모델 | 상태 및 메모 |
|---|---|---|---|
| U4 | 차량용 5V Buck | TI `LMR16006YQ5DDCRQ1` (SOT23-6, 2.1MHz) | **우선 검토안**, 고정 5V 모델 |
| U1 | 3.3V LDO | TI `TPS7B8333QDCYRQ1` (SOT-223, 150mA) | Buck 뒤에서 사용 |
| U2 | 고속 comparator | TI `TLV3601QDBVRQ1` (SOT23-5) | VCC 3.3V, 약 6mA typ |
| U3 | 고속 차동 드라이버 | ADI/MAXIM `MAX3295AUT+T` 또는 자동차 적격형 `MAX3295AUT/V+T` 검토 | 20Mbps, 3.3V. 품목별 AEC-Q100 구분 |
| F1 | 소형 퓨즈 | 0.5A 후보 | 차량 분기 배선, inrush 검증 후 정격 확정 |
| D1 | 역극성 보호 | `1N4007-TP` 잠정 | 전압강하/발열/풋프린트 재검토 |
| D2 | TVS | `1.5KE27A` 잠정 | 단방향, 서지 검증 필요 |
| D3 | Buck Schottky | 모델 미정 | Anode=GND, Cathode=SW; 전압/전류/패키지 선정 |
| L1 | Buck 인덕터 | `6.8µH` 시작 | I_sat, DCR, 자체발열, EMI/높이 고려 |
| C10 | Buck CIN | 2.2µF 50V 후보 | DC bias/서지 확인 |
| C11 | Buck COUT | 10µF 10V 후보 | DC bias 및 리플 확인 |
| C12 | Buck Bootstrap | 100nF ≥10V | U4 CB↔SW |
| C13 | LDO CIN | 1~2.2µF ≥10V | U1 IN↔GND |
| C4 | LDO COUT | ≥2.2µF ≥10V | U1 OUT↔GND |
| R1 | 75Ω 종단 | 75Ω 1% | J1 근처 |
| C1 | 입력 DC blocking | 10nF | 초기값 |
| R2~R5 | 비교기 바이어스 | 10kΩ ×4 | 초기값 |
| C2 | 기준전압 바이패스 | 100nF | VREF→GND |
| R7/R8 | 차동 직렬저항 | 22Ω ×2 | 초기값, 값 변경 가능 |
| FL1 | 2라인 CMC | TDK `ACM2012-900-2P-T002` | 90Ω common-mode @100MHz |
| C430/C431 | output-to-GND | 22pF NP0/C0G ×2 | **DNP 시작** |
| C433 | line-to-line | 10pF NP0/C0G ×1 | **DNP 시작** |
| J1 | 입력 커넥터 | 75Ω BNC Female 패널 고정 | 피그테일 연결 |
| J2 | 차량 전원 | 2-pin 잠금식 커넥터 | ACC / GND |
| J3 | AMP 연결 | 3-pin 잠금식 커넥터 | SPDIF+ / SPDIF− / GND |
| Case | 외함 | `Hammond 1455C1201BK` 후보 | 치수·엔드패널 가공·PCB 삽입 깊이 확인 |

### 구매처 관련 과거 검토 링크 (현행 가격·재고 보장 안 됨)

- Comparator: [디바이스마트 TLV3601QDBVRQ1](https://www.devicemart.co.kr/goods/view?no=16006763)
- CMC: [디바이스마트 ACM2012-900-2P-T002](https://www.devicemart.co.kr/goods/view?no=15435306)
- 75Ω/22Ω 저항: 디바이스마트, 엘레파츠 등에서 구매 후보 검색
- 22pF NP0: [디바이스마트](https://www.devicemart.co.kr/goods/view?no=7218892)
- 10pF NP0: [엘레파츠](https://eleparts.co.kr/goods/view?no=16922074)
- `LMR16006YQ5DDCRQ1`: [DigiKey](https://www.digikey.kr/ko/products/detail/texas-instruments/LMR16006YQ5DDCRQ1/5395815)
- `TPS7B8333QDCYRQ1`: TI/디지키 등 구매처 최종 확정 필요
- 과거 파일 `DUDU7_i40_SPDIF_RevA_BOM_v2_ACC_Enable.xlsx`는 **상시 B+ + EN 제어 시절 BOM**이므로 구매 기준으로 사용 금지. 원가·수량·구매처를 현행 Buck+LDO 설계로 업데이트 필요.

## 7. 전기 회로도(.kicad_sch) 시트 구상

**Sheet 01 — `POWER_ACC_Buck_5V_LDO_3V3`**

- J2, F1, D1, D2, C3, U4, C10, C11, C12, L1, D3, C13, U1, C4
- 전원 네트: `ACC_12V`, `FUSED_12V`, `VIN_PROTECTED`, `SW_BUCK`, `BOOT`, `+5V_BUCK`, `+3V3`, `GND`
- TP: `TP_VIN`, `TP_5V`, `TP_3V3`, `TP_GND`
- U4는 고정 5V품이므로 `FB`가 **+5V_BUCK**로 직접 회귀. 인덕터/다이오드/부트스트랩이 생략된 단순 블록도가 아니라 TI Figure 14에 따른 실제 Buck 회로로 구현해야 한다.

**Sheet 02 — `SPDIF_COAX_TO_OEM_DIFF`**

- J1 (BNC 외함), R1, C1, R2, R3, R4, R5, C2, U2, U3, R7, R8, FL1, C430/431/433, J3
- 필수 디커플링 및 테스트 포인트 포함.
- 신호 네트: `SPDIF_COAX`, `SPDIF_AC_BIASED`, `VREF_1V65`, `SPDIF_CMOS`, `SPDIF_Y`, `SPDIF_Z`, `SPDIF_OUT_P`, `SPDIF_OUT_N`, `GND`, `+3V3`.

**Sheet 03 — 선택적 `MECHANICAL_CONNECTORS`**

- 케이스, BNC 패널, 케이블/커넥터, 브래킷, PCB 가이드 홈, 실드 및 GND 정책을 Notes/Mechanical layer로 명문화.

### 회로도 심벌 핀 번호(반드시 데이터시트/footprint 대조)

```csv
Ref,Pin,Function,Net
U4,1,CB,BOOT
U4,2,GND,GND
U4,3,FB,+5V_BUCK
U4,4,SHDN,VIN_PROTECTED
U4,5,VIN,VIN_PROTECTED
U4,6,SW,SW_BUCK
U1,1,IN,+5V_BUCK
U1,2,GND,GND
U1,3,OUT,+3V3
U1,4,TAB,GND
U2,1,OUT,SPDIF_CMOS
U2,2,VEE,GND
U2,3,IN+,SPDIF_AC_BIASED
U2,4,IN-,VREF_1V65
U2,5,VCC,+3V3
U3,1,DI,SPDIF_CMOS
U3,2,VCC,+3V3
U3,3,DE,+3V3
U3,4,Z,SPDIF_Z
U3,5,GND,GND
U3,6,Y,SPDIF_Y
```

**원래 SVG 일부와 예전 CSV에 핀 관련 불일치가 있었으므로 이 표를 새로운 검증 기준으로 삼되, 실제 제조사 PDF를 최종 기준으로 삼는다.** (입출력 전원 핀, SOT-223 탭, Comparator의 IN−/IN+ 혼동 주의)

## 8. PCB 설계·케이스 적용 지침

1. **실물 케이스를 우선 확정**하고 Hammond 제조사의 STEP/DXF 기계도를 기준으로 PCB 폭·길이·슬라이드 홈 두께·엔드패널 위치를 확정한다. 외형 `120×54×23mm`는 **외형 후보값**이며 PCB 유효 치수가 아님.
2. 모든 소자는 Top에 배치. Bottom GND Plane 연속성을 최우선. 필요한 경우 Thermal Via, GND stitching via 사용.
3. Buck U4, 입력 C10, Schottky D3, 인덕터 L1의 **핫 루프를 극단적으로 짧게** 구성. SW copper는 작게. 인덕터를 **BNC→Comparator 입력 및 기준전압 분배기**에서 멀리 떨어뜨림.
4. 5V와 3.3V 출력은 Buck 스위칭 전류 리턴과 comparator 아날로그 기준전압 경로를 분리해 적절한 GND plane으로 복귀. **GND를 무조건 절단하거나 별도 floating plane으로 만드는 것 금지.**
5. U1 LDO GND 탭과 Bottom GND Plane을 연결해 열 확산. 케이스로 열 전달을 하려면 **절연·열접촉 구조**가 실제로 필요하며, 단순 금속 케이스 사용만으로 방열 성능을 보장하지 않는다.
6. J1 BNC에서 R1/ C1/ U2까지 입력 배선을 매우 짧게 배치하고, 75Ω 피그테일·종단위치·GND return을 고려한다.
7. U3 → 직렬저항 → FL1 → 출력커넥터는 짧고 평행하게 배치. 일반적으로 긴 단일종단 신호 구간보다 짧은 차동 구간이 유리.
8. 케이스에 고정된 BNC와 배선 하네스가 PCB 납땜부에 장력을 전달하지 않도록 한다. **차량용 내열 양면테이프**를 사용하더라도 5m 케이블은 별도로 차체에 고정한다.
9. PCB 바닥에 부품을 배치하지 않는다. SMT/TH 핀 끝단·PCB 바닥 copper와 알루미늄 케이스가 접촉하지 않도록 기계적 절연 확보.
10. 고주파·전원 필터와 금속 케이스의 EMC 동작을 검토. BNC 실드 ↔ PCB GND ↔ 알루미늄 케이스 ↔ AMP SPDIF GND ↔ 차량 접지의 접속 정책은 실측 후 확정.

## 9. 시험 및 검증 계획

| 순서 | 검증 내용 | 합격 판단 예시 / 비고 |
|---:|---|---|
| 1 | KiCad **ERC** | 미연결·전원 심벌·출력 충돌·핀맵 오류 제거 |
| 2 | 부품 풋프린트 | U4/U1/U2/U3 top view, pin 1, SOT-223 TAB, 커넥터 핀 순서, 기구 도면 확인 |
| 3 | DRC / 제조 | 트랙/홀/실크/keepout, 케이스 슬롯과 PCB의 기계적 간섭 검사 |
| 4 | 벤치 PSU 전원 | 12V→5V→3.3V 각 지점 동작, 전류제한, 입력 9~16V 등 시험 |
| 5 | 전원 노이즈 | Buck ripple, 3.3V ripple, startup/stop, D2·D3·인덕터/IC 온도 측정 |
| 6 | 전원차단 / IBS | ACC OFF 이후 5V/3.3V가 꺼지는지, 역급전 및 전체 암전류 변화 측정 |
| 7 | RCA S/PDIF 입력 | 75Ω 종단 후 0.5Vpp 가정 검증, U2 출력 BMC 지터/오검출 확인 |
| 8 | MAX3295 | Y/Z 극성, 차동 출력 amplitude/edge, 부하별 소비전류, EMI 필터 DNP vs populated 비교 |
| 9 | 음원 검증 | 48kHz 우선, 44.1/96/192kHz 등은 단계적으로 확인. drop/mute/pop 이슈 포함 |
| 10 | 차량 적용 | ACC/IGN on-off, 스타트 중 전원 변화, 앰프 음질, 잡음, 고온 장시간, 진동·하네스 이탈 |

**장비 조건:** 사용자에게 멀티미터는 있으나 오실로스코프는 없음. Raspberry Pi Pico 2 Logic Analyzer는 관심만 있고 구매 전이며, GPIO 로직 분석만으로는 RCA 0.5Vpp나 진짜 차동 아날로그 파형을 검증할 수 없다. 실제 보드 검증에는 **오실로스코프(가능하면 100MHz 이상급)** 확보 또는 외부 측정 지원 권장. 절대 12V/미확인 신호를 Pico 2 GPIO에 직접 넣지 말 것.

## 10. 설계상 미확정 / 차단해야 하는 실수

- **(매우 중요) 5V Buck을 `POWER.svg`에서 빠뜨리지 말 것.** 과거 버전 SCH 전원부를 그대로 재사용하지 않는다.
- 과거 단독 LDO 회로에서 추론된 BOM 가격/합계를 그대로 사용하지 말 것.
- DUDU7 RCA 실제 피크레벨, 75Ω source/termination, S/PDIF 출력 규격이 실측되지 않음.
- AMP 내부 종단 저항 및 허용 차동 입출력 레벨 미확인.
- `R419/R420 =120Ω`는 마킹 추정이며 회로상 위치 미확인. OEM 필터 C값 미확인.
- 1.5KE27A + 1N4007 + Buck 조합이 **자동차 부하차단 과도전압을 모두 보호한다고 단정 금지**.
- `MAX3295AUT+T`와 AEC-Q100 승인 제품 `MAX3295AUT/V+T` 차이 확인.
- 5V Buck 효율은 실제 20~80mA 정도의 경부하에서도 확인해야 함. 5V→3.3V LDO 발열은 줄지만 전원 EMI·부품 수는 증가.
- 보드 케이스가 **알루미늄**이므로 BNC/GND/차체 금속 접촉 및 절연 정책 확정 필요.
- 판넬가공 없이 모든 커넥터가 기성 Hammond 케이스에 들어간다고 가정하지 말 것.
- MAX3295의 20Mbps 한계에서 192kHz 입력을 지원한다고 보장하지 말 것.
- 새 `.kicad_sch`는 아직 없음. SVG는 시각적 설계 가이드일 뿐 KiCad 파일 아님.

## 11. 로컬 에이전트 작업 순서 및 완료물

**Task 1 — Symbol & SCH:** TI/ADI 공식 데이터시트 확인 → 정확한 KiCad library symbol 및 footprint 선택 → `POWER`/`SPDIF` sheets 제작 → ERC → 회로 리뷰용 PDF/SVG, `*.kicad_sch` 및 `*.kicad_pro` 생성.

**Task 2 — BOM & Mechanical:** 파워 Buck 주변소자, BNC 패널 커넥터, 케이스 내부 치수, 하네스·커넥터를 포함한 BOM 확정. 부품 실장 높이·사이즈·리드 타임·가격·구매 링크 재검증.

**Task 3 — PCB:** 선정 케이스의 레일 슬롯 치수 및 엔드패널 위치 확인, Top 부품 배치, 2-layer GND plane, Buck 핫루프 및 디지털 신호 분리, DRC, 3D PCB/케이스 fit check.

**Task 4 — Prototype:** 제조 파일(`Gerber + drill`), 조립 도면, 제조 BOM, 검사 체크리스트 준비 → 사용자 검토 → 제작·벤치 측정.

사용자는 **디바이스마트/엘레파츠에서 단품 소량 구매**를 선호하며, 손납땜과 기성 케이스·차량용 양면테이프를 사용할 계획이다.

---

## 12. 참고 데이터시트/규격 링크

- TI LMR16006Y-Q1 fixed 5V buck: https://www.ti.com/lit/ds/symlink/lmr16006y-q1.pdf  — **page 3 pinout, page 14 Figure 14 fixed-output application circuit, page 15 PCB layout guideline**
- TI LMR16006YQ5DDCRQ1 제품: https://www.ti.com/product/LMR16006Y-Q1/part-details/LMR16006YQ5DDCRQ1
- TI TPS7B83-Q1: https://www.ti.com/product/TPS7B83-Q1
- TI TLV3601-Q1: https://www.ti.com/product/TLV3601-Q1
- ADI MAX3295: https://www.analog.com/en/products/max3295.html
- Hammond 1455 series: https://www.hammfg.com/part/1455C1201BK
- DUDU7 판매/참조 페이지: https://www.mekede.com/qqw10/3778.html

## 13. 함께 전달할 파일

- `DUDU7_i40_RevA_SCH_01_POWER_Buck5V_LDO3V3.svg` — **이번에 수정한** 전원 회로 개념도 (Buck 포함)
- `DUDU7_i40_RevA_SCH_02_SPDIF.svg` — 과거에 만든 S/PDIF 신호 경로 SVG, 최종 핀·노드 검증 전
- `DUDU7_i40_RevA_POWER_NETS.csv` — 새 전원 회로 핵심 연결표
- `DUDU7_i40_RevA_SCH_pin_net_example.csv` — 이전 설계용 핀·Net 초안 (전원 U4 연결은 없음 / 새 CSV 우선)

> **인수인계 요약:** KiCad에서 최초 구현할 전원부는 **ACC 전용 12V + 보호회로 + LMR16006YQ5(5V Buck) + TPS7B8333(3.3V LDO)**. 신호부는 **75Ω BNC → TLV3601-Q1 → MAX3295 → optional EMI → OEM AMP**. 단면 부품실장 2-layer PCB + 기성 알루미늄 케이스. 회로/부품/핀이 검증되기 전까지 실제 제작용이라고 말하지 말 것.
