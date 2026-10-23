# AGENTS.md — AI 에이전트 작업 지침

이 파일은 이 저장소에서 작업하는 AI 에이전트(OpenCode, Claude Code 등)를 위한 지침입니다.

## 프로젝트 개요

- **DUDU7 Android 헤드유닛의 RCA Coaxial S/PDIF 출력 → Hyundai i40 순정 AMP(`96370-3Z101`) 차동 SPDIF 입력** 변환기
- 물리계층 어댑터: S/PDIF 프로토콜 디코딩 없이 75Ω 동축 신호를 정형해 차동 신호로 변환
- 차량 전원: **ACC 12V만 사용, 상시 B+ 금지** (IBS 배터리 센서 학습 보호)
- 외함: 기성 알루미늄 케이스(Hammond 1455C1201BK 후보), 2-layer PCB, 부품 전부 Top 실장

## 최우선 사양 문서

`docs/DUDU7_i40_SPDIF_KiCad_Handoff_RevA2_20261016.md` (**Rev.A2**) — 모든 설계 판단의 기준.
구버전(Rev.A, 20261015)과 충돌하면 **항상 Rev.A2 우선** (예: 출력 극성 `Y→SPDIF+ / Z→SPDIF−`, 직렬저항 120Ω).

## 검증된 핀아웃 (제조사 데이터시트 원문 대조 완료, 2026-10-23)

| IC | 핀 배열 |
|---|---|
| U4 LMR16006YQ5 (SOT-23-6) | 1=CB · 2=GND · 3=FB · 4=SHDN · 5=VIN · 6=SW |
| U1 TPS7B8333-Q1 (SOT-223) | 1=IN · 2=GND · 3=OUT · 4=TAB=GND |
| U2 TLV3601-Q1 (SOT-23-5) | 1=OUT · 2=VEE · 3=IN+ · 4=IN− · 5=VCC |
| U3 MAX3295 (SOT-23-6) | 1=DI · 2=VCC · 3=DE · 4=Z · 5=GND · 6=Y |
| FL1 ACM2012-900-2P (후보) | Line1 = 핀1→4 · Line2 = 핀2→3 |

- 원문 PDF: `references/datasheets/`
- **온라인 정보(AI 포함)와 데이터시트가 다륾면 데이터시트가 항상 정답.**
  (실제 사례: TPS7B8333이 `1=OUT, 3=IN`으로 잘못 알려진 적 있음 — 실제는 `1=IN, 3=OUT`)

## 회로 규칙 (Rev.A2 요약)

- 고정 극성, 점퍼 매트릭스 없음: `U3 Y(6번)→R420대응 120Ω→FL1→SPDIF+`, `Z(4번)→R419대응 120Ω→FL1→SPDIF−`
- 차동 병렬저항 56Ω (R423 대응)은 **CMC 뒤 커넥터 쪽**에 배치
- EMI 커패시터 `C593 / C430 / C431 / C433` = **초기 DNP** (풋프린트만 유지)
- 디커플링 `C4 / C5 / C6 / C13` = **필수 장착** (DNP 아님)
- Buck D3 쇼트키: **K=SW, A=GND** (역방향 금지)
- LMR16006Y 고정 5V형: FB를 +5V_BUCK에 **직접** 연결 (분압저항 없음), SHDN은 VIN 직결
- 테스트 샘플레이트: 우선 44.1/48kHz (192kHz는 MAX3295 20Mbps 한계로 미보장)

## 치명적 실수 이력 (재발 금지)

1. **U1 Value 오타**: `TPS7B8330`(3.0V)·`TPS7B8350`(5.0V)이 아니라 **`TPS7B8333QDCYRQ1`** (3.3V)
2. AMS1117식 SOT-223 핀 규약(1=GND, 2=OUT, 3=IN)을 TPS7B83에 적용하지 말 것 — 핀 배정이 다름
3. MAX3295의 Y/Z 극성, TLV3601의 IN+/IN− 순서 혼동 주의

## 라이브러리 구성 (프로젝트 내장)

- 심볼: 닉네임 `symbol` → `${KIPRJMOD}/libraries/symbols` (UL에서 다운로드, 핀 검증 완료)
- 풋프린트: 닉네임 `UL_FP_MAX3295` / `UL_FP_TLV3601` / `UL_FP_TPS7B83` → `libraries/footprints/*.pretty`
- 손납대 기준 **-L(대패드) 변종** 권장
- 프로젝트 라이브러리 등록은 `sym-lib-table` / `fp-lib-table`로 관리 — **깃에 반드시 포함**

## MCP 자동화 환경

- `opencode.json` → `uvx kicad-mcp-pro` (프로필 `full`, 모드 `experimental`)
- KiCad **10.0.6** + `kicad-cli` 자동 감지 (GUI 실행 없이 파일 기반 작업 가능)
- 주요 도구: `sch_get_symbols`, `run_erc`, `export_sch_pdf`, `sch_build_circuit` 등 332개
- 작업 시작 전 `kicad_get_project_info`로 프로젝트 연결 확인

## 작업 규칙

1. 회로·핀 정보를 바꾸기 전 `references/datasheets/` 원문 확인
2. ERC/DRC 통과 ≠ 동작 보장 (Rev.A2 §10 검증 게이트 준수)
3. **사용자 승인 없이 Gerber/제조 파일 생성 금지, 부품 구매 지시 금지**
4. git push는 사용자가 직접 수행 (에이전트는 커밋까지)
5. `.kicad_sch`/`.kicad_pcb`를 텍스트로 직접 수정할 경우: 사전 백업 + KiCad 10 포맷 준수
6. 회로도 배포 전 `run_erc` 실행 및 결과 리포트

## 현재 진행 상태

- 회로도: U1~U4 배치 완료, 수동부품 배치·배선 진행 예정
- 미완료 검증(Rev.A2 §11): DUDU7 실측 전압, AMP 남부 종단/입력 레벨, F400 정확한 대체 부품, 케이스 기구
