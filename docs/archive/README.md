# docs/archive — 지난 문서 (기본적으로 읽지 않음)

이 폴더의 문서는 **이전 단계의 기록**이다. 부품·전원 구조·참조 번호·핀 배정·외함이 현재와 다르다.
작업할 때는 아래 **현행 문서**만 읽는다. 여기 문서는 "왜 그렇게 정했는지" 이력을 찾을 때만 연다.
현행 문서와 충돌하면 **항상 현행 문서가 우선**이다.

## 현행 문서 (여기서 시작)

| 문서 | 용도 |
|---|---|
| `AGENTS.md` | 작업 규칙, 검증된 핀아웃, 현재 상태 |
| `docs/DUDU7_i40_SPDIF_TX_Rev1_BOM_for_KiCad_Agent.md` | Rev.1 사양 + **§9 Decision log (#1~#20)** — 최우선 기준 |
| `docs/DUDU7_i40_SPDIF_TX_Rev1_PCB_Mechanical_Handoff_20261113.md` | PCB·외함 인계, 다음 작업 순서. 옛 문서에서 유효한 내용은 §8에 옮겨 둠 |
| `docs/DUDU7_i40_SPDIF_TX_Rev1_Schematic_Placement_and_Wiring.md/.html` | 회로도 연결 가이드 (결정 로그와 동기화됨) |

## 보관 문서

| 문서 | 시점 | 현재와 다른 점 | 열어볼 때 |
|---|---|---|---|
| `DUDU7_i40_SPDIF_KiCad_HANDOFF_20261015.md` | Rev.A 초안 | LMR16006 Buck 전원, 1N4007·1.5KE27A 보호, U 번호 체계 다름, J2=전원/J3=출력, Hammond 케이스, BNC 피그테일 | OEM 회로 분석 원문, 초기 요구사항 배경 |
| `DUDU7_i40_SPDIF_KiCad_Handoff_RevA2_20261016.md` | Rev.A2 | 위와 같은 Buck 전원·U 번호, 단방향 TVS가 다이오드 뒤, Hammond 케이스, 양면테이프 장착 | OEM 출력단 실측 근거(§2), 극성 매트릭스 도입 배경(§4), 시험 게이트 원문(§10) — 요약은 인계 문서 §8 |
| `DUDU7_i40_SPDIF_TX_Rev1_Symbol_Footprint_Audit_20261111.md` | Rev.1 초기 | D1 심볼 1=A(현재 1=K), L1 명칭(현재 FL1), 미배치 부품 목록 | 심볼·풋프린트 출처 이력 |

`references/datasheets/archive/LMR16006Y-Q1.pdf` — Rev.A2 Buck 데이터시트. Rev.1에서 사용하지 않음.
