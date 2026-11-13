# HYK_AMP_Converter — DUDU7 → Hyundai i40 순정 AMP S/PDIF 변환기 (Rev.1)

Android 헤드유닛(DUDU7 / FYT7862)의 RCA Coaxial S/PDIF 출력을 현대 i40 순정 AMP(부품번호 `96370-3Z101`)의 차동 SPDIF 입력(`SPDIF+ / SPDIF− / GND`)에 연결하는 **능동 물리계층 변환기**입니다.
S/PDIF 프로토콜은 디코딩하지 않고, 75Ω 동축 신호를 정형해 순정 AMP가 기대하는 차동 신호로 바꿉니다.

> ⚠️ **제작 승인(Production Release) 문서가 아닙니다.**
> 회로·부품·핀 검증과 차량 시험이 끝나기 전에는 Gerber 발주와 부품 주문을 하지 마세요.
> 모든 설계 판단의 기준은 **`docs/DUDU7_i40_SPDIF_TX_Rev1_BOM_for_KiCad_Agent.md` (Rev.1, 2026-11-11)** 입니다.
> Rev.A2 문서의 LMR16006 Buck 전원단은 Rev.1에서 **사용하지 않습니다**.

## 현재 상태 (2026-11-13)

- 회로도: Rev.1 부품 **54개 배치·배선 완료**, **ERC 0건**, 넷리스트 핀 단위 대조 일치
- 커넥터 핀 배정 (GND는 항상 마지막): J3 `1=ACC_IN, 2=GND` · J2 `1=SPDIF−, 2=SPDIF+, 3=GND` (결정 로그 #23: FL1 출력과 일직선 배선)
- PCB: 미착수 (`.kicad_pcb` 빈 파일)
- 확정 결정 기록: BOM 문서 §9 "Decision log"

## 설계 개요

```text
ACC 12V ─ J3 ─ F1 2A ─┬─ ACC_FUSED ─ D1 SB360 ─┬─ VIN_PROTECTED ─ U1 TSR 1-2450 ─ +5V ─ U2 TPS7B8333 ─ +3.3V
                      └─ D2 TVS (GND)         └─ C1 / C2 (GND)

J1 BNC 75Ω ─ R1 75Ω 종단 ─ C9 AC 결합 ─ U3 TLV3601 (IN− = VREF 1.65V) ─ R5 ─ U4 MAX3295
U4 Y ─ R7 120Ω ─ R10 0Ω ─ FL1 1→4 ─┬─ SPDIF+ ─ J2
                                   R9 56Ω (CMC 뒤, 커넥터 쪽)
U4 Z ─ R8 120Ω ─ R11 0Ω ─ FL1 2→3 ─┴─ SPDIF− ─ J2
```

- 전원은 **ACC 12V만 사용**합니다. 상시 B+는 쓰지 않습니다 (차량 IBS 배터리 센서 학습 보호).
- 2-layer PCB, 부품은 전부 Top 실장, 일반 수동소자는 0805 (손납땜 기준).
- 외함: **케이스포유 RT-75 (RT-7511, 75 × 30.9 × 110 mm, 국산)** — 알루미늄 압출 본체(AL6061), 전·후면 판넬(AL5052), PCB 홈(1.6T). 결정 로그 #16 참고.
- 시험 샘플레이트: 우선 44.1 / 48 kHz. 192 kHz는 MAX3295 20 Mbps 한계로 보장하지 않습니다.

## 주요 부품

| Ref | 부품 | 패키지 | 역할 |
|---|---|---|---|
| U1 | TRACO **TSR 1-2450** | SIP-3 THT | 12V → 5V 스위칭 모듈 |
| U2 | TI **TPS7B8333QDCYRQ1** | SOT-223 (DCY) | 5V → 3.3V LDO — **8330/8350 아님** |
| U3 | TI **TLV3601QDBVRQ1** | SOT-23-5 | 고속 비교기 (S/PDIF 정형) |
| U4 | ADI **MAX3295AUT+T** | SOT-23-6 | 20 Mbps 차동 드라이버 |
| D1 | Diotec **SB360** | DO-201 축형 | 직렬 역극성 보호 Schottky |
| D2 | Littelfuse **1.5KE22CA** | DO-201AE 축형 | 양방향 TVS (`ACC_FUSED`–GND 병렬, D1 앞) |
| F1 | Keystone **3557-2** 홀더 + Littelfuse **0891002.NXS** | THT | 2A LP Mini 블레이드 퓨즈 |
| FL1 | TDK **ACM2012-900-2P-T001** | 2012 SMD | 차동 출력 공통모드 초크 (후보) |
| J1 | Kinghelm **KH-BNC75-3511** | THT | 75Ω BNC 입력 |
| J2 | 3핀 XH 계열 직각형 | THT | `SPDIF+ / SPDIF− / GND` 출력 (정확한 MPN 미정) |
| J3 | JST **B2PS-VH** (+ VHR-2N / SVH-21T-P1.1) | THT 3.96mm | `ACC 12V / GND` 입력 |

핀아웃은 `references/datasheets/`의 데이터시트 원문으로 대조했습니다. 상세 내용은 `AGENTS.md`의 "검증된 핀아웃"을 보세요.

## 실장 구성 (FIT / DNP)

| 구분 | 부품 | 기본 상태 | 비고 |
|---|---|---|---|
| 극성 선택 | R10 (Y→+), R11 (Z→−) | **FIT** | 순정 극성 |
| 극성 선택 | R12 (Y→−), R13 (Z→+) | DNP | 반전 시험 시 R10/R11 대신 실장. **4개 동시 실장 금지** (Y/Z 쇼트) |
| CMC 바이패스 | R14 (FL1 1↔4), R15 (FL1 2↔3) | DNP | FL1 제거 시에만 실장 |
| 히스테리시스 | R6 R_HYS | DNP, Value `TBD` | **0Ω 실장 금지** (비교기 래치) |
| EMI 커패시터 | C11~C14 | DNP, Value `TBD` | OEM C593/C433/C430/C431 대응 |
| DE | R16 0Ω → +3.3V | **FIT** | 지연 enable 회로는 미설계 |

## 외함 (케이스포유 RT-75)

|항목|내용|
|---|---|
|모델|RT-7511: 전체 75 × 30.9 × 110 mm (본체 74.9 × 30.5 × 107, 판넬 1.5 mm). RT-7590(본체 87 mm)도 같은 단면|
|구매|[케이스포유](https://case4u.co.kr/product/detail.html?product_no=135) 직판 (24,200원, 부가세 포함), 디바이스마트 입점|
|PCB|내부 홈(1.6T, 홈 바닥 간 69.7 mm)에 끼움 → **PCB 외형 69.5 × 100.0 mm** (JLCPCB 특가 한도 102 mm, 결정 로그 #18). 앞 가장자리는 본체 앞끝, 뒤쪽 약 7 mm는 빈 공간. PCB 아래 4.5 mm, 위 약 19.4 mm|
|앞판|J1 BNC 나사부가 관통해 너트로 고정 (D형 Ø12.8 추정, 데이터시트 재확인). 결정 로그 #17|
|뒷판 (권장, 미확정)|하네스용 그로밋/PG7 글랜드 1개. J2·J3은 케이스 안쪽 PCB 위에 두고 판넬에 사각 구멍을 내지 않음|
|고정 (미확정)|플랜지 없음 → 벨크로·양면테이프 + 케이블타이, 또는 L브래킷|
|가공·마킹·실크|**PCB 배치 확정 → 시제품 조립으로 맞춤 확인 → 그 후** 직접 가공 또는 케이스포유에 구멍 가공·레이저 마킹·실크 인쇄 의뢰|

## PCB 전에 남은 결정

- 케이스 접지 정책 (BNC 나사부로 케이스=GND 연결 여부), J1 쉴드–GND 0Ω 옵션 패드
- C1 전해 커패시터 실물 MPN과 THT 풋프린트 (현재 0805 임시)
- J2 XH 커넥터 실물 확정 (정품 2.50mm인지, 호환품 2.54mm인지)
- J1 KH-BNC75-3511 전용 풋프린트 제작 (현재 Amphenol 50Ω 임시, 실물은 직각 PCB 실장 + 패널 너트 고정형)
- MLCC·저항 구매 MPN, 10µF MLCC의 DC bias 실효 용량

미완료 검증: DUDU7 실측 전압, AMP 쪽 종단·입력 레벨, OEM F400 대비 CMC 적합성, 실차 팝노이즈(DE), 무입력 발진(R_HYS).

PCB 작업 인계: [`docs/DUDU7_i40_SPDIF_TX_Rev1_PCB_Mechanical_Handoff_20261113.md`](docs/DUDU7_i40_SPDIF_TX_Rev1_PCB_Mechanical_Handoff_20261113.md)

## 폴더 구조

```
HYK_AMP_Converter/
├─ HYK_AMP_Converter.kicad_pro / .kicad_sch / .kicad_pcb   ← KiCad 10 프로젝트
├─ sym-lib-table / fp-lib-table      ← 프로젝트 라이브러리 등록 (깃 관리 대상)
├─ libraries/
│  ├─ symbols/      ← 3557-2, ACM2012H-900-2P-T00, MAX3295AUT-V-T, SB360_diode,
│  │                   TLV3601QDBVRQ1, TPS7B8350QDCYRQ1, TSR_1-2450
│  └─ footprints/   ← 위 부품의 .pretty (U2·U3·U4는 손납용 -L 변종 사용)
├─ docs/
│  ├─ DUDU7_i40_SPDIF_TX_Rev1_BOM_for_KiCad_Agent.md               ← ★ 최우선 사양 + Decision log
│  ├─ DUDU7_i40_SPDIF_TX_Rev1_PCB_Mechanical_Handoff_20261113.md   ← ★ PCB·외함 인계 (다음 작업 시작점)
│  ├─ DUDU7_i40_SPDIF_TX_Rev1_Schematic_Placement_and_Wiring.md/.html  ← 배치·배선 가이드
│  └─ archive/                                                      ← 지난 문서(Rev.A·Rev.A2·초기 감사). 이력용, 평소엔 읽지 않음
├─ references/datasheets/   ← 핀아웃 검증용 제조사 데이터시트 원문
├─ references/case_file/    ← 케이스포유 RT-75 도면 zip (로컬 전용, .gitignore — 저작권). 원본: [케이스포유 자료실 RT-75](https://www.case4u.co.kr/board/free/read.html?no=1060&board_no=7)
├─ AGENTS.md                ← AI 에이전트 작업 지침 (핀아웃·회로 규칙·사고 이력)
└─ .gitignore
```

## 시작하기

1. KiCad **10.0 이상**을 설치합니다 (작업 환경: 10.0.8).
2. 이 저장소를 클론합니다.
3. `HYK_AMP_Converter.kicad_pro`를 엽니다. 라이브러리가 프로젝트에 내장되어 있어(`sym-lib-table` / `fp-lib-table`) 추가 설정 없이 바로 쓸 수 있습니다.

## MCP 자동화 (선택)

서버는 [mixelpixx/KiCAD-MCP-Server](https://github.com/mixelpixx/KiCAD-MCP-Server) (v2.9.0, MIT)를 씁니다. 저장소 밖에 설치하고, 클라이언트 설정 파일은 이 저장소에 두지 않습니다 (경로가 PC마다 다름).

- 필요 도구: KiCad 10 (내장 Python 3.11 + `pcbnew`), Node.js 18 이상, Git
- 설치 (Windows PowerShell):

  ```powershell
  git clone --branch stable https://github.com/mixelpixx/KiCAD-MCP-Server.git $HOME\MCP\KiCAD-MCP-Server
  cd $HOME\MCP\KiCAD-MCP-Server
  .\setup-windows.ps1   # npm install + KiCad Python에 pip 의존성 설치 + 빌드 + windows-mcp-config.json 생성
  ```

  업데이트: 같은 폴더에서 `git pull` → `npm install` → `npm run build`
- 클라이언트 등록 (stdio). 생성된 `windows-mcp-config.json`을 기준으로 하되 `KICAD_MCP_OPERATING_MODE`를 추가합니다.

  ```json
  "kicad": {
    "command": "C:\\Program Files\\nodejs\\node.exe",
    "args": ["C:\\Users\\<USER>\\MCP\\KiCAD-MCP-Server\\dist\\index.js"],
    "env": {
      "PYTHONPATH": "C:\\Program Files\\KiCad\\10.0\\bin\\Lib\\site-packages",
      "KICAD_PYTHON": "C:\\Program Files\\KiCad\\10.0\\bin\\python.exe",
      "LOG_LEVEL": "info",
      "KICAD_MCP_OPERATING_MODE": "readonly"
    }
  }
  ```

  위치: omp `~/.omp/agent/mcp.json`, Claude Code `claude mcp add --scope user kicad --env ... -- node <dist\index.js>`, Claude Desktop `%APPDATA%\Claude\claude_desktop_config.json`
- 운용 모드 (`KICAD_MCP_OPERATING_MODE`)

  |값|도구 수 (v2.9.0 실측)|용도|
  |---|---:|---|
  |`readonly`|86|**기본값으로 사용.** 리뷰·ERC·넷리스트|
  |`manufacturing`|—|readonly + Gerber·BOM 등 출력. 사용자 승인 전 사용 금지|
  |`write` (또는 미설정)|250|회로도·PCB 편집. 작업할 때만 잠깐 전환|

  > ⚠️ 이 서버는 변수를 **설정하지 않으면 `write`(전체 쓰기)** 로 시작합니다. 반드시 `readonly`를 명시하세요.
- 도구가 많아 컨텍스트를 많이 씁니다. 줄이려면 `KICAD_MCP_TOOLBOXES=core`(또는 `schematic,drc` 등)를 추가합니다.
- PCB 실시간 연동(IPC)을 쓰려면 KiCad **Preferences → Plugins → Enable KiCad API**를 켜고 KiCad 창을 하나만 엽니다.
- KiCad 10은 회로도 IPC가 없습니다. MCP의 회로도 쓰기는 `.kicad_sch` 파일을 **직접 덮어쓰므로** 다음 순서를 지키세요.
  1. GUI에서 저장하고 회로도 편집기를 닫습니다.
  2. `backup/`에 백업합니다.
  3. 요청한 범위만 수정합니다.
  4. diff를 확인합니다.

  자세한 내용은 `AGENTS.md`에 있습니다.

## 라이선스

[The Unlicense](LICENSE) (퍼블릭 도메인)
