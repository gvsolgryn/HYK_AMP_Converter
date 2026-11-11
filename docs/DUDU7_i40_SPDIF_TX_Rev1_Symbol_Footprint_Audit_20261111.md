# DUDU7 / FYT7862 → Hyundai i40 S/PDIF TX Rev.1
## Section 1.1 Symbol / Footprint Audit — 2026-11-11

### 1. 목적과 범위

Rev.1 회로도 배치 가이드의 **1.1 IC·보호부품·CMC·커넥터** 항목과, 사용자가 온라인에서 새로 설치한 심볼·풋프린트를 실제 프로젝트 파일 기준으로 대조했다.

검토 대상:

- 현재 `HYK_AMP_Converter.kicad_sch`에 배치된 U1, U2, U3, U4, D1, D2, F1
- 새로 추가된 TSR 1-2450, SB360, Keystone 3557-2 심볼과 풋프린트
- `fp-lib-table`, `sym-lib-table`의 라이브러리 닉네임
- Rev.1 HTML 가이드의 KiCad 공용 심볼 사용 안내

이 문서는 핀 번호·라이브러리 연결에 대한 검토 기록이며, 회로의 전기적 동작이나 제조 적합성을 승인하는 문서가 아니다.

---

## 2. 현재 회로도 점검 결과

| 부품 | 확인 결과 | 필요한 수정 / 판정 |
|---|---|---|
| **U1 TSR 1-2450** | 공용 `Converter_DCDC:TSR_1-2450`의 `1=VIN`, `2=GND`, `3=VOUT` 확인 | 현재 공용 심볼과 기본 TSR-1 풋프린트 조합 사용 가능 |
| **U2 TPS7B8333** | 프로젝트 심볼 `1=IN`, `2=GND`, `3=OUT`, `4=GND`; 전용 풋프린트에 패드 1~4 존재 | Value `TPS7B8333QDCYRQ1`과 `UL_FP_TPS7B83:SOT_8350QDCYRQ1_TEX-L` 사용 가능 |
| **U3 TLV3601** | `1=OUT`, `2=GND`, `3=IN+`, `4=IN−`, `5=VCC` 확인 | 현재 심볼·풋프린트 사용 가능 |
| **U4 MAX3295** | 핀 배정은 `1=DI`, `2=VCC`, `3=DE`, `4=Z`, `5=GND`, `6=Y`로 맞지만 현재 Value는 `MAX3295AUT/V+T` | Rev.1 기준 **`MAX3295AUT+T`**로 Value 변경 필요 |
| **D1 SB360** | 프로젝트 심볼 `1=A`, `2=C/K`; `SB360:P2_DO-201`의 패드 1/2와 대응 | 현재 연결 매핑 사용 가능. 공용 `Device:D_Schottky`도 사용 가능 |
| **D2 TVS** | 공용 `Diode:1.5KExxCA`는 양방향 TVS이며 `1=A1`, `2=A2`; 현재 DO-201AE 풋프린트 사용 가능 | Value를 포괄값 `1.5KExxCA`에서 정확한 **`1.5KE22CA`**로 변경 필요 |
| **F1 / FH1** | 홀더 심볼의 `1_1/1_2`, `2_1/2_2`와 새 풋프린트 패드 번호 일치 | 실제 퓨즈와 홀더를 구분해 Value/MPN은 `0891002.NXS`, 별도 속성 `HOLDER_MPN=3557-2`로 지정 |
| **F1 Footprint** | 현재 회로도는 `3557-2:FUSE_3557-2`, 등록된 라이브러리 닉네임은 `FUSE_3557-2` | **`FUSE_3557-2:FUSE_3557-2`**로 변경 필요 |
| **L1, J1, J2, J3** | 현재 회로도에 미배치 | HTML 가이드의 공용 심볼 안내를 사용해 추가 |

현재 회로도의 U4, D2, F1 값과 F1 Footprint 필드는 사용자 작업을 보존하기 위해 이번 검토에서 직접 변경하지 않았다. 위 표의 수정 사항은 HTML 1.1 앞의 경고 상자에도 기록했다.

---

## 3. 새로 추가된 파일 검증

### 3.1 TSR 1-2450

검증 파일:

- `libraries/symbols/TSR_1-2450.kicad_sym`
- `libraries/footprints/CONV_TSR_1-2450.pretty/CONV_TSR_1-2450.kicad_mod`

핀·패드 대응:

| 신호 | 심볼 핀 | 풋프린트 패드 |
|---|---:|---:|
| +VIN | 1 | 1 |
| GND | 2 | 2 |
| +VOUT | 3 | 3 |

핀 번호와 패드는 일치한다. 다운로드 심볼의 기본 Footprint는 `TSR_1-2450:CONV_TSR_1-2450`이었으나 프로젝트 등록 닉네임은 `CONV_TSR_1-2450`이므로, 심볼 기본값을 다음으로 수정했다.

```text
CONV_TSR_1-2450:CONV_TSR_1-2450
```

현재 회로도에 배치된 공용 `Converter_DCDC:TSR_1-2450`도 같은 핀 배정을 가지므로, 프로젝트 심볼로 교체할 필요는 없다.

### 3.2 SB360

검증 파일:

- `libraries/symbols/SB360_diode.kicad_sym`
- `libraries/footprints/SB360.pretty/P2_DO-201.kicad_mod`

