---
name: feedback-jgluna-gui-repo-split
description: "🔴 2026-09-02 대표님 확정: GUI 운용 분리 — biz-publisher=블덱스 전용(정본 v3.05), 제이지루나 GUI=vvgg56/jgluna-publisher 전용. biz-publisher master 에 jgluna 커밋 금지"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 26ca5c6b-9dfb-4547-9c2c-61753aef1a59
  modified: 2026-09-03T03:41:50.747Z
---

**2026-09-02 대표님 확정 (PC2 세션 경유 지시)**: biz-publisher repo = 블덱스라이터 비즈니스 GUI **전용**.
제이지루나 블로그(blog.jgluna.com) GUI 작업은 **전용 repo `vvgg56/jgluna-publisher`** 에만 기록·커밋.

**Why:** 두 제품이 한 repo·한 버전 줄기를 공유하다 계속 겹침 — v3.06/v3.07(제이지루나 용도)이 블덱스 줄기에 들어갔고,
8/27 중복 v2.99 사고·9/2 v3.07 오빌드 직전 중단 사례. 블덱스 운영 정본 = 태그 `blogdex-biz-v3.05`(0b017c3, /_gui 서버 최신).

**How to apply:**
1. 발행 노트북의 제이지루나 GUI 작업 클론 = `~/Desktop/jgluna-publisher` (biz-publisher 이력 전체 + 태그
   jgluna-gui-v3.06(5921d63)/jgluna-gui-v3.07(015b4e3) 보유, origin=vvgg56/jgluna-publisher). **biz-publisher 에 push 금지**
   (`~/Desktop/biz-publisher` 는 참조/pull 용). SKILL·주기 푸시 루틴도 jgluna-publisher 쪽으로.
2. exe = `jgluna용GUI_vX.XX.exe` 이름 유지, 배포 = 발행노트북 로컬 폴더(`Downloads\jgluna용GUI_v2.51_배포`)+`Documents\gui 백업`
   — 블덱스 `/_gui` 업로드(upload_gui.py)·`blogdex용GUI_vX.XX.exe` 이름 체계 사용 금지.
3. 작업 전 `git fetch` 로 최신 커밋이 어느 제품 것인지 확인(같은 보고가 두 세션에 갈 수 있음).
4. ⏳ 2026-09-03 상태: repo 생성 권한 토큰이 이 PC 에 없어(파인그레인드 PAT=생성 403, classic=만료) **GitHub 에
   `jgluna-publisher` 비공개 repo 생성은 대표님/PC2 몫**. 생성되면 `git -C ~/Desktop/jgluna-publisher push -u origin master --tags`
   → 완료 후 PC2 가 biz master 의 v3.06/3.07 revert 예정(그 전까지 biz master HEAD 로 블덱스 exe 빌드 금지).
   푸시가 403 이면 파인그레인드 PAT 의 Repository access 에 새 repo 추가 필요.

관련: [[reference-jgluna-heading-renumber-v307]] [[reference-jgluna-branding-expected-ip-404]]
