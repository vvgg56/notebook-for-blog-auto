---
name: reference-jgluna-gui-v251-bizops
description: "🆕 발행 체계 전환(2026-08-10): jgluna용GUI v2.51(biz워커) + 서버 bizops 호환 레이어 + write.jgluna 원고 + 브랜딩 7블로그 분리 + 딸칵 사업체 + 제목 AI 업그레이드"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 770aafe0-30ba-4d26-86da-84d772df489c
  modified: 2026-08-11T04:57:27.192Z
---

**2026-08-10 대전환: 발행봇을 TabPublisher(v1.8.x)에서 `jgluna용GUI_v2.51`(블덱스 비즈니스 워커 = biz-publisher master 27ba095, 누락 해결판)로 교체.** 대표님이 2번 PC에서 깎아온 exe(`C:\Users\장영훈\Downloads\jgluna용GUI_v2.51_배포`)를 이 노트북에서 blog.jgluna.com에 물림.

**서버(3.38.22.7 blog-dashboard)에 새로 얹은 것 (systemctl restart 완료, 전부 가동 확인):**
- `bizops_compat.py` — 워커가 부르는 `/api/bizops/worker/*` + `/api/biz-kakao-queue` 전체 재구현(main.py 맨끝 register 훅 + `_AUTH_EXEMPT_PREFIXES`에 `/api/bizops/` 추가). 스케줄=`/api/schedule`(self-HTTP, **오늘 slot0만 서빙**), 원고=`write_api.WriteAPI.fetch_full_post`(write.jgluna bot/qmffhrm1, 본문+사진 생성·검토, `<사진N>`+`{{bold}}`마커 그대로 워커에 전달—워커가 재부여/서식적용), 발행완료=`POST /api/published-url`(그리드 표시 연동). sched_id=`day_idx*100000+(uid-1000)*10+slot`. 🔴 `worker_expected_ip` 빈 고객은 절대 서빙 안 함(맨IP 금지). 로컬 TestClient 30/30 통과 후 배포.
- `bizops_customers.json` — 고객 57명(uid 1001~1057=옛 profile_to_blog 순서), first_login_at 채워둠(엣지 프로필 이미 로그인=최초로그인 불필요). **엘리트 IP = 7월 blog_ips 그대로 복원**(블로그별 기존 IP 유지). 🔴 처음에 Config.ini [Mainod] 59개(유령 목록)로 오배정했다가 GUI 스캔 경고로 발각 → 실물(캡쳐 판독=7월 세트 62개 건재, 정렬=문자열 오름차순 bSort=2)로 복원(CLAUDE.md 실수기록 2026-08-10). elite.rows=62(오름차순 행번호). ⚠️ 7월 IP 행0~9는 **만료 2026-08-15** — 만료로 목록 바뀌면 재동기화 필요. [Mainod]의 59개 정체불명 세트는 교체 대비 신규 발급분일 가능성(실물 목록 바뀌는 순간 그걸로 재매핑).
- `bizops_worker_key.txt` — 워커 키(v2.51 config.json worker_key와 동일). `bizops_config.json`=write 계정. venv에 `requests` 설치함.
- `title_biz_ai.py` — 블덱스 비즈니스 제목제작 프로세스 이식: 네이버 자동완성+초성(ㄱ~ㅎ) 실검색어+상위노출 제목+rank.jgluna 키워드 → Claude(sonnet-4-6, backend/.env 키) 25~40자 합성 → 사업자관점/가격/금지어 필터. `build_daily_convert_pool` rank 부족분 채움(→BIZ_TITLES 폴백), 정보글 `info_ai_candidates`(day/스케줄 생성 두 곳). CLI: `venv/bin/python title_biz_ai.py show <사업체> 20 | show-info 20`. 전부 실패시 [] = 기존 동작.
- **딸칵 사업체 신규 등록** — 항공권·쇼핑 최저가 추적 알림 앱(ddalcak.com). BIZ_TITLES 15개+RANK_BIZ_ID_BY_NAME("딸칵"→"ttalkak", rank에 키워드 65개 기등록)+CONVERSION_TITLE_VARIANT_PATTERNS+KEYWORD_TO_BIZ(항공권/땡처리/비행기표)+server config.json 가중치 10(백업 config.json.bak_20260810_ddalkak).
- 🔴 **동시작업 주의**: 같은 날 12:51 **다른 세션(2번 PC)이 서버 main.py에 브랜딩 기능 +524줄**(BRANDING_BUSINESSES 7개=wemembers·horizon37·goldenlady73·haerieva·space_blog·inbyeol-·hhanwool, BLOGS에서 제거+/api/schedule pop+브랜딩 탭·전용 스케줄 branding_schedules.json) 작업. 내 패치는 그 위에 얹음(둘 다 살아있음). 백업: `main.py.bak_20260810_branding`(브랜딩 전=234KB), `main.py.bak_20260810_bizops`(브랜딩 후·bizops 전=259KB). 그 세션이 main.py 통째 재업로드하면 내 bizops가 날아가니 diff 후 배포하라고 전달 필요. 그들의 딸칵 intro가 "순위조회 서비스"로 잘못돼 있어 "항공권 최저가 알림 앱"으로 교정함.

