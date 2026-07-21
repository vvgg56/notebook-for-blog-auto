---
name: reference_notebook_elite_gui_deploy
description: "발행용 노트북에 TabPublisher 엘리트아이피 GUI v1.2.1 빌드·배포 완료 (2026-07-13, ESvpn→엘리트 전환)"
metadata: 
  node_type: memory
  type: reference
  originSessionId: ba2ebbaf-956e-40ee-a102-5a73c7774bcd
---

**배경(2026-07-13 대표님)**: 이 발행용 노트북(blog.jgluna 전용)이 ESvpn 보호조치가 너무 잦아 **엘리트아이피(eliteip.co.kr)로 전환**. 블덱스라이터 비즈니스가 엘리트IP로 발행하니 보호조치 안 걸리고 가격도 쌈. 다른 엘리트 발행PC(2번 노트북)가 만든 GUI를 이 노트북으로 가져와 실행. 개선은 이 노트북에서 직접.

**핵심: TabPublisher 엘리트 버전은 이미 `vvgg56/blog-publisher` main에 있음** — 파일 따로 가져올 필요 없이 pull하면 됨. 상세 = [[reference_tabpublisher_elite]] (config.ip_provider 스위치, elite_helper.py, 맨IP 금지 게이트 불변).

**2026-07-13 이 노트북에서 한 것:**
- `~/Desktop/blog-publisher` pull → v1.2.1 (커밋 `fe13849 양방향 탐색`, APP_VERSION 1.2.1). elite_helper.py에 **근접 우선 양방향 탐색(search_radius=5, 오프셋 0,-1,+1,-2,+2…)** = 고정IP 만료로 매니저 목록 밀려도 config 안 고쳐도 자동 복구([[ref_elite_ip_mapping]], biz worker v1.32 a631f35 이식분).
- 빌드: `tab_publisher/gui/build_tab_gui.bat`에 `APP_NAME=TabPublisherEliteGUI_v1.2.1` env. **이 노트북 python=`C:\Users\장영훈\AppData\Local\Python\pythoncore-3.14-64\python.exe`**(PATH 별칭 가로챔 주의, build 전 PATH에 그 dir+Scripts 앞세움). build_tab_gui.bat엔 이미 `--hidden-import=elite_helper --hidden-import=PIL.ImageGrab` 있음. PyInstaller 6.21. 산출=`dist/TabPublisherEliteGUI_v1.2.1.exe`(26,618,102 bytes).
- 검증: py_compile OK + 모듈 임포트(elite_helper.EliteIpManagerConnection·tab_gui.App._connect_elite 존재) OK.
- **배포**: `C:\Users\장영훈\Pictures\blog용GUI_v1.2.1\blog용GUI_v1.2.1.exe` 로 교체(대표님이 그 폴더로 실행). 기존 프리빌드는 `blog용GUI_v1.2.1.exe.prebuilt_bak` 백업. 옆 config.json=엘리트(ip_provider=elite, quote 켜짐 quotation_line).

**🔴 실동작 전 대표님이 채워야 할 것 (아직 안 됨):**
1. **엘리트IP 매니저 프로그램 설치** — eliteip.co.kr 고정IP매니저(설치 시 "악성 파일" SmartScreen 오탐=서명없는 국내SW, 다른 PC서 검증됨). elite_helper가 이 매니저 창을 키보드로 자동조작해 IP 전환. 미설치면 엘리트 IP 전환 자체가 안 됨.
2. **config.json `elite.rows`(엘리트IP→매니저 행번호)·`elite.blog_ips`(블로그ID→엘리트IP)** — 현재 둘 다 비어있음. 엘리트IP 발급/블로그 배정 후 채워야 함. 미매핑 블로그는 엣지 안 엶(맨IP 금지 절대규칙).
3. `elite.window_keyword`="엘리트"(매니저 창 제목 키워드). search_radius 없으면 코드 기본 5.

