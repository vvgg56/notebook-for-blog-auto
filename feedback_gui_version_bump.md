---
name: feedback-gui-version-bump
description: 🔴 신규 GUI 빌드 시 반드시 버전 bump (대표님 2026-08-10 지시) — jgluna용GUI/biz-publisher 계열 공통
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 770aafe0-30ba-4d26-86da-84d772df489c
  modified: 2026-08-10T05:54:01.029Z
---

**대표님 지시(2026-08-10): 신규 GUI 빌드가 나오면 반드시 버전을 올릴 것.** (jgluna용GUI v2.51 → 다음 빌드는 v2.52 …)

**Why:** 버전이 안 올라가면 어떤 빌드가 돌고 있는지 구분 불가. 과거 사고 이력: 자정 런처가 v1.64 하드코딩으로 옛 방식 실행(2026-08-04), 구버전 GUI 가 떠 있는데 몰랐던 사례. 런처도 `*GUI_v*.exe` 최신 버전 패턴으로 exe 를 고르므로 버전 bump 가 배포의 일부임.

**How to apply:**
1. 소스 `vvgg56/biz-publisher` `gui.py` 의 `APP_VERSION` bump (v2.51 = gui.py:324) — 창 제목·워커 로그에 반영됨.
2. exe 파일명에도 버전 (`jgluna용GUI_v2.52.exe` 식). 배포 폴더는 그대로 두고 exe 만 교체, config.json·발행이력·로그 파일은 보존 (TabPublisher 때 확립한 규칙 [[reference_notebook_elite_gui_deploy]] 2026-07-17 "배포 폴더 영구 고정"과 동일 원칙).
3. 빌드가 2번 PC(비즈 워커 빌드 PC)에서 이뤄지면 그쪽 세션에도 이 규칙 적용 — 대표님이 가져온 배포본 받을 때 버전 확인.
4. 이 노트북 배포 위치 = `C:\Users\장영훈\Downloads\jgluna용GUI_v2.51_배포` (버전 올라가면 exe 만 교체, 폴더명은 대표님 결정 따름).

관련 [[reference_jgluna_gui_v251_bizops]].
