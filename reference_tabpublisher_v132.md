---
name: reference_tabpublisher_v132
description: TabPublisher 현재 버전(v1.1.32)·기능 요약·미해결(write.jgluna 글자깨짐) — 2026-06-23 세션
metadata: 
  node_type: memory
  type: reference
  originSessionId: 804512cd-283c-4942-b4bb-7b2c79613eca
---

발행봇 TabPublisher 현재 = **v1.1.39** (`~/Desktop/blog-publisher/dist/TabPublisherGUI_v1.1.39.exe`). v1.1.39: VPN IP 변경 검증 후 글쓰기 창 열기 전 안정화 대기(기본 60초, config `esvpn.settle_after_ip_sec`, `_launch_blog` 게이트 직후·중지가능). 빌드=`tab_publisher/gui/build.ps1`(이전 버전 exe 삭제 금지). 빌드 파이썬=PATH `python`(deps OK: PyInstaller·pywinauto·pyautogui·PIL·win32). 상세 changelog=`tab_gui.py` 상단 + `SKILL.md`. 백업=GitHub vvgg56/blog-publisher Releases(zip) — 단 **대표님 "요청할 때만 올려줘"**(Release 업로드는 명시 요청 시에만).

**v1.1.34(2026-06-25) ESvpn 잔재 로그인 fix:** 직원이 ESvpn 수동 로그인 후 안 풀면 → `login()`이 메인 화면에 ID 헛입력 → 실제 로그인 안 됨 → `connect`가 **잔재 계정**을 접속(IP는 바뀌어 검증 통과) → **네이버 계정과 다른 VPN으로 엣지 열림=보호조치**. IP변경검증만으론 계정 일치 보장 못 함이 핵심. 수정: `esvpn_helper.ensure_logged_out()`(버튼 텍스트 열거로 접속/해제/로그아웃 마커 감지→해제+로그아웃, 2회 실패 시 RuntimeError=발행중단)를 `login()` 진입 시 항상 호출(깨끗한 로그인 화면 확보 후 로그인). 연속발행 시작 시 1회 점검. `_launch_blog`에 최종 계정 일치 게이트(`_vpn_verified != vpn_id`면 엣지 안 엶·스킵). 로직 모킹테스트 통과. **v1.1.35(2026-06-25): v1.1.34 오탐 fix** — WPF 는 안 보이는 버튼(로그아웃/접속)도 UI 트리에 항상 들고 있어, 로그인 화면인데 '잔재 로그인 감지'로 헛 로그아웃하던 버그. `_visible_button_texts`(is_visible+양수면적+창 안)로 '보이는 버튼'만 판정: 보이는 LOGIN 버튼=로그인화면→no-op(우선), 보이는 접속/해제+LOGIN없음=로그인상태일 때만 로그아웃, 불명=no-op. LOGIN 우선이라 가시성필터 불완전해도 헛 로그아웃 안 남(최악=메인서 무동작=안전). **v1.1.36(2026-06-25): 발행 직전 VPN 재검증** — 글 작성 몇 분 사이 ESvpn '오류로 종료'되면 발행 시점 맨 IP=보호조치. `_fill` 성공 후 `_click_publish` 전에 `_verify_vpn_before_publish(vpn_id)`: ESvpn 재포커스→`esvpn_connection_state`(보이는 접속/해제 버튼)→IP 재확인(검증 VPN IP=`self._vpn_ip` 유지?). 끊김([접속] 보임/IP 바뀜)/불명이면 발행 안 함(글은 작성됨·미발행). 끊김 시 진단 캡쳐(`_save_failure_screenshot`). IP가 ground truth, 버튼상태는 보조. **v1.1.37(2026-06-26): 발행 직전 대시보드 최신 제목 갱신** — 노트북 켜진 채 직원이 blog.jgluna.com 제목 고쳐도 로컬 [▶발행]이 옛 b_rows 제목으로 나가던 문제. `_start_batch`가 발행 직전 `_fetch_slot_titles(slot)`로 `/api/schedule` 재조회→b_rows 갱신 후 발행(폴더 직접원고는 폴더 우선). 원격 경로(`_build_remote_rows`)는 원래부터 매 명령 fresh fetch라 OK였음. 대시보드 `/api/schedule`는 slot0+slot1(`schedule_to_api_shape`) 수정 충실 반영 확인(서버 132건 비교 불일치 0). 즉 진짜 원인은 노트북 로컬 버튼 stale.

