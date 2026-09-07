---
name: reference-jgluna-heading-renumber-v307
description: "2026-08-31 '봇이 글자 빼먹음' 규명: 본문은 원고와 100% 일치, 진범=생성이 목차/본문 소제목을 다르게 뽑음(음절탈락, write.jgluna 미해결) + 워커 renumber 가 번호를 5,2,3,4 로 증폭 → v3.07 시퀀스 재부여로 fix"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 26ca5c6b-9dfb-4547-9c2c-61753aef1a59
  modified: 2026-09-07T08:22:43.190Z
---

**직원 보고(2026-08-31, /remote 발행 후) "발행봇이 글자를 계속 빼먹는다" 규명 결과** — 발행 노트북 run 39·40 의
발행글 33건을 서버 원고(bizops_articles/*.json)와 글자 단위 대조(공백 제거 SequenceMatcher):

1. **본문 글자 소실 0** — 봇(클립보드 붙여넣기)은 글자를 안 빼먹는다. 제목도 33/33 일치.
2. **진범 = 원고 생성(write.jgluna)**: 목차 블록과 본문 소제목이 **서로 다른 글자**로 생성됨(음절 탈락) —
   '연기연금'↔'연연금'(n007)·'술 페어링'↔'술 어링'(jyun1533)·'주의사항'↔'주사항'(mission49)·'투자 전'↔'자 전'(searomin).
   🔴 **생성측 미해결 과제** — 같은 소제목 두 벌이 다르게 나오는 것 자체를 서버(원고 생성/검수)에서 잡아야 근본 해결.
3. **워커 증폭 2곳(v3.07 에서 fix, biz-publisher 커밋)**:
   - `worker.renumber_sections`: 문구 기준 번호 배정 → typo 로 짝 안 맞으면 새 번호(5,6) → 발행 소제목 5,2,3,4/1,2,5,6/1,2,5,4.
     fix = 시퀀스(오름차순 run) 기준 1..N 재부여. 가드: 0/≥20/월-일 날짜줄 통과, 목차 없으면 재시작 즉시 동결,
     같은 번호 반복 동결(동결=남은 줄 원고 그대로, "잘못 고치느니 안 고친다").
   - `human_publisher._split_toc_headings` rule①: 목차 직후 바로 붙은 본문 첫 소제목을 목차로 삼켜 인용구 누락
     → 번호 되돌아가면 목차 수집 종료.
   - 검증 = 실원고 6건 fixture + 적대 12모양 + 코퍼스 33건 멱등성 전부 PASS. 적대 리뷰 2라운드(차단 1+refute 3) 반영.
4. 부수 확인: ljh2111 '區' 소실 = strip_foreign(한자 제거 방어선)의 설계 동작. 링크 URL 텍스트 소실 = 링크 카드 변환(정상).
   IMAGE_FAIL 은 8/31 낮 run 에도 8건 재발(크롬 로그인 여전히 0명 — [[reference-jgluna-branding-expected-ip-404]] 참조).
5. 대조 스크립트 재사용법: 서버 bizops_articles/<sid>.json ↔ `m.blog.naver.com/<blog>/<logNo>` (se-main-container 텍스트,
   `<사진N>`·`{{마커}}` 제거, 공백 squash 후 SequenceMatcher opcodes 의 delete/replace 만 보면 됨).

## 9/7 재신고("한 글자씩 잘림 아직도") → 서버측 수리 레이어 (2026-09-07)
- **실물 sotye 224403625726**: '우선입니.'(다 탈락, `{{/color}}` 직전)·목차 '가이'↔본문 '가격이'·해시태그 끝 ' calc' —
  **셋 다 원고(12900090.json)에 이미 있음, 발행글은 원고와 100% 일치** = 8/31 결론 재확인(생성이 범인, 봇 무죄).
  write.jgluna 생성 = 프롬프트로 {{bold}}/{{color}} 마커까지 통짜 생성(live main.py, 마커 짝 검사는 있으나 음절 탈락 검사는 없음).
- **fix = bizops_compat `_repair_generation`(서빙 전 수리, 배포 대기)**: ①어미 '…니.'→'…니다.'(입·십은 앞 한글 필수,
  습/합/됩/랍 포함, 마커 끼임 변형 처리) ②목차↔본문 소제목 — **번호 같고** fuzzy≥0.7 이고 **부분수열+1~2자 차이**일 때만
  통일(중복음절 쪽 배제 — '가격격이'는 짧은 쪽 승) ③해시태그 꼬리 **소문자** 낱말 토큰만 제거(KRX/ETF 보호).
  검증 = 실원고 35건(파손 5건만 수리·30건 무변경·멱등) + 적대 refuter 반례 6종 전부 무변경. 스니펫 = 세션 스크래치
  repair_snippet.py. 삽입 2곳 = `_generate_article` 의 `_sanitize_body` 직후 + `bz_article` 서빙(캐시 파일도 치유·재저장).
- ⚠ 9/7 sshd 회선 차단(kex reset — 실수기록 패턴)으로 배포 대기. 서버 bizops_compat 는 8/30 이후 타 세션 변경 가능성 →
  배포 전 현재 파일 새로 받아 그 위에 삽입(해시 대조). 발행돼 버린 sotye 글의 '우선입니.' 등은 블로그에서 수동 수정 필요.
- 토스트(알림) fix 실측 완료: 9/6~7 '덮고 있음' 0건·발행 81건 정상.