**✅ 2026-07-13 마이그레이션 검증·완료 (옛 GUI 규칙→엘리트, 재로그인 0):**
- 배포 config(Pictures)가 운영 config와 **profile_to_blog 키+값 완전 동일** + 같은 정션 `C:\EdgeBotData` → **모든 아이디 엣지 재로그인 불필요**(그대로 씀). dashboard=blog.jgluna, write=write.jgluna, remote.agent_id=notebook-main 동일.
- 제목가져오기/자정 재호출/발행기록/원격은 같은 코드(엘리트는 VPN계층 4곳만 분기, tab_gui _ip_provider/_elite_ip_for/_connect_elite, elite_helper). 파이프라인·맨IP금지 게이트 불변 확인.
- 🔴 **원격 발행이 엘리트 GUI로 가려면 옛 GUI(TabPublisherGUI_v1.1.43.exe)와 동시 실행 금지** — 같은 agent_id=notebook-main 충돌. 엘리트 GUI만 실행.
- **엘리트IP 10개(테스트용) 매핑 완료**: 매니저(v4.24, 계정 jglunablog) 표시순=행0~9 = 211.235.234.253/211.241.212.131/211.254.48.236/211.254.82.149/211.255.55.147/211.41.174.206/218.36.93.210/223.165.195.201/61.250.157.133/61.250.234.207. `elite.rows`(IP→행)+`elite.blog_ips`(첫10블로그 joywater2·tirososo·xkaliver123·ehdehdrn126·nuri6342·skflskfl337·arbamkong·youmiggi·hoosigidane·sotye → IP) 채움. 나머지 60블로그=IP없어 스킵. config만 변경(재빌드X)→**GUI 재시작 필요**(실행중 GUI는 옛 빈매핑). IP값은 대표님 캡처에서 판독(오차 시 실제IP≠기대IP로 스킵=안전).
- ⚠️ elite.rows는 v1.2.1 양방향탐색(±5)이라 정확할 필요 없음. IP 더 사면 매니저 순서대로 rows 추가+blog_ips 확대.

**2026-07-13 라벨 + v1.2.2**: 프로필 목록(최초1회 로그인·연속발행 탭)이 엘리트 모드일 때 배정 엘리트IP 표시(공용 `_ip_label(blog,bracket)` = `_elite_ip_for(vpn_id)`, "엘리트IP 211.x.x.x"/"엘리트IP 없음", esvpn 모드 불변). APP_VERSION 1.2.1→**1.2.2** bump(blog-publisher main push, 커밋 7867aec). ⚠️엘리트 라벨은 vpn_id로 조회(첫10블로그 blog_id==vpn_id라 무관, override블로그는 blog_ips 키를 vpn_id로 넣어야).
- 🔴 **현재 배포 = `C:\Users\장영훈\Pictures\blog용GUI_v1.2.2\blog용GUI_v1.2.2.exe`** (exe+config.json[엘리트·10IP매핑]+사용법). 창제목 v1.2.2. 옛 v1.2.1 폴더는 구버전(실행중이던 것). 대표님은 v1.2.2 폴더 exe만 실행. 빌드=APP_NAME=TabPublisherEliteGUI_v1.2.2 → dist → blog용GUI_v1.2.2.exe로 복사.

**참고**: biz-publisher 워커 최신 = `vvgg56/biz-publister` v1.32. blog.jgluna 발행 GUI(ESvpn 옛버전)=`~/Desktop/blog-publisher/dist/TabPublisherGUI_v1.1.43.exe`(config.ip_provider 없거나 esvpn이면 그대로 ESvpn). 관련 [[reference_tabpublisher_elite]] [[reference_bizpublisher_v103_elite_gui]] [[ref_elite_ip_mapping]] [[reference_edge150_debugport_block]].

