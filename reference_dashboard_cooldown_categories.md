---
name: reference_dashboard_cooldown_categories
description: blog.jgluna 30일 발행쿨다운 — 고시원4지점+삐딱(요식업)·우삼집, 제목키워드 탐지, blog_data.json엔 business 빈값·날짜 대부분 없음(RSS로 복구)
metadata:
  node_type: memory
  type: reference
  originSessionId: 804512cd-283c-4942-b4bb-7b2c79613eca
---

blog.jgluna.com 그리드의 블로그별 **30일 발행쿨다운 표시**([[reference_blog_dashboard_remote_control]] 서버 3.38.22.7, `backend/main.py`):
- 대상 카테고리 = `COOLDOWN_CATEGORIES` = `GOSIWON_BRANCHES`(을지로·종로·간석오거리·답십리) + `EXTRA_COOLDOWN_BUSINESSES`(`["삐딱(요식업)","우삼집"]`). `GOSIWON_COOLDOWN_DAYS=30`.
- 계산 = `collect_gosiwon_branch_status()`. 블로그×카테고리별 최근 발행일 → 30일 미만이면 "발행 N일 남음"(빨강), 아니면 "발행가능". 프론트 `index.html`은 `Object.keys(status)` 로 렌더(2026-06-24, 08ea888 직전 커밋들).

**🔴 데이터 함정 (2026-06-24 조사):**
- `blog_data.json` 글들의 **`business` 필드는 거의 다 빈값('')** → business 정확매칭으로는 못 잡음. **제목 키워드로 추론**해야 함: `detect_biz_keyword(title)`(KEYWORD_TO_BIZ, main.py ~2884). collect 함수가 business 없으면 이걸 fallback으로 호출(커밋 08ea888).
- **삐딱 맛집(요식업) vs 삐딱 가맹모집(전환) 혼동 주의** = 대표님 명시 경고. `detect_biz_keyword`가 `한식주점/술집 창업/주점 창업`(=삐딱(전환))을 `문래`(=삐딱(요식업))보다 **먼저** 매칭해서 분리됨. 우삼집=`연남/우삼집`. 실데이터 166글 검증 시 오분류 0.
- **글 대부분 `date` 없음**: source=`publisher`(대시보드 발행)만 날짜 있음, source=`(none)`(네이버 스크랩 과거이력)은 날짜 없어 쿨다운 계산 불가(자동 skip). 2026-06-24 기준 요식업/우삼집 96글 중 날짜있음 5(전부 30일내)·없음 91(전부 30일초과).
- **날짜 복구법**: `https://rss.blog.naver.com/{blogId}.xml` (최근 50글, `<pubDate>`+`<link>`의 logNo 매칭). 서버 venv=`backend/venv/bin/python`(httpx 있음).

**🔴 2026-06-24 enforcement 강화 (커밋 e4c595b, 대표님: 인천점 30일내 중복발행 사고):**
- 기존 쿨다운은 **화면 표시만**이고 생성 차단은 '날짜 있는 글'에만 동작 → 직원 수동 발행분(날짜 없음) 안 잡혀 중복. (인천점=간석오거리점, alias `인천→간석오거리점`).
- `collect_gosiwon_branch_status(use_rss=True)`: 네이버 **RSS로 최근 발행분(수동 포함)을 합쳐** 쿨다운 계산. 캐시30분/실패5분, 8연속실패 10분 회로차단, 30초 시간예산(느려도 생성 안 멈춤). 화면=`cache_only=True`+`maybe_warm_rss_cache()` 백그라운드 워밍(동기 fetch 0, /api/blogs 0.09s).
- 생성 enforce 경로 4곳 `use_rss=True`: `_generate_day_impl`/`_generate_schedule_impl`/`repair_zero_weight_conversion_businesses`/`fill_second_slots`. enforce 함수=`is_conversion_allowed_for_blog`→`_conversion_gosiwon_branch`(고시원+삐딱요식업+우삼집).
- **fill_second_slots 버그 수정**: `_cv_branch`(예약)를 날짜루프 밖으로 빼 실행 전체 공유(안 그러면 slot1에 같은 카테고리 여러 날 배정=30일룰 깨짐). slot0은 원래 정상.
- **오분류 가드 2개**: `_post_is_gosiwon`(고시원 맥락 키워드 필수 + 음식 신호 제외) — '미돈미우 고기집 을지로점' 같은 음식점 지명글이 고시원으로 새는 것 방지(을지로 26→15). `_cooldown_food_category`(가맹/창업/모집 신호=삐딱 전환 가드) — 가맹모집글이 문래/연남 지명으로 요식업/우삼집에 새는 것 방지.
- 검증: 실데이터/실RSS에서 간석오거리(인천) 34건 차단(전엔 2건), collect 6.2s. 적대적 코드리뷰 22에이전트.
- **남은 한계(미해결)**: 삐딱요식업/우삼집 수동글이 제목에 브랜드/지명 키워드(문래/연남/우삼집) 없으면 못 잡음(detect_biz_keyword 키워드 의존). 고시원은 alias 풍부해 커버 좋음. 실제 식당글은 보통 상호 들어가 실무상 양호.