**노트북 쪽:**
- `Downloads\jgluna용GUI_v2.51_배포\config.json` 재작성(백업 config.json.bak_blogdex): server=blog.jgluna.com, worker_key, customers 57(uid+edge_profile=기존 Edge 프로필 그대로→재로그인 0), elite.rows=매니저 실측 59개(행0~58), mode=manager, publish_mode=auto. edge_user_data=C:\EdgeBotData(정션) 그대로.
- `Pictures\blog용GUI\config.json`(옛 TabPublisher)에서 브랜딩 5개 제거(62→57, 백업 .bak_20260810_branding7). ⚠️ 옛 GUI와 v2.51 동시 실행 금지(옛 GUI는 이제 안 씀).
- 자정 자동발행 런처(auto_publish_midnight.ps1)는 2번 PC 경로/파일명 기준이라 이 노트북용 수정 전(미설정).

**스케줄**: 8/9=day100(제목 바닥나 0건이던 것) → 8/9~8/11=day100~102 각 44/44 생성 완료(정보13/전환31, 딸칵 2건/일). 활성 블로그 44개(70→브랜딩7+기존제외). 생성 중 13:19 타세션 재시작으로 day101 1회 유실→재생성으로 복구(force=False라 안전).

**🎨 제목 스타일 학습 (2026-08-10 오후, 대표님 "그리드 제목 허접함"):**
- 원인: 스케줄 생성이 rank 키워드→기계식 템플릿(`_make_rank_title`/사이페이 쿠폰패턴) 1순위라 AI 제목이 부족분에만 쓰였음.
- horizon37(사이페이 브랜딩) 제목 **2023-09~2026-01 총 591건** 크롤(PostTitleListAsync)·학습 → `title_style_horizon37.json`(서버 backend). 스타일: 평균 31자·콤마로 서브키워드 2~3개 묶기(79%)·'및/부터~까지' 연결·물음표 후킹 40%·어미 다양(정리/총정리/알아보기/알려드림).
- `title_biz_ai.py`: `_STYLE_RULES`+코퍼스 예시를 전환/정보 프롬프트에 주입. **사이페이 전용 `restyle_saipay_pool`** = 쿠폰 rank 제목을 스타일 리라이트하되 **숫자 토큰 완전일치 게이트**(할인율 팩트 보존, 불일치=원본 유지). 사이페이 유사도 컷 0.92(쿠폰 주제 반복 구조라 0.75는 풀 고갈 — 실제 7/33 사고).
- `build_daily_convert_pool` 순서 변경: **AI(스타일+rank 키워드 타겟)가 1순위** → rank 템플릿 → BIZ_TITLES. 고시원/요식업/우삼집은 템플릿 유지(지점·쿨다운 민감).
- day100~102 전환글 **84칸 전부 새 스타일로 교체**(`restyle_days.py`, /api/schedule/update 경유 — manual·발행완료 칸 보존, 교체분은 manual:True 마킹됨).
- 🔴 **동시편집 경합 2회**: 타세션이 13:38에 +10.5KB 추가 → 내가 13:41 덮어씀(그쪽 delta 유실) → `main.py.bak_20260810_style`(그쪽 13:38본)에서 복구, 그 위에 내 패치 재적용해 병합 배포(286,368B). 서버 main.py 배포 전 반드시 `stat` mtime 확인 + 바뀌었으면 diff-merge.

