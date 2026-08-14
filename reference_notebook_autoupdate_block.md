---
name: reference-notebook-autoupdate-block
description: "발행 노트북 자동업데이트 전면 차단(2026-08-12 대표님) — 바탕화면 자동업데이트차단.bat(UAC 승격), 정책 레지스트리+서비스+예약작업. 해제=자동업데이트차단해제.bat"
metadata: 
  node_type: memory
  type: reference
  originSessionId: f2e81429-2c7e-43da-b69a-b2c59e3d9937
  modified: 2026-08-14T04:34:47.032Z
---

**2026-08-12 대표님 "여기 컴퓨터 자동업데이트 다 막아줘"** — 배경: Edge 150 자동업데이트가 디버그포트를 죽여 발행 0건 사고([[reference-edge150-debugport-block]]), 발행 전용 노트북은 버전 동결이 안전.

**차단 범위** (바탕화면 `block_autoupdate.ps1`, 실행기 `자동업데이트차단.bat`):
1. **Windows Update**: `HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` NoAutoUpdate=1(+AUOptions=2), ExcludeWUDriversInQualityUpdate=1. Win11 Pro 라 정책 유효. 수동 업데이트([설정→Windows 업데이트]에서 직접 확인)는 여전히 가능.
2. **Edge**: `HKLM\SOFTWARE\Policies\Microsoft\EdgeUpdate` UpdateDefault=0·AutoUpdateCheckPeriodMinutes=0·Update{56EB18F8-B008-4CBD-B6D2-8C97FE7E9062}=0 + edgeupdate/edgeupdatem 서비스 Disabled + MicrosoftEdgeUpdate* 예약작업 Disable.
3. **Chrome**(크롬 발행 폴백 버전 동결): `HKLM\SOFTWARE\Policies\Google\Update` UpdateDefault=0 등 + gupdate*/GoogleUpdater* 서비스 Disabled + GoogleUpdate*/GoogleUpdater* 예약작업 Disable. ⚠ 크롬 원격데스크톱(remoting_host)도 구글 업데이트 계열이라 같이 동결됨 — 원격접속 문제 생기면 이걸 의심.
4. **MS스토어**: WindowsStore AutoDownload=2.

**실행 방식**: 셸이 비승격이라 UAC 필요 — `Start-Process -Verb RunAs` 원격(CRD) 세션에서 2회 연속 즉시 취소됨(보안 데스크톱 승인 창 문제로 추정). → 바탕화면 bat 더블클릭(자가승격) + UAC [예] 방식으로 전환. **결과 로그 = `%TEMP%\noupdate_result.txt`** (각 단계 OK/ERR). 실행 여부 확인 = 이 로그 존재 + `Get-Service edgeupdate`(Disabled) + WU 정책 레지값.

**해제**: 바탕화면 `자동업데이트차단해제.bat`(`block_autoupdate_undo.ps1`) — 정책 삭제 + 서비스 Manual + 예약작업 Enable. 로그 `%TEMP%\noupdate_undo_result.txt`.

**주의**: 보안패치도 같이 멈춤 — 몇 달에 한 번, 발행 없는 낮에 수동 업데이트 권장(엣지는 업데이트 후 디버그포트/발행 1건 검증 필수).
