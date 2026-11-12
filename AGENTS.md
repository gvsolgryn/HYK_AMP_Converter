# AGENTS.md — AI 에이전트 작업 지침

이 파일은 이 저장소에서 작업하는 AI 에이전트(Claude Code 등)를 위한 지침입니다.

## 프로젝트 개요

- **DUDU7 Android 헤드유닛의 RCA Coaxial S/PDIF 출력 → Hyundai i40 순정 AMP(`96370-3Z101`) 차동 SPDIF 입력** 변환기
- 물리계층 어댑터: S/PDIF 프로토콜 디코딩 없이 75Ω 동축 신호를 정형해 차동 신호로 변환
- 차량 전원: **ACC 12V만 사용, 상시 B+ 금지** (IBS 배터리 센서 학습 보호)
- 외함: **케이스포유 RT-75 (RT-7511, 75×31×110mm, 국산 알루미늄 압출, PCB 홈 1.6T)** — 결정 로그 #16. 2-layer PCB, 부품 전부 Top 실장

## 최우선 사양 문서

`docs/DUDU7_i40_SPDIF_TX_Rev1_BOM_for_KiCad_Agent.md` (**Rev.1**, 2026-11-11 채택) — 모든 설계 판단의 기준.
- Rev.1은 전원단을 **TRACO TSR 1-2450 모듈**로 바꾼 버전. Rev.A2(`docs/DUDU7_i40_SPDIF_KiCad_Handoff_RevA2_20261016.md`)의 LMR16006 Buck 회로는 **사용하지 않음**
- 출력 극성·토폴로지(`Y→SPDIF+ / Z→SPDIF−`, 120Ω 직렬, CMC 뒤 56Ω)는 Rev.A2와 동일
- 확정 결정 기록: BOM 문서 §9 "Decision log" (#1~#17)
- **다음 작업(PCB) 인계 문서: `docs/DUDU7_i40_SPDIF_TX_Rev1_PCB_Mechanical_Handoff_20261113.md`** — 외함 치수, J1 BNC 장착, PCB 제약, 작업 순서, 미결 항목. PCB 작업 전에 먼저 읽을 것

## 검증된 핀아웃 (Rev.1 Reference 기준, 데이터시트 원문 대조 완료)

| Ref / IC | 핀 배열 |
|---|---|
| U1 TSR 1-2450 (SIP-3, THT) | 1=+Vin · 2=GND · 3=+Vout |
| U2 TPS7B8333QDCYRQ1 (SOT-223 DCY) | 1=IN · 2=GND · 3=OUT · 4=TAB=GND |
| U3 TLV3601QDBVRQ1 (SOT-23-5) | 1=OUT · 2=VEE · 3=IN+ · 4=IN− · 5=VCC |
| U4 MAX3295AUT+T (SOT-23-6) | 1=DI · 2=VCC · 3=DE · 4=Z · 5=GND · 6=Y |
| FL1 ACM2012-900-2P-T001 | Line1 = 핀1↔4 · Line2 = 핀2↔3 (TDK 데이터시트 Circuit Diagram 확인, 무극성) |
| D1 SB360 (DO-201 축형) | 심볼 `Device:D` 1=K · 2=A ↔ 풋프린트 `SB360:P2_DO-201` 패드1(사각, 띠)=K |
| F1 Keystone 3557-2 | 핀 1_1·1_2 = 한쪽, 2_1·2_2 = 반대쪽 → **각 쌍을 함께 배선** |
| J1 KH-BNC75-3511 | 1=중심(In) · 2=GND(Ext) |

- 원문 PDF: `references/datasheets/`
- **온라인 정보(AI 포함)와 데이터시트가 다르면 데이터시트가 항상 정답.**
  (실제 사례: TPS7B8333이 `1=OUT, 3=IN`으로 잘못 알려진 적 있음 — 실제는 `1=IN, 3=OUT`)
- 심볼 핀 번호와 풋프린트 패드 번호의 **극성 의미**까지 대조할 것 (D1 사례: 심볼 1=A, 풋프린트 1=K로 반대였음)

## 회로 규칙 (Rev.1 요약)

- 전원: ACC 12V → J3 → F1(퓨즈홀더) → `ACC_FUSED` [D2 TVS 병렬] → D1 SB360(직렬) → `VIN_PROTECTED` [C1·C2] → U1 TSR(5V) → U2 LDO(+3.3V). **D2는 D1 앞** (결정 로그 #14)
- 전원 Net 이름: `+5V`, `+3.3V` (KiCad 기본 전원 심볼). TP3 Value `+3.3V_TP`는 표시용 이름일 뿐, 별도 라벨 금지
- 출력: `U4 Y(6)→R7 120Ω→극성선택 0Ω→FL1 1→4→SPDIF+`, `U4 Z(4)→R8 120Ω→극성선택 0Ω→FL1 2→3→SPDIF−`
- R9 56Ω 차동 병렬저항은 **CMC 뒤(FL1 3·4번 쪽) 커넥터 쪽**에 배치
- 극성선택 0Ω: R10(Y→+)·R11(Z→−) 실장, **R12·R13 역방향 DNP** — 4개 동시 실장 시 Y/Z 쇼트
- CMC 바이패스 R14(1↔4)·R15(2↔3) = DNP. 1↔3 / 2↔4로 걸면 SPDIF+/− 쇼트
- DE: R16 0Ω으로 +3.3V 직결 (지연 enable 회로는 미설계, 임의 RC 금지)
- R6 R_HYS: 풋프린트만, Value `TBD`, DNP. **0Ω 실장 금지** (U3 OUT→IN+ 직결 = 래치, 신호 소실). TLV3601 내부 히스테리시스 1.5~5mV
- EMI 커패시터 C11~C14 (OEM C593/C430/C431/C433 대응) = Value `TBD`, DNP
- 테스트 샘플레이트: 우선 44.1/48kHz (192kHz는 MAX3295 20Mbps 한계로 미보장)

## 치명적 실수 이력 (재발 금지)

1. **U2 Value 오타**: `TPS7B8330`(3.0V)·`TPS7B8350`(5.0V)이 아니라 **`TPS7B8333QDCYRQ1`** (3.3V). 풋프린트는 동일 DCY 패키지인 `TPS7B8350QDCYRQ1` 라이브러리 사용
2. AMS1117식 SOT-223 핀 규약(1=GND, 2=OUT, 3=IN)을 TPS7B83에 적용하지 말 것 — 핀 배정이 다름
3. MAX3295의 Y/Z 극성, TLV3601의 IN+/IN− 순서 혼동 주의
4. **MCP로 회로도 직접 수정 금지 사고 (2026-11)**: 라벨 추가 요청에서 배선·재배치·시트 변경까지 하고 롤백하며 사용자 라벨을 삭제함. 요청 범위 밖 수정 금지, 수정 전 백업 필수
5. `ACM2012-900-2P-T001` UL 풋프린트(`SMT_0-2P-T001_TDK*`)는 피치 1.0mm로 **데이터시트(0.8mm)와 불일치** → 등록 해제함. FL1은 `ACM2012H-900-2P-T00:ACM20121022PT001` 사용 (TDK 권장 랜드패턴과 일치)

## 라이브러리 구성 (프로젝트 내장)

- 심볼/풋프린트 모두 **파일별 닉네임**으로 `sym-lib-table` / `fp-lib-table`에 등록 (`${KIPRJMOD}/libraries/...`)
  - 예: `TPS7B8350QDCYRQ1`, `TLV3601QDBVRQ1`, `MAX3295AUT-V-T`, `TSR_1-2450`, `3557-2`, `ACM2012H-900-2P-T00`, `FUSE_3557-2`, `SB360`, `CONV_TSR_1-2450`
  - 구 닉네임 `UL_FP_*`도 같은 폴더를 가리키는 중복 등록으로 남아 있음
- 손납대 기준 **-L(대패드) 변종** 사용 (U2·U3·U4)
- 프로젝트 라이브러리 등록은 `sym-lib-table` / `fp-lib-table`로 관리 — **깃에 반드시 포함**

## MCP 자동화 환경

- 서버: [mixelpixx/KiCAD-MCP-Server](https://github.com/mixelpixx/KiCAD-MCP-Server) v2.9.0 (`~/MCP/KiCAD-MCP-Server`, stdio, `node dist/index.js`). 클라이언트 설정 파일은 저장소에 두지 않음 — 설치·등록 방법은 `README.md` "MCP 자동화" 참고
- KiCad **10.0.8** + `kicad-cli`. 회로도 IPC 없음 → MCP 회로도 쓰기(kicad-skip)는 **`.kicad_sch` 파일을 직접 덮어씀** (GUI 미저장 내용과 충돌, Ctrl+Z 불가). v2.9.0은 회로도가 KiCad에 열려 있으면 편집을 거부함
- **이 서버는 `KICAD_MCP_OPERATING_MODE` 미설정 시 전체 쓰기(`write`)** → 항상 `readonly`로 등록, 쓰기 필요 시에만 `write`로 바꾼 뒤 작업 후 되돌림. `manufacturing`(Gerber/BOM 출력)은 사용자 승인 전 금지
- 회로도 쓰기 전: GUI 저장 후 회로도 편집기 닫기(`~HYK_AMP_Converter.kicad_sch.lck` 없음 확인) → `backup/`에 타임스탬프 백업 → 요청 범위만 수정 → diff 보고
- 라벨·와이어 추가 후 ERC만 믿지 말고 `generate_netlist`(또는 `kicad-cli sch export netlist`)로 **핀 단위 넷 대조** (겹친 라벨로 인한 넷 쇼트는 ERC 경고로만 나올 수 있음)
- ERC 리포트는 `kicad-cli sch erc --output <TEMP>` 사용 권장 (프로젝트 폴더에 산출물 남기지 않기)

## 작업 규칙

1. 회로·핀 정보를 바꾸기 전 `references/datasheets/` 원문 확인
2. ERC/DRC 통과 ≠ 동작 보장 (Rev.A2 §10 검증 게이트 준수)
3. **사용자 승인 없이 Gerber/제조 파일 생성 금지, 부품 구매 지시 금지**
4. git push는 사용자가 요청한 경우에만 수행
5. `.kicad_sch`/`.kicad_pcb`를 텍스트로 직접 수정할 경우: 사전 백업 + KiCad 10 포맷 준수
6. 회로도 배포 전 `run_erc` 실행 및 결과 리포트

## 현재 진행 상태 (2026-11-13)

- 회로도: Rev.1 부품 54개 배치·**배선 완료**, 넷리스트 핀 단위 대조 일치, **ERC 0건** (PWR_FLAG: `VIN_PROTECTED` #FLG01, `GND` #FLG02)
- 회로도 레이아웃(2026-11-13 정리): 4개 블록(1 POWER / 2 INPUT / 3 DRIVER / 4 OUTPUT, 점선 프레임) · 블록 안은 와이어, 블록 간은 **로컬 라벨**(글로벌 라벨 미사용, 넷 이름은 `/ACC_IN` 형식) · 우하단 타이틀 블록(x>177, y>166) 영역 비움
- 커넥터 핀 배정(결정 로그 #15, "이름 순서 그대로, GND는 항상 마지막"): J3 `1=ACC_IN, 2=GND` · J2 `1=SPDIF_P, 2=SPDIF_N, 3=GND` — 하네스는 이 순서를 따름. 네모 패드 = 1번 표시일 뿐 GND 의미 아님
- 프로젝트 심볼 핀 타입 수정(2026-11-13): U2 `TPS7B8350QDCYRQ1` IN·GND=power_in, OUT=power_out / U4 `MAX3295AUT_V+T` DI·DE=input, Y·Z=output
- 저항 필드: Value=실제 값(몸체 안 세로 1.0mm), Description=역할 이름(R_TERM 등)
- PCB: **미착수**. 외형 **69.5 × 107.0 mm**(RT-7511 홈 폭 69.7, 본체 길이 107), PCB 아래 4.5 mm·위 약 19.4 mm, Bottom 부품 금지 — 세부는 인계 문서 §3
- J1 BNC: PCB 앞 가장자리에 두고 나사부를 앞판(1.5 mm)으로 관통해 너트 고정(결정 #17). **전용 풋프린트 제작 필요** (현재 Amphenol 50Ω 임시)
- PCB 전 미결: J1 풋프린트, C1 전해 THT 실물 MPN·풋프린트(현재 0805 임시), J2 정품 XH 확정, MLCC·저항 MPN, 케이스 접지 정책·J1 쉴드 0Ω 옵션, 뒷판 하네스 출구, 케이스 고정 방법
- 외함 가공(앞판 BNC 구멍, 뒷판 그로밋)·레이저 마킹·실크 인쇄는 **PCB 배치 확정 + 시제품 맞춤 확인 후** 진행. 위치 기준은 PCB의 J1 위치
- 케이스 도면 `references/case_file/RT_7511.zip`, `RT_7590.zip`은 **로컬 전용**(케이스포유 무단복제 금지 문구 + 공개 저장소라 `.gitignore`). 치수는 인계 문서에 기록됨. **원본 다운로드: [케이스포유 자료실 RT-75 TYPE PDF·DWG·STEP](https://www.case4u.co.kr/board/free/read.html?no=1060&board_no=7)** (회원 로그인 필요할 수 있음). PDF 렌더링은 KiCad Python의 PyMuPDF 사용
- 미완료 검증: DUDU7 실측 전압, AMP 남부 종단/입력 레벨, F400 대비 CMC 적합성, 실차 팝노이즈(DE)·무입력 발진(R_HYS)