**2026-07-13 보호조치불가 6개 삭제 + IP 밀기**: sosohanssaum·wemembers·ydhfpswly·dlaldo33333·ehdehdrn126·ehdbs1298 = 보호조치 절대 못 풀어 운영 금지 → 전 지점 삭제. ① 배포 config(v1.2.2) + 운영 repo config(push 8e4e44a) profile_to_blog/labels/override/naver_id/active_vpns/expected_ips에서 제거(70→66, ydhfpswly·ehdbs1298는 원래 profile_to_blog에 없었음-PROTECTION_BLOGS 코드에만). ② blog.jgluna 서버 excluded_blogs.json에 6개 추가(SSH, 총24, 새 추가=sosohanssaum·ydhfpswly·ehdbs1298, 나머지 3개 이미 제외). ③ **엘리트 IP 밀기**: ehdehdrn126(행3) 삭제 → nuri6342가 행3(211.254.82.149) 받고 하향 캐스케이드, **inbyeol-(vpn_id=haruka234, key=haruka234)이 마지막 행9(61.250.234.207)**. 상위10 생존=joywater2·tirososo·xkaliver123·nuri6342·skflskfl337·arbamkong·youmiggi·hoosigidane·sotye·inbyeol-. 엘리트10 전부 대시보드 스케줄 있음(발행OK). 🔴GUI 재시작해야 66블로그+새 IP매핑 로드. PROTECTION_BLOGS 코드 constant는 안전망이라 남겨둠(profile_to_blog에서 빠져 GUI엔 안 뜸).

**2026-07-16 GUI서 5개 추가 제거(발행중지)**: tetherpayback·goldenlay73(=goldenlady73)·shds9owe·jerrysnoopy·vndjduraks4 = 대표님 "gui에서 빼줘, vpn 연결 안되게". 대시보드는 손 안 댐(요청=GUI만) — 확인결과 이 5개는 이미 대시보드 excluded=True·스케줄없음(goldenlady73은 2026-06-26 목록서 이미 제거). 배포 config(v1.2.2)+운영repo(push 8378238) profile_to_blog/labels/override/naver_id/active_vpns/expected_ips/low_trust_blogs서 제거(66→62, goldenlay73/goldenlady73는 원래 없었어 실제 삭제=4개 tetherpayback·shds9owe·jerrysnoopy·vndjduraks4). 5개 다 엘리트 상위10에 없어 **IP 재배정 불필요**(원래 IP 없어 스킵되던 것). 🔴GUI 재시작해야 62개 반영. 백업=config.json.bak.before_delete5.

**2026-07-17 엘리트 IP 50개 추가→60개 전체 매핑 + 조종=관리자권한 규명**:
- 🔴🔴 **매니저 IP 목록 원본 = `C:\Program Files (x86)\엘리트 아이피 고정 매니저\Config.ini` 의 `[Mainod]` 섹션** (IP=행번호, 0부터). 스크롤/OCR/캡처 전부 불필요 — 이 파일 읽는 게 가장 정확(캡처 rows 0~16과 일치 확인). 60개면 여기 60줄. (같은 파일에 eID/ePass(암호화)도 있음 — 노출 금지.)
- 새 50개 추가결제(만료 2026-10-14, 발급 2026-07-16)=행10~59. 행0~9=기존(만료 2026-08-15). append 순서(정렬 아님).
- **매칭=행 N → 프로필순서 N번째 블로그**. 배포 config(v1.2.2)+repo(push 04bf62f) elite.rows=60개 전체, elite.blog_ips=블로그60개(인덱스0~59). blog_ips 키=blog_to_vpn_override.get(blog,blog)=vpn_id(override면 vpn_id, 아니면 blog_id). 61·62번째(blogno1·soo_bio)=IP 없음(60개라 스킵). 백업 config.json.bak.before_elite60.
- 🔴🔴 **매니저 창 조종이 안 되는 것처럼 보이는 함정**: 엘리트 매니저(pid, `엘리트 아이피 고정 매니저.exe`)는 **관리자 권한**으로 실행됨. 일반권한 프로세스(예: bash에서 띄운 python)가 보내는 클릭/키/휠은 **Windows UIPI 로 전부 차단**(캡처만 됨). 그래서 내가 테스트한 스크롤·클릭·화살표 전부 무반응이었음(리스트뷰 문제 아님).
- ✅ **해결은 이미 내장**: GUI exe는 `uac_admin=True`(스펙) + `_ensure_admin()`(tab_gui.py, ShellExecute runas 재실행)로 **자동 관리자 실행**. 배포 exe에 `requireAdministrator` 매니페스트 확인됨. 관리자 GUI → 관리자 매니저 조종 OK = 다른 PC와 동일. **대표님은 GUI를 그냥 실행(UAC 승인)하면 됨**.
- 이 리스트뷰(ListView20WndClass)는 WS_VSCROLL 안 씀→GetScrollInfo/LVM_* 전부 0 반환(항목수·스크롤 못 읽음, 있어도 무시). 판독은 Config.ini 로.
- 화면배율 125% 노트북 → 프로그램 캡처는 반드시 DPI 인식(SetProcessDpiAwarenessContext(-4)) 후 GetWindowRect/ImageGrab, 안 그럼 축소좌표(754px)로 잡혀 10개만 보임.

