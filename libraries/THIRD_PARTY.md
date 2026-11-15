# 외부 출처 라이브러리 파일과 라이선스

이 저장소는 **PCB 설계 프로젝트**이며, 아래 외부 파일은 이 설계에 쓰는 부품에 한해 설계 파일의 일부로 포함한다. 라이브러리 단독 배포 목적이 아니다.
출처는 파일 안의 필드(`SOURCE_URL`, 필드 이름 형식, 풋프린트 이름 규칙)로 확인했다. **(추정)** 표시는 파일만으로 확정하지 못한 것이다.

| 파일 | 부품 | 출처 | 라이선스·조건 |
|---|---|---|---|
| `symbols/3557-2.kicad_sym`, `footprints/FUSE_3557-2.pretty/FUSE_3557-2.kicad_mod`, `footprints/FUSE_3557-2.pretty/3557-2.step` | Keystone 3557-2 퓨즈홀더 (F1) | SnapEDA (SnapMagic) — https://www.snapeda.com/parts/3557-2/Keystone/view-part/ (STEP은 (추정)) | CC BY-SA 4.0 + Design Exception 1.0 (출처 표기) |
| `symbols/TSR_1-2450.kicad_sym`, `footprints/CONV_TSR_1-2450.pretty/CONV_TSR_1-2450.kicad_mod`, `footprints/CONV_TSR_1-2450.pretty/TSR_1-2450.step` | TRACO TSR 1-2450 (U1) | SnapEDA — https://www.snapeda.com/parts/TSR%201-2450/Traco+Power/view-part/ (STEP은 TRACO 제공 모델 (추정)) | CC BY-SA 4.0 + Design Exception 1.0 |
| `3dmodels/B2PS-VH.STEP` | JST B2PS-VH (J3) | SnapEDA — https://www.snapeda.com/parts/B2PS-VH/JST%20Sales/view-part/ | CC BY-SA 4.0 + Design Exception 1.0 |
| `symbols/TPS7B8350QDCYRQ1.kicad_sym`, `footprints/TPS7B8350QDCYRQ1.pretty/*` | TI TPS7B83-Q1 DCY (U2) | Ultra Librarian (TI 제공) (추정: `_TEX`, `-L/-M` 이름 규칙) | Ultra Librarian EULA §2: 설계에 포함해 배포 허용(오픈소스 포함), 라이브러리로 재배포 금지, 저작권 표시 삭제 금지 |
| `symbols/TLV3601QDBVRQ1.kicad_sym`, `footprints/TLV3601QDBVRQ1.pretty/*` | TI TLV3601-Q1 (U3) | Ultra Librarian (TI 제공) (추정) | 위와 같음 |
| `symbols/MAX3295AUT-V-T.kicad_sym`, `footprints/MAX3295AUT-V-T.pretty/*` | ADI/Maxim MAX3295 (U4) | Ultra Librarian (ADI 제공) (추정: `_MXM` 이름 규칙) | 위와 같음 |
| `symbols/ACM2012H-900-2P-T00.kicad_sym`, `footprints/ACM2012H-900-2P-T00.pretty/ACM20121022PT001.kicad_mod` | TDK ACM2012H (FL1) | SamacSys / Component Search Engine (추정: `Manufacturer_Name` 필드 형식) | 설계 사용 허용, 라이브러리 재배포 금지 (SamacSys 약관) |
| `footprints/ACM2012H-900-2P-T00.pretty/ACM2012H-900-2P-T00.stp` | TDK ACM2012 | TDK 제품 페이지 3D 모델 (추정: 파일명 `ACM2012-T002 Block model`) | 제조사 제공 설계용 모델 |
| `symbols/SB360_diode.kicad_sym`, `footprints/SB360.pretty/P2_DO-201.kicad_mod` | SB360 (D1) | Diotec KiCad 라이브러리 (추정: Datasheet 필드 diotec.com), 프로젝트에서 드릴 수정 | 제조사 제공 설계용 라이브러리 |
| `footprints/JST_XH_S3B-XH-A.pretty/*` | JST S3B-XH-A (J2) | KiCad 공식 라이브러리 `Connector_JST` 복사본, 드릴 0.95→1.0 수정 | CC BY-SA 4.0 + KiCad 라이브러리 예외 |
| `footprints/KH-BNC75-3511.pretty/*`, `3dmodels/KH-BNC75-3511.wrl`, `footprints/Case_RT75.pretty/*` | J1 BNC, 케이스 참조 | 이 프로젝트에서 데이터시트·도면 치수로 직접 작성 | 저장소 라이선스(`LICENSE`) |

## 저장소에 넣지 않는 것

- `references/case_file/` — 케이스포유 RT-75 도면·STEP. 도면에 무단복제 금지 문구가 있어 로컬 전용(`.gitignore`). 받는 곳은 AGENTS.md 참고.

## 데이터시트 (`references/datasheets/`)

핀아웃 검증용으로 제조사 PDF 원문을 둔다. 저작권은 각 제조사에 있다. 제조사의 삭제 요청이 있으면 해당 PDF를 빼고 다운로드 링크로 바꾼다.
