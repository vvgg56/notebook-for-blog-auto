---
name: reference_dashboard_generation_algo
description: blog.jgluna 제목 생성 알고리즘 — 정보:전환 비율 '일간 기준' + rank.jgluna.com 순위기반 전환글 제목(상위 미달 우선)
metadata:
  node_type: memory
  type: reference
  originSessionId: 804512cd-283c-4942-b4bb-7b2c79613eca
---

blog.jgluna.com(서버 3.38.22.7, [[reference_blog_dashboard_remote_control]]) 제목 생성 알고리즘 2026-06-28 변경:

**1) 정보:전환 목표 비율 = '일간(그날 전체)' 기준 (커밋 c03b5b0):**
- 설정: `daily_config.info_ratio_pct`(대표님 PIN, `/api/owner-ratio`). 정보글 %(현재 30) → 전환 (100-30)%.
- `_generate_day_impl`: 그날 전체 블로그(기존 칸+새 칸)에서 정보글이 info_ratio_pct% **floor** 되게 전환 개수 상한. 기존엔 conversion_count 만 썼고 info_ratio 는 14일 재생성에서만 적용됐음 → 이제 날짜별 생성(+생성/재생성/일간 자동)에도 적용.
- `_generate_schedule_impl`: 옛 24글 윈도우(`calc_target_split_14`, 최근10글 보정) **폐기**, 블로그당 고정 `round((100-pct)/100*14)` 전환 → 날짜별로도 같은 비율. (`calc_target_split_14`/`count_recent_post_types`/`info_target_24` 는 이제 dead code.)
- 검증: 서버 dry-run day13 → 64글 중 정보 19(30%)·전환 45.

**2) rank.jgluna.com 순위기반 전환글 제목 — 전환 사업체 전체 확대 (커밋 b1c42b7):**
- rank API(`RANK_API_BASE_URL`=https://rank.jgluna.com): `/api/businesses`(id·name·keywords), `/api/priorities`(biz_id→{kw:urgent/important}), `/api/results`(dates), `/api/results/{date}`(biz_id→{kw:[{rank,title,url,has_tracking}]}).
- 등록 사업체 8개: saipay·cleaningplanet·inbyeol·gosiwon·bybonus·garonji·bbidak(삐딱)·blexslider.
- `RANK_BIZ_ID_BY_NAME`(대시보드명→rank id) + `load_rank_data`(10분캐시) + `_rank_underranked_keywords`(tracking 적은 순=상위 못먹은 우선, urgent 우선) + `_make_rank_title`(키워드를 사업체 제목패턴 topic 으로) + `build_rank_title_pool_for_business`. `build_daily_convert_pool` 가 rank 제목 우선→부족분 BIZ_TITLES(rank 실패시 기존동작=안전).
- 적용: 청소플래닛·인별마케팅·바이보너스·가론지·블덱스라이터·삐딱(전환). **제외**: 사이페이(기존 전용 쿠폰패턴 `build_saipay_rank_titles_from_payload`), 고시원(지점명/쿨다운 로직 깨짐 방지), 우삼집·삐딱요식업(rank 키워드 없음).