**🔗 /remote 연동 (2026-08-10 오후, 대표님 지시):**
- `bizops_compat.py` 안에 **브리지 스레드**(8초 틱, main.py 무수정): /remote 발행버튼 → publish-jobs 큐 → 브리지가 X-Pub-Secret 으로 `publish-jobs/next` claim → **bizops run 생성**(선택 블로그→uid 타겟) → v2.51 워커가 발행 → 진행상황을 `PUT job status`(published_count)·`bot/heartbeat`(agent_id=jgluna-worker-v2.51)·`bot/logs`(이벤트 seq 미러)로 역전달 → /remote 화면에 그대로 표시. /remote [정지] = control stop_seq 엣지 감지 → run stopping.
- claim 조건: 활성 run 없음 + **워커가 최근 90초 내 bizops 호출**(오프라인이면 queued 유지 = 화면에 정확히 표시). 워커 오프라인 상태에서 /remote 눌러도 안전.
- 노트북 config `auto_stop_after_run=false` 로 변경 — run 끝나도 워커가 계속 폴링해 /remote 트리거 상시 수신 (biz 기본과 다름 주의).
- /remote 발행은 10분 쿨다운(slot|date 채널) — 기존과 동일.
- **E2E 실측 검증 완료(2026-08-10 14:10)**: /remote 발행버튼(joywater2 1개) → 브리지 claim+run#1 → 워커 poll 시뮬 claim → prepare 실원고 생성 **113초**(4,259자·강조마커29·사진1) → article 수신 → report → run done → /remote job "done·발행0건·agent=jgluna-worker-v2.51"·봇 online·로그 표시 전부 확인. **엣지로 실발행하는 마지막 단계만 미검증**(GUI 실행은 대표님 몫). joywater2 오늘자 원고는 캐시돼 있어 실발행 시 즉시 나감.
- 🔧 report 핸들러는 /api/schedule self-HTTP 금지(repair 8초+대용량 → 15초 타임아웃 실측) — schedule_meta.json/published_state.json 파일 직읽기로 교체. bot/logs 전달 seq 는 **+9e9 오프셋**(옛 TabPublisher seq 와 충돌 시 중복 판정으로 버려짐), 커서(fwd_seq)는 원본 seq 공간 유지.

**📶 브랜딩 대시보드 엘리트IP 표시 (2026-08-10 오후, 직원 요청→대표님 지시):**
- `BRANDING_BUSINESSES` 각 항목에 `elite_ip` 추가 + `/api/branding` 응답 포함 + index.html 브랜딩 탭 헤더에 IP 배지(미배정=회색 점선). 백업 index.html.bak_20260810_eliteip.
- **브랜딩 IP 확정 근거** = 7월 blog_ips 백업의 행번호↔프로필순서 역추적(자동발행 57개 미사용 잔여 5개와 1:1 일치): horizon37=61.250.142.59(key syss8938) · haerieva=211.255.15.42 · space_blog=211.254.99.247(key sense1215) · inbyeol-=61.250.234.207(key haruka234) · hhanwool=61.250.133.4 · **wemembers/goldenlady73=배정 이력 없음**. min18ya=211.254.69.156(자동발행 배정 그대로).
- 🆕 타세션이 **min18ya(구 사이페이 공식블)를 8번째 브랜딩(블덱스라이터)으로 전환**(14:45) — 자동발행에선 BRANDING pop으로 자동 제외됨(bizops customers 에 남아있지만 서빙 안 됨).
- 🔍 **GUI 엘리트 스캔 "자동읽기 불완전" 경고의 정체**: Windows OCR이 끝자리 짧은 IP(…239.8/…12.21 등) 행을 계속 못 읽어 안전가드(불완전 스캔 덮어쓰기 방지)가 발동하는 것. **매핑은 정확, 발행 무관**(발행은 rows+ipify 실측 검증). 대처=시작 팝업 "자동읽기 할까요?"에 아니오. 근본수선은 v2.52 빌드 때(스캔 OCR 보완+갭 재시도 개선).