**2026-07-17 🔴 배포 폴더 영구 고정 (대표님 지시 "빌드할때마다 다른곳에 두지 마")**: 배포 위치는 버전 무관 항상 **`C:\Users\장영훈\Pictures\blog용GUI`** 하나. exe 파일명에만 버전(`blog용GUI_v1.2.3.exe`), 새 버전 배포 시 이전 exe만 삭제·교체(config.json·published_log.json·remote_jobs.json·사용법.txt는 그대로). 구버전 폴더(v1.2.1·v1.2.2, bak 포함)는 `blog용GUI\_구버전\`에 통째 보존. 배포 스크립트=`tab_publisher/gui/deploy_notebook.bat <버전>` (push e12a65d). **다음 세션: 새 폴더 만들지 말 것.**
- v1.2.3 빌드·배포됨(ALT stuck 수정 + 시작 시 modifier 자동해제, requireAdministrator 확인, 26,616,147B). APP_VERSION=1.2.3.
- 엘리트 IP 2개 추가 결제(총 62) — Config.ini 아직 60개, 매니저 [IP리스트 갱신] 후 행60·61 뜨면 blogno1·soo_bio에 매핑 예정(그러면 62블로그 전원 IP).

**2026-07-17 엘리트 62개 완성**: 추가 2개(발급 2026-07-17, 만료 2026-10-15) = 행60 211.255.12.21→blogno1(프로필71), 행61 218.36.109.144→soo_bio(프로필72). Config.ini 62개 확인. 고정배포 config+repo(push 466bf99) rows=62·blog_ips=62 → **62블로그 전원 엘리트IP 배정**(엘리트IP 없음 = 0개). GUI 재시작해야 반영.

**2026-07-21 v1.2.4 빌드·배포 (PC1 인계)**: pull(f71f256+dbceb64, 로컬수정 0) → APP_VERSION 1.2.4 확인 → 엘리트 이름 빌드(requireAdministrator·26,620,435B) → 고정폴더 배포. **실행 중 GUI 안 죽이고 교체**: 실행 중 exe는 rename 가능 → v1.2.3.exe를 _구버전으로 mv 후 v1.2.4.exe 복사(발행·원격수신 중단 0, 프로세스는 계속 v1.2.3 — 재시작해야 v1.2.4). v1.2.4=회차 제목 없는 블로그 체크해제+비활성(_set_row_slot_titled/_slot_no_title, 전체선택·VPN만으로도 못 켬, 재활성 시 자동체크 안 함). 검증: 코드로직 확인 + API에서 1건/일 17개 slot2 제목 0/17(대조군 5개는 있음 — blog-dashboard 562266f 정상). 5f4957b(elite_helper 해제루프 biz v1.34 동기화)도 pull에 포함. GUI 인터랙션 검증(제목가져오기 버튼 등)은 관리자GUI라 입력주입 불가 → 대표님 체크리스트로 인계. 관찰: 11:07 aukiding 에디터 미감지(로그인 필요?) 1건.
