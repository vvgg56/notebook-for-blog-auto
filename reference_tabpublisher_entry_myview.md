---
name: tabpublisher-entry-myview
description: "v1.8.9 글쓰기 진입 확정 경로 — 텍스트 검색 금지, MyView [role=tab]+GoBlogWrite 셀렉터만, URL 가드, 줌아웃 시 CDP 좌표 금지"
metadata: 
  node_type: memory
  type: reference
  originSessionId: ba2ebbaf-956e-40ee-a102-5a73c7774bcd
  modified: 2026-07-29T18:12:45.663Z
---

TabPublisher 글쓰기 '사람 경로' 진입(v1.8.9, 2026-07-30 확정):

- 🔴 **텍스트 검색('블로그' 등)으로 진입 클릭 금지** — 네이버 홈에 '블로그' 텍스트 요소가 6개(상단 서비스 바로가기=section.blog.naver.com 으로 튐). 면적최소 매칭이 엉뚱한 걸 눌러 BlogHome 으로 새는 사고 실증.
- 확정 셀렉터(저쪽 세션 실측 성공 플로우): ① 로그인 패널 `[class*=MyView]` 안의 `[role=tab]` '블로그' 탭(패널 전환일 뿐 URL 그대로) → ② `a[href*=GoBlogWrite]` 만 클릭. 클릭 후 URL 이 www.naver.com 아니면 이탈 판정 → 홈 복귀 1회 재시도 후 중단.
- 요소가 화면 밖(y>뷰포트)이면: 휠 스크롤 → 그래도 안 되면 실제 OS Ctrl+휠 줌아웃(대표님 지시). 🔴 **줌 바꾼 페이지에서 CDP Input 클릭 금지**(CDP 좌표는 브라우저 줌 미보정 → 빗나감) — `_os_click_vp`(css×dpr=물리픽셀, 줌에도 성립)로 클릭. 줌은 도메인별 영구 저장 → 진입 시작 때 항상 Ctrl+0 리셋(`zoom_reset`).
- MyView 패널은 클라이언트 렌더 — 탐색은 반드시 폴링(12초). 1회 측정 즉시실패 금지.
- 진입 각 단계 `snap()` 캡쳐가 exe 옆 `블로그발행/_캡쳐/YYMMDD/` 에 저장됨 — 진단은 이 캡쳐+실시간 로그(테스트 탭 하단)로.
- 관련: [[tabpublisher-v132]], [[edge150-debugport-block]]