**⚠️ 미검증/후속**: ①v2.51 실기기 발행 end-to-end 미검증 — 첫 실행 시 GUI [워커 시작]+발행시작으로 1건 확인 필요(테스트 탭도 동작: test-article 서버 구현됨) ②prepare(원고+사진 생성)가 8분 넘으면 그 회차 실패→다음 run에서 캐시로 즉시 성공(재시도 무해) ③자정 자동발행 런처는 이 노트북용 미조정(경로·exe명이 2번 PC 기준) ④타세션이 13:18에 main.py를 또 수정(275KB, 내 훅 7개 생존 확인) — **서버 main.py는 브랜딩+bizops 공존본이 정본, 어느 세션이든 통째 재업로드 금지(diff-merge 필수)**.

**🛰 v2.52 /remote 원버튼화 + IP 중복 수습 (2026-08-10 저녁, 대표님 지시):**
- **IP 중복(GUI 경고 4건)의 정체** = `bizops_state.json` **overlay**(최초 로그인 때 bz_first_login 이 실측 IP 기록, base 보다 **우선**). 15:19/15:27 최초 로그인 때 nuri6342·arbamkong 이 엉뚱한 엘리트 행으로 연결된 채 기록돼 cwr7707(211.254.48.149)·golfyaplay(211.235.228.206)의 IP 와 충돌. **`POST /api/bizops/worker/expected-ip`(워커키)로 base 값 복원**: nuri6342=211.254.82.149(행29)·arbamkong=211.41.174.206(행42) → 중복 0. 🔴 함정: overlay 는 빈 문자열("")도 base 를 이김 — [고정IP 연결해제]식 "" 말고 **올바른 IP 를 넣어야** 함. 비슷한 IP(…48.149/…82.149, …228.206/…174.206) 수동 행클릭 착오가 원인으로 추정.
- **원버튼화 근본원인 2개**: ①워커 꺼짐+GUI만 열림 → 잡이 queued 로 잠듦(사람이 [워커 시작] 눌러야) ②서버 `_auth` 가 모든 인증 호출에 `_last_seen` 갱신 → **GUI 60초 customers 폴링까지 '워커 온라인' 오판** → 브리지가 워커 없이 claim → /remote 유령 '발행중'(누르면 진행되는 척만 하던 증상의 서버측 원인).
- **서버 패치**(bizops_compat.py, 백업 `.bak_20260810_remoteauto`, systemctl restart 완료): `_touch_worker()` 분리 — 워커 전용 6엔드포인트(poll/prepare/prepare-status/article/report/event)만 생존 갱신(worker.py 유휴 루프도 /poll 45초 확인). customers 응답에 `remote_pending`(publish_jobs.json queued peek)·`run_active`(stopping 제외)·`run_id` 추가(구버전 GUI 하위호환). 미등록 블로그만 지정된 /remote 잡 = run 안 만들고 done 마감(빈 target=전체발행 방지). BRIDGE_AGENT=jgluna-worker-v2.52.
- **GUI v2.52**(gui.py, push b1e0777): `_maybe_autostart_remote` — 발행할 일 있고 워커 꺼져 있으면 자동 [▶ 워커 시작]. 예외: 중복IP·로그인창·살아있는 워커·[■ 중지] 후 10분·`remote_autostart:false`(기본 true). 적대적 리뷰 12건 반영: auto 경로 save_cfg 생략(핫루프+설정탭 입력 오염 방지)·`_pid_is_live_worker`(STILL_ACTIVE+이미지명 blogdex/jgluna/BizPublisher, 좀비/재사용 pid 오판 방지)·_on_exit pid 파일 정리·중지 run_id 봉인+`autostart_state.json` 영속화·run-stop 3회(1동기+2백그라운드)·run_active 2연속 관측 후 시작(자정 런처 이중워커 방지)·자동시작 55초 간격+30분 3회 상한(2분 이상 생존=정상 완주 리셋).
- **빌드/배포**: 이 노트북 python3.14 PyInstaller(`--onefile --noconsole --uac-admin --name BizPublisherGUI_v2.52 --hidden-import cdp_client/worker/connection/socks/PIL.ImageGrab/human_publisher/cdp_human/human_test_tab`) → `Downloads\jgluna용GUI_v2.51_배포\jgluna용GUI_v2.52.exe`(v2.51.exe 병존). 소스 클론 = `~/Desktop/biz-publisher`(신규). ⚠️ **대표님이 v2.52 exe 를 실행해야 반영** — /remote 원버튼은 GUI(v2.52)가 켜져 있어야 동작(60초 감지+브리지 8초+워커 45초 폴 = 버튼→발행 시작 최대 ~2분). **실제 /remote 버튼→자동시작→발행 E2E 는 미검증**(실행은 대표님 몫).
- 🔴 **회귀 1건 즉시 수습(같은 날 저녁, 대표님 신고)**: _worker_online 분리 부작용으로 브리지 하트비트가 '워커 살아있을 때만' 나가 **GUI 켜져 있어도 /remote 가 '노트북 오프라인'** 표시. fix = `_gui_seen`/`_gui_online()`(customers 150초 창) 별도 추적, 하트비트 게이트만 `online or _gui_online()`(**claim 게이트는 워커 전용 유지** — 섞으면 유령 '발행중' 재발). 백업 `.bak_20260810_guionline`, 하트비트 6초 신선도 실측 확인. 교훈: 생존 신호를 분리하면 그 신호를 '표시'에 쓰던 소비자(하트비트)까지 따라가는지 확인할 것.

