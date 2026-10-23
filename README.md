# HYK_AMP_Converter — DUDU7 → Hyundai i40 순정 AMP S/PDIF 변환기

Android 헤드유닛(DUDU7)의 RCA Coaxial S/PDIF 출력을 현대 i40 순정 AMP(부품번호 `96370-3Z101`)의 차동 SPDIF 입력(`SPDIF+ / SPDIF− / SPDIF GND`)에 연결하는 **능동 물리계층 변환기**입니다.
S/PDIF 신호를 디코딩하지 않고, 75Ω 동축 신호를 정형하여 순정 AMP가 기대하는 차동 전기 신호로 변환합니다.

> ⚠️ **제작 승인(Production Release) 문서가 아닙니다.**
> 회로·부품·핀 검증과 차량 시험이 완료되기 전까지 Gerber 발주와 부품 주문을 하지 마세요.
> 모든 설계 기준은 `docs/DUDU7_i40_SPDIF_KiCad_Handoff_RevA2_20261016.md`(Rev.A2)가 최우선입니다.

## 현재 상태

- 회로도 작성 중 (Rev.A2 기준) — U1~U4 IC 배치 완료
- 시그널 체인: 75Ω BNC → TLV3601-Q1 비교기 → MAX3295 차동 드라이버 → OEM AMP
- 전원: ACC 12V → 5V Buck(LMR16006YQ5) → 3.3V LDO(TPS7B8333-Q1)
- 상시 B+ 미사용 — 차량 IBS(배터리 센서) 학습 보호

## 주요 부품

| Ref | 부품 | 역할 |
|---|---|---|
| U4 | LMR16006YQ5DDCRQ1 | 차량용 5V Buck (2.1MHz) |
| U1 | TPS7B8333QDCYRQ1 | 3.3V LDO 150mA — **8330/8350 아님** |
| U2 | TLV3601QDBVRQ1 | 고속 비교기 (S/PDIF 정형) |
| U3 | MAX3295AUT/V+T | 20Mbps 차동 드라이버 |
| FL1 | TDK ACM2012-900-2P-T002 (후보) | 2라인 공통모드 초크 |

## 폴더 구조

```
HYK_AMP_Converter/
├─ HYK_AMP_Converter.kicad_pro / .kicad_sch   ← KiCad 10 프로젝트
├─ sym-lib-table / fp-lib-table               ← 프로젝트 라이브러리 등록 (깃 관리 대상)
├─ libraries/
│  ├─ symbols/        ← UL 심볼 3종 (.kicad_sym)
│  └─ footprints/     ← UL 풋프린트 3종 (.pretty, L/M/N 변종)
├─ docs/                    ← 설계 인수인계 문서 (Rev.A / Rev.A2)
├─ references/datasheets/   ← 핀아웃 검증용 제조사 데이터시트 원문
├─ opencode.json            ← OpenCode MCP(kicad-mcp-pro) 자동화 설정
└─ .gitignore
```

## 시작하기

1. KiCad **10.0 이상** 설치
2. 이 저장소 클론
3. `HYK_AMP_Converter.kicad_pro` 열기 — 라이브러리가 프로젝트 내장(`sym-lib-table`/`fp-lib-table`)이라 **추가 설정 없이 즉시 사용 가능**

## MCP 자동화 (선택)

OpenCode 사용 시 `opencode.json`이 `uvx kicad-mcp-pro` 서버를 자동 연결합니다.
- 필요 도구: [`uv`](https://docs.astral.sh/uv/) (설치 후 `uvx kicad-mcp-pro` 1회 실행으로 캐시)
- 다른 PC에서 `uvx`가 PATH에 없으면 `opencode.json`의 command 경로를 해당 PC에 맞게 수정

## 라이선스

미정 (개인 프로젝트)
