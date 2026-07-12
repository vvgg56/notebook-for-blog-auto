---
name: reference_tabpublisher_quote_feature_port
description: 직원 인용구(quotation) 패치를 TabPublisher로 이식 분석 — GO 권장, 구현 계획(2026-07-09)
metadata:
  node_type: memory
  type: reference
  originSessionId: ba2ebbaf-956e-40ee-a102-5a73c7774bcd
---

**배경**: 직원분이 "소제목 인용구 적용" 자동발행 프로그램 완성(`C:\Users\장영훈\Pictures\블덱스라이터_확장프로그램` + zip, 2026-07-09 v1.2). "1. 소제목" 번호줄 → 네이버 인용구(quotation) 블록 변환. 대표님이 우리 TabPublisher GUI에 이식 가능한지 문의. **워크플로우 5에이전트 분석 → GO 권장(2026-07-09, 미구현·대표님 승인 대기).**

**왜 쉬운가**: 직원 `lib_blogdex.js`는 원래 우리 `cdp_client.py JS_SET_BODY`에서 포팅된 것(자체 주석 명시)이라 SE3 컴포넌트 구조(text/paragraph/textNode/documentTitle) 문자단위 동일. `mkId`(lib_blogdex.js:198) == `makeId`(cdp_client.py:75) 동일. 직원 인용구 코드는 카페확장 실기기 검증된 완성본 → 거의 복붙 이식.

**우리 원고 소제목 형태 = 딱 맞음(실파일 확인)**: `tab_publisher/work/.../article.txt`에 "1. 앱 설치 및 준비 조건" 등 정확히 "N. 소제목"(숫자+점/괄호+공백+텍스트, 한줄단독). 직원 `isHeadingShape`(lib_blogdex.js:239-246, 정규식 `/^\d{1,2}[.)]\s*\S/`, bareOf 3~45자, 개행없음, 사진마커 제외)가 노리는 형태 그대로 → 판정식 수정 불필요. 커버리지 91%(무번호 소제목 9%는 미적용=일반문단, 손해 0). 우리 원고 77% `[목차]` 보유 → 2중가드 필수(lib_blogdex.js:241 개행가드 + 247-264 재등장 dup가드).

**이식 방식 = 직원 JS 거의 그대로 + 입력 어댑터 1개**. quoteComp '맨몸' 핵심: textNode에 style 키 없음(qSize 선택 시만 `{fontSizeCode}` 하나), paragraph에 style 없음 → 네이버가 layout별 기본모양 자동적용(스타일 주입하면 밋밋해짐=구버전 버그). 6종 layout: default(따옴표)/quotation_line/quotation_underline/quotation_bubble(말풍선)/quotation_postit(포스트잇)/quotation_corner. 크기 fs15/16/19/24/28. 안전장치: setDocumentData 거부/미정착(재조회 검증) 시 일반 굵은 문단 폴백=소제목 유실 0.
- ★입력 어댑터: 직원은 article.body 평문 blocks에서 소제목 판정, 우리 주경로는 body_html. 소제목엔 서식마커 0건(985개 전량 grep)이라 평문↔HTML 소제목 텍스트 100% 일치 → 소제목 문단만 quotation 교체, 나머지 HTML 그대로 합류.

**구현 변경목록**: cdp_client.py JS_SET_BODY(:67-113)에 quotation 로직 이식(폴백계산:104~조립:108 사이, `__QUOTE__` placeholder) + set_body(:594) quote_on/style/size 파라미터 + tab_gui.py QUOTE_STYLES/QUOTE_SIZES 상수·self.quote_cfg·_build_settings 위젯 3개·_fill(:1571) 전달·config.json `quote{enabled,style,size}` + v1.1.43.

**남은 리스크 = 실기기 1건**: 네이버 '블로그' 에디터가 raw CDP setDocumentData로 quotation 받는지(카페만 검증됨). 폴백 있어 최악=일반 굵은문단(미관만, 데이터 손실 X). 실발행은 대표님 승인 필요(CLAUDE.md).

**대표님 결정(추천값)**: ①기본 끔→실발행1건 확인후 켬(꺼두면 종전 100%동일) ②모양 default(따옴표) ③크기 자동(빈값). 작업량 반나절~1일 + 검증 반나절. 관련 [[reference_tab_publisher_no_selenium]] · [[reference_tabpublisher_v132]].

**✅ 2026-07-09 구현 완료 = TabPublisher v1.1.43** (대표님 "바로바로 해줘"). 
- `cdp_client.py`: `JS_SET_BODY` IIFE를 async로(evaluate awaitPromise=true라 안전) + `applyQuotes`(qBareOf/qIsHeading/tocSkip 2중가드/qComp 맨몸) 이식. bodyComps(HTML/평문 어느 경로든) 빌드 후 소제목 문단만 quotation 컴포넌트로 분리. setDocumentData 후 setTimeout(400)+재조회로 정착검증→미정착/거부 시 origComps(일반문단)로 폴백=소제목 유실 0. `set_body(body, body_html, quote_on, quote_style, quote_size)` + placeholder `__QUOTE_ON__/STYLE/SIZE` 치환.
- `tab_gui.py`: QUOTE_STYLE_LABELS(6종)/QUOTE_SIZE_LABELS 상수, __init__ `self._quote_on/_quote_style/_quote_size`(config.quote), 설정탭 체크박스+모양·크기 콤보+`_apply_quote`(config 영속저장), `_fill`이 set_body에 전달(연속/단일/원격 공통). 발행로그에 "인용구 N개(layout)"/"⚠인용구폴백:사유".
- `config.json`: `quote{enabled:false, style:'default', size:''}` (기본 OFF).
- 검증: py_compile OK + **node 로 실제 JS_SET_BODY에서 인용구함수 추출→실원고(41블록) 테스트 ALL PASS**(isHeadingShape 7케이스·마커제거·applyQuotes 4소제목 변환·맨몸구조·tocSkip 1차(개행)+2차(분리블록 재등장) 가드). 이스케이핑 OK.
- **★남은 것 = 네이버 블로그 실발행 1건으로 quotation 정착 육안확인**(카페만 검증됨, 폴백 있어 최악=일반문단). 켜는 법: 설정탭 체크 or config quote.enabled=true → GUI 재시작/즉시(런타임 self._quote_on). 빌드=v1.1.43 exe, python=`C:\Users\장영훈\AppData\Local\Python\pythoncore-3.14-64`.