**🔑 워커 키 회전 (2026-08-11 새벽, 대표님 "신호가 저쪽 노트북으로 가는 것 같다"):**
- 새 키 발급 → `bizops_worker_key.txt`(구키 백업 `.bak_20260811_rotation`) + 노트북 config.json 만 갱신. **구키=401 확인** — 2번 PC 등 다른 기기의 v2.51 잔재 config 는 전부 차단됨. 포렌식: 그날 밤 워커 폴링은 45초 단일 스트림(이 노트북뿐), run#3 죽음=/remote [■정지] 2회(00:56:44), 자정 최초로그인 9건=전부 올바른 IP(오염 0). ⚠️ 키 교체 후 **GUI 재시작 필수**(설정탭 위젯에 구키 잔존 — [설정 저장]/[워커 시작] 누르면 구키 역저장).
- bz_run stopping 전환(브리지 stop_seq)은 run.events 에 안 남고 `_append_log` 만 씀 — "이벤트 없는 done run"의 정체.

**🧯 /remote 사용성 (2026-08-11 새벽, 대표님 지시):** ①쿨다운 완전 제거(서버 COOLDOWN_SEC=0 + 클라 startCooldown 제거 — active 중복거부·RSS 가드는 유지) ②로그 렉 = pollLogs 가 줄마다 `innerHTML+=`(전체 재파싱 O(n²)·무한누적) → LOGBUF 400줄 캡+1회 렌더. 백업 `main.py.bak_20260811_remotelag`. ③워커 유휴 문구 "bizadmin 에서 [발행시작]" → 원격/브랜딩 포함으로 교정(v2.53).

