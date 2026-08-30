---
name: gui-defender-fp-backup
description: GUI exe가 며칠마다 사라진 원인=Defender ML 오탐 격리. 제외등록+3중 백업체계 구축(2026-08-24)
metadata: 
  node_type: memory
  type: reference
  originSessionId: ac06fafe-8ee0-43a0-8ddd-e623889848b2
  modified: 2026-08-24T06:58:13.682Z
---

**GUI 사라짐 사건 (2026-08-24 해결)**: jgluna용GUI exe가 `Downloads\jgluna용GUI_v2.51_배포`에서 사라진 원인 = Defender ML 오탐(Wacatac.B!ml/Bearfoos.A!ml)이 8/16·8/18·8/22 v2.87/v2.89 격리. 진단법 = Defender/Operational 이벤트 1116/1117.

구축된 방어 체계:
1. **Defender 제외 4경로** (8/24 15:18 이벤트 5007로 등록 실측 검증): Downloads\jgluna용GUI_v2.51_배포, Desktop\biz-publisher\dist, Documents\gui 백업, Pictures\blog용GUI. 스크립트 = `biz-publisher/deploy/defender_exclude_notebook.ps1`(%USERPROFILE% 기준·등록 후 read-back 검증 포함) + 바탕화면 `GUI삭제방지_관리자실행.bat`(UAC 승격).
2. **백업 폴더** `Documents\gui 백업` — jgluna용GUI v2.51~v2.89 전 버전 + blog용GUI. 대표님 지시: 이 폴더 파일은 삭제 절대 금지(추가만).
3. **예약작업 GuiBackupSync**(1시간마다) = `deploy/gui_backup_sync.ps1` 실행 — dist/Downloads/Pictures의 새 exe를 백업 폴더로 추가 복사(덮어쓰기·삭제 없음). 스크립트는 ASCII-only, 한글 폴더명은 [char]코드포인트로 조립(인코딩 사고 면역).

주의: 구 `deploy/defender_exclude.ps1`(8/10)은 2번PC(C:\Users\USER) 경로 하드코딩 — 이 노트북에서 실행하면 헛등록. [[deploy-script-hardcoded-path-lesson]]
