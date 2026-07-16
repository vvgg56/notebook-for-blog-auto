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

**참고**: biz-publisher 워커 최신 = `vvgg56/biz-publister` v1.32. blog.jgluna 발행 GUI(ESvpn 옛버전)=`~/Desktop/blog-publisher/dist/TabPublisherGUI_v1.1.43.exe`(config.ip_provider 없거나 esvpn이면 그대로 ESvpn). 관련 [[reference_tabpublisher_elite]] [[reference_bizpublisher_v103_elite_gui]] [[ref_elite_ip_mapping]] [[reference_edge150_debugport_block]].