**🖋 브랜딩 임시저장 (2026-08-11 새벽, 대표님 "원고 5개 미리 임시저장"):**
- /remote 에 **[🖋 브랜딩 임시저장] 탭**(main.py 패치, 백업 `main.py.bak_20260811_branding`): /api/branding 목록 렌더(IP 없는 딸칵·청소플래닛=비활성), 블로그당 개수(기본5) → POST publish-jobs `action:"draft"`+`count`(PublishJobReq 에 count 추가, 채널·_active_job_for 에 action 분리).
- 서버 bizops: `bizops_branding.json` 레지스트리(6개: 사이페이/horizon37·고시원/haerieva·가론지/space_blog·인별/inbyeol-·삐딱/hhanwool=uid 2001~2005 가짜고객, 블덱스라이터/min18ya=기존 uid 1035 재사용 — pseudo 만들면 IP 중복 경고 터짐). customers 응답에 draft_only="1"로 서빙. 브리지 draft claim→run{mode:"draft", draft_items(제목 얼림), draft_pos}→poll 이 순서 서빙→prepare/report 는 sid 분기. **sid=9e8+(uid%1000)*1e6+YYMMDD**(리뷰: idx 인코딩=레지스트리 편집 시 타 블로그 오배정, 연도누락=12월→1월+1년 뒤 캐시 재사용 사고). 완료기록=`bizops_branding_drafts.json`(타세션 branding_schedules.json 은 읽기만).
- 노트북 config customers 에 uid 2001~2005+1035: edge_profile(P63/P46/P45/P11/P42… 옛 profile_to_blog 실측)+**draft_only:"1"+expected_ip**(오버라이드는 항상 병합 — 고객목록 캐시/폴백 상황에서도 발행·맨IP로 못 샘).
- 워커 v2.53: **item.draft=True 면 _dmode 무조건 "1"**(서버 draft 아이템은 어떤 경우에도 발행 안 함) + draft 아이템은 로그인 계정 확정 불일치 시 저장 중단. 임시저장 실행부는 기존 [임시저장만] 경로(cdp.save_draft) 그대로.
- 🔴 리뷰가 막은 대형사고: patch 스크립트 confirm 의 `\n` 이스케이프 1단계 부족 → non-raw 로 적용했으면 **/remote <script> 전체 SyntaxError**(발행 탭까지 사망). main.py HTML 문자열 패치는 **raw 문자열 + 기존 confirm 라인과 바이트 대조**가 규칙.
- ⚠️ E2E 실기기 미검증: 첫 실행은 /remote 브랜딩 탭에서 블로그 1개×원고 1개로 확인 권장. 같은 블로그 2번째 원고부터 SE3 '작성 중인 글' 복원 팝업은 워커의 초안팝업 자동닫기가 처리(비즈 운영 검증 경로).
- **레포 공유 주의**: vvgg56/biz-publisher 를 2번 PC(비즈)와 같이 씀 — 그쪽도 같은 날 v2.53(a21c2b2 URL 재클릭+링크 아이콘) 푸시해 rebase 병합함(ef964d1). push 전 pull 필수. 배포 exe = `jgluna용GUI_v2.53.exe`(병합본 빌드).

