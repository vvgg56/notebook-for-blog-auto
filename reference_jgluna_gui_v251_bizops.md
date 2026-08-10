---
name: reference-jgluna-gui-v251-bizops
description: "🆕 발행 체계 전환(2026-08-10): jgluna용GUI v2.51(biz워커) + 서버 bizops 호환 레이어 + write.jgluna 원고 + 브랜딩 7블로그 분리 + 딸칵 사업체 + 제목 AI 업그레이드"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 770aafe0-30ba-4d26-86da-84d772df489c
  modified: 2026-08-10T05:48:03.862Z
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

**⚠️ 미검증/후속**: ①v2.51 실기기 발행 end-to-end 미검증 — 첫 실행 시 GUI [워커 시작]+발행시작으로 1건 확인 필요(테스트 탭도 동작: test-article 서버 구현됨) ②prepare(원고+사진 생성)가 8분 넘으면 그 회차 실패→다음 run에서 캐시로 즉시 성공(재시도 무해) ③자정 자동발행 런처는 이 노트북용 미조정(경로·exe명이 2번 PC 기준) ④타세션이 13:18에 main.py를 또 수정(275KB, 내 훅 7개 생존 확인) — **서버 main.py는 브랜딩+bizops 공존본이 정본, 어느 세션이든 통째 재업로드 금지(diff-merge 필수)**.