| 기능 | 심볼 핀 | 풋프린트 패드 |
|---|---:|---:|
| Anode | 1 | 1 |
| Cathode | 2 | 2 |

핀·패드 매핑은 일치한다. 현재 프로젝트 심볼은 일반 다이오드 형태이므로, 회로도 가독성을 위해 공용 `Device:D_Schottky`를 사용하고 Value를 `SB360`으로 지정하는 방법도 가능하다.

### 3.3 Keystone 3557-2 fuseholder

검증 파일:

- `libraries/symbols/3557-2.kicad_sym`
- `libraries/footprints/FUSE_3557-2.pretty/FUSE_3557-2.kicad_mod`

| 전기 경로 | 심볼 핀 | 풋프린트 패드 |
|---|---|---|
| 접점 1 | `1_1`, `1_2` | `1_1`, `1_2` |
| 접점 2 | `2_1`, `2_2` | `2_1`, `2_2` |

핀·패드 매핑은 일치한다. 다운로드 심볼의 기본 Footprint는 `3557-2:FUSE_3557-2`였으나 프로젝트 등록 닉네임은 `FUSE_3557-2`이므로, 심볼 기본값을 다음으로 수정했다.

```text
FUSE_3557-2:FUSE_3557-2
```

퓨즈와 홀더는 서로 다른 구매 부품이므로 다음처럼 속성을 구분하는 것이 BOM상 명확하다.

```text
Reference: F1
Value: 0891002.NXS
MPN: 0891002.NXS
HOLDER_MPN: 3557-2
Footprint: FUSE_3557-2:FUSE_3557-2
```

---

## 4. HTML에 추가한 공용 심볼 안내

대상 문서:

- `docs/DUDU7_i40_SPDIF_TX_Rev1_Schematic_Placement_and_Wiring.html`

`1.0 KiCad 공용 심볼 사용 방법`과 현재 회로도 수정 대상 경고를 추가했다. 공용 심볼 사용 시 **심볼 핀 번호, Value의 실제 MPN, Footprint의 실제 패키지**를 각각 확인하도록 안내한다.

| 대상 | 안내한 심볼 |
|---|---|
| U1 | `Converter_DCDC:TSR_1-2450` |
| U2 | `symbols:TPS7B8350QDCYRQ1` + Value를 `TPS7B8333QDCYRQ1`로 변경 |
| U3 | `symbols:TLV3601QDBVRQ1` |
| U4 | `symbols:MAX3295AUT_V+T` + Value를 `MAX3295AUT+T`로 변경 |
| D1 | `Device:D_Schottky` |
| D2 | `Diode:1.5KExxCA` 또는 `Device:D_TVS` + Value를 `1.5KE22CA`로 변경 |
| F1/FH1 | `symbols:3557-2` 또는 `Device:Fuse` |
| L1 | `Device:Filter_EMI_LL_1423`, 실제 채널이 1→4 및 2→3일 때만 사용 |
| J1 | `Connector:Conn_Coaxial` |
| J2 | `Connector_Generic:Conn_01x03` |
| J3 | `Connector_Generic:Conn_01x02` |
| C1 | `Device:C_Polarized` |
| C2~C14 | `Device:C` |
| R1~R16 | `Device:R` |
| TP1~TP13 | `Connector:TestPoint` |
| 전원 | `power:+5V`, `power:+3V3`, `power:GND` |

CMC의 공용 심볼은 실제 권선과 풋프린트 채널이 `1→4`, `2→3`이라는 조건에서만 사용한다. 커넥터의 공용 심볼도 실제 패드 번호·핀 순서·pitch를 별도로 확인해야 한다.

---

## 5. 확인한 화면 동작

수정한 HTML을 실제 Chromium에서 열어 다음을 확인했다.

- 공용 심볼 안내 16개 행 표시
- 기존 부품·테스트포인트 체크박스 54개 유지
- 현재 회로도 수정 대상 경고 표시
- 데스크톱 화면 가로 넘침 없음
- 모바일 화면 가로 넘침 없음
- 배치 진행률 `0 / 54` 정상 표시

---

## 6. 관련 커밋과 남은 작업

관련 커밋:

```text
e88238a Add Rev.1 schematic guide and component libraries
```

해당 커밋에는 Rev.1 BOM, 배치·배선 MD/HTML, 새 심볼·풋프린트·STEP·데이터시트와 라이브러리 등록 파일이 포함됐다. HTML도 커밋에 포함된 것을 확인했다.

다음은 현재 회로도에서 사용자가 적용할 수정 사항이다.

1. U4 Value를 `MAX3295AUT+T`로 변경
2. D2 Value를 `1.5KE22CA`로 변경
3. F1 Value/MPN을 `0891002.NXS`로 변경하고 `HOLDER_MPN=3557-2` 추가
4. F1 Footprint를 `FUSE_3557-2:FUSE_3557-2`로 변경
5. L1, J1, J2, J3 배치
6. L1 및 커넥터의 실제 패드 번호와 핀 순서를 제조사 도면으로 확인

기존 `HYK_AMP_Converter.kicad_pro`와 `HYK_AMP_Converter.kicad_sch`의 사용자 변경은 관련 리소스 커밋에서 제외했다. Git push는 저장소 지침에 따라 사용자가 수행한다.
