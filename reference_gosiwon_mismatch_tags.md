---
name: gosiwon-mismatch-tags
description: "브랜딩 고시원 제목-본문 지점 불일치 원인+패치(서버 /tmp 대기), 발행봇 태그 미구현 확정 (2026-08-24)"
metadata: 
  node_type: memory
  type: project
  originSessionId: ac06fafe-8ee0-43a0-8ddd-e623889848b2
  modified: 2026-08-24T06:58:14.793Z
---

**① 고시원 제목-본문 지점 불일치 (blog.jgluna.com 브랜딩 haerieva/gosiwon)**: 제목=키워드 풀 50개 순환, 본문 지점=날짜 toordinal%3 로테이션(종로점/인천점/답십리점) — 서로 독립이라 불일치 (main.py:4774, :4954 두 곳). 을지로점은 물리 지점 아님=종로점 소스의 마케팅 분기(write 내부 40%, blog-writer main.py:4569). 인천점=간석오거리점 alias.
- 패치 = `_branding_gosiwon_branch(title, date_str)` 헬퍼(제목→infer_gosiwon_branch, 을지로점→종로점, 폴백=기존 로테이션)로 두 곳 교체. **배포 상태: 서버 /tmp/main_gosiwon_patch.py 업로드 완료, 교체+재시작은 권한 차단으로 대표님 실행 대기** (2026-08-24). 백업명 규칙: main.py.bak_20260824_gosiwon_branch.
- 불량 원고 4건 = gosiwon 08-12/08-21/08-24/08-25 (전부 planned·미발행) → 패치 후 재생성 필요: POST /api/branding/article/generate {business:"gosiwon",date} → 완료 후 /api/branding/images/generate. 서비스=blog-dashboard.service.
- 단체발행(schedule_articles.json) 경로는 business에서 지점 파생이라 정상.

**② 발행봇(biz-publisher) 태그 = 미구현 확정(회귀 아님)**: 원고 API에 tags 필드 없음, 태그=body 끝 해시태그 한 줄(28~30개). 워커 Ctrl+V 붙여넣기론 네이버가 태그 등록 안 함(발행글 tagList 빈 것 실측). 발행 플로우(worker.py 2159~2214)=발행→카테고리→발행뿐, '태그 편집' 입력 단계 없음. 구현안: cdp_human.py에 enter_tags(OS 실입력, mouse_publish/select_category 패턴 복제) + worker.py 2199/2200 사이 호출 + 재시도 경로(2263~2273, ESC×2가 레이어 닫음)에도 재입력. 대표님 결정 대기: 구현 여부 + 본문 끝 #평문 줄 중복 처리.

서버 SSH 주의: 단발 ssh 접속을 단시간 수십 회 하면 3.38.22.7이 kex reset으로 IP 차단(수십 분). 원격 조사는 명령을 묶어 접속 횟수 최소화.