**🛑 v2.54 긴급정지 + 진입 클릭 착지 실측 (2026-08-11 낮, 대표님 "글쓰기버튼 감지못함/긴급정지 만들어놔/왜 여기만 안 되나"):**
- **08-11 02:07 실패의 실체(entry_fail 캡처 실측)**: 실패 순간 노트북 전면 = 네이버가 아니라 **VS Code**(대표님이 채팅 입력 중인 화면까지 찍힘). 진입 자체는 정상 동작했고([블로그] 클릭 착지→[글쓰기] DOM 좌표 확보→클릭 2회), **발행 중 노트북을 다른 작업에 쓰면 OS 마우스 클릭·전면화가 사람과 충돌해 씹히는 것**이 원인. 마지막 10053=엣지 창이 닫힘. **다른 PC는 발행 전용이라 이 충돌이 없음** = "왜 이 노트북만 안 되나"의 답. 셀렉터(MyView role=tab/GoBlogWrite)는 멀쩡.
- **v2.54**(커밋 d73d344, `jgluna용GUI_v2.54.exe` 배포폴더 복사 완료, 실기기 미검증): ①[블로그] 탭 클릭 후 [글쓰기] 링크 등장까지 **착지 실측**, 안 먹으면 캡처+8초 양보 후 재클릭(3회) ②[글쓰기] 클릭 직전 **실물 캡처**(publish_captures, 최근 60장 유지) + 에디터 탭이 8초 내 안 생기면 1회 재클릭(새 탭 하나라도 있으면 이중클릭 금지=초안오염 방지) ③os_click_vp: Edge 창 특정 실패 시 맹클릭 금지(기존엔 뚫려 있었음!) + 클릭 직전 커서 위치/덮임 재검증(사람이 커서 가져가면 포기) + **덮은 창 이름 로그**(`_fg_win_info`, psutil) + 차단 사유를 실패 보고에 전파(`_last_input_block`) ④🛑 **긴급정지 3경로**: 헤더 버튼 + 워커 가동 중 우하단 topmost 플로팅 버튼(드래그 이동·✕ 숨김) + 전역 핫키 **Ctrl+Shift+F9**. 동작=stop.flag "estop"(입력 하나하나 직전 체크에 걸림)→워커 taskkill /F→눌린 modifier/마우스버튼 **KEYUP만** 복구(worker /F로 finally 미실행 대비, 2026-07-17 ALT stuck 예방)→이후 조작 0, **엣지는 그대로 둠**(기존 [■ 중지]와 차이=엣지 정리도 안 함).
- 교훈: 이 노트북에서 발행 돌릴 땐 **노트북을 동시에 쓰지 않거나**, 쓰다가 충돌하면 긴급정지 후 재시작. 실패 진단 1순위 = publish_captures 캡처에 뭐가 찍혔나(네이버인가 다른 앱인가).

**🖋 [발행]↔브랜딩 임시저장 분리 확정 (2026-08-11 오후, 대표님 "발행만 눌렀는데 브랜딩이 임시저장됨"):**
- **run #5(13:42 브랜딩 draft)의 실체**: [발행] 탓이 아님 — 아침 **08:14:38 /remote 브랜딩 탭에서 눌린 draft 잡**(command_id `draft-1786403678257`, 6블로그×1개)이 워커 오프라인으로 큐에 잠들었다가, 13:42 GUI 켜지자 `/next`의 "가장 오래된 queued 우선" 규칙으로 **먼저 claim** 된 것. 오늘 [발행] 잡은 00:55·02:03 두 건뿐(전부 루나 44개, 브랜딩 0). 브랜딩 LOGIN_EXPIRED(haerieva P46·space_blog P45)는 **애초에 그 프로필들 로그인 안 해둔 것**(대표님 확인) — 오판 아님.
- **서버 패치**(백업 `main.py.bak_20260811_brwith`·`bizops_compat.py.bak_20260811_draftexpire`, restart 완료): ①브리지 claim 때 **draft 잡이 생성 15분 경과면 실행 않고 만료 처리**(note "⏰ 만료 — 다시 눌러주세요") — 잠든 임시저장이 몇 시간 뒤 갑자기 도는 일 차단 ②/next 응답에 created_ts 추가 ③발행 탭 상단 **[🖋 브랜딩도 임시저장 같이] 체크박스**(기본 꺼짐, id=brWith) — 체크 시 [발행] 성공 후 `draftw-` 접두 draft 잡을 추가 발행(blogs=[]=IP 있는 전체 브랜딩, 개수=브랜딩 탭 brCnt 기본5). **draftw- 는 만료 제외**(발행 run 끝난 뒤 집는 게 정상). 대시보드 포트=8007, 서비스=blog-dashboard.service.