**이번 세션(2026-06-23) 추가 핵심:**
- VPN 절대규칙(v1.1.24, [[feedback_vpn_required_before_naver_login]]) — 단 최초1회 로그인만 면제(v1.1.26).
- 하루 2건 회차분리(v1.1.25): 연속발행 1회차/2회차 버튼, slot 단위 중복방지.
- 서식(볼드/색/하이라이트, v1.1.27): article_html→styled textNode.
- 시스템문자 청소(v1.1.31): set_body 직전 U+FFFD·변이선택자(✔️)·제어문자 제거.
- 엣지 종료→VPN 해제 순서 fix(v1.1.32): IP 노출 방지(엣지 닫고 VPN 끊음).
- ESvpn 첫 로그인 ID 누락 fix(v1.1.33): 첫 블로그 ESvpn 창 렌더 느림 → esvpn_helper.login 첫 로그인 안정화 3.5s + ID 입력 딜레이. VPN 미연결 시 엣지 안 엶은 절대규칙+IP검증으로 이미 보장.
- 원격 제어(v1.1.28~29): [[reference_blog_dashboard_remote_control]], 원격수신 기본 ON.
- 보호목록 PROTECTION_BLOGS 14개(tetherpayback·dlaldo33333·eunsilto4021 추가).
- **2026-06-26 블로그 제거: `eunsilto4021`(저품블)·`goldenlady73`(보호조치 불가)** — 활성목록에서 제외: 대시보드 `main.py BLOGS`+`fetch_blogs.py` 제거 + 서버 `excluded_blogs.json` 추가(그리드/`/api/blogs`/`/api/schedule`에서 숨김, 커밋 aa1d219, 배포·검증 완료) + 노트북 `config.json`(`profile_to_blog`/`profile_labels`)에서 제거(GUI 재시작 시 반영, EXE 재빌드 불필요—config는 `~/Desktop/blog-publisher/config.json` 외부 파일을 읽음). ⚠️ 단 안전망(`PROTECTION_BLOGS`·대시보드 PROTECT JS·RISK 식별자)에는 **일부러 남겨둠**(재추가 시 보호) → grep 으로 보이지만 활성 아님.

**v1.1.38(2026-06-30) 발행 본문 서식 안전망:** 발행글에 색/볼드 안 들어간다는 신고 → 진단: write.jgluna=서버 `/home/ubuntu/blog-writer`, **2026-06-29 write-v2 신엔진(`blog-writer-v2.service` port 8015) cutover**. 사업체별 프롬프트(saipay/cleaning은 서식 지시 有, 블덱스라이터/고시원/가론지 등 無)라 전환글 일부가 서식 마커({{bold}}/{{red}}/{{highlight}}) 없이 와서 평문 발행(로그 `styledNodes=0`, 전체 254글 중 31=~12%, 거의 전환글). cdp_client set_body/JS_SET_BODY 엔진·발행물 HTML은 정상(blogno1 등 색17·볼드22 확인) — 즉 엔진 OK, **상류 누락이 원인**. 발행물에 실제 색/볼드 들어있음을 확인하려면 `m.blog.naver.com/{id}/{logNo}` HTML grep(color:/<b>/<mark). **수정**: `write_api._writer_markers_to_html` 시작부 `_auto_emphasize_plain` — 원고에 서식 마커가 '전혀 없을 때만' 단락 첫 문장 자동 강조(첫단락 형광·3번째마다 색상·나머지 볼드)→기존 마커→HTML 파이프 그대로. AI 서식 있는 88%는 무변경. 노트북측이라 write-v2 또 바뀌어도 안 깨짐. (근본은 write-v2 프롬프트에 서식 지시 추가가 정석이나 별도 시스템이라 안전망 우선.)

**🔴 미해결 — write.jgluna 원고 글자 깨짐:** AI 원고 생성(write.jgluna.com)이 한글을 깨뜨려 **U+FFFD(깨진문자)가 원고에 섞여 나옴**(예 "넌 이거"→"넌 이"+깨짐). 보통 AI 응답 스트림에서 한글 멀티바이트가 청크 경계로 잘려 디코드 실패. v1.1.31 sanitize 가 **발행 경고는 막지만** 깨진 자리는 빈 채 발행됨(내용 손실). **근본 해결은 write.jgluna 서버 생성 파이프라인 수정 필요**(별도 작업, write.jgluna 는 blog-dashboard 와 다른 시스템). 대표님이 원하면 그쪽 들여다볼 것.
