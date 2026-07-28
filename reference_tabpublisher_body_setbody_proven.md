---
name: reference_tabpublisher_body_setbody_proven
description: TabPublisher 본문/제목 입력=set_body(setDocumentData)가 검증된 방식. CDP타이핑·synthetic paste는 iframe에 안 닿아 실패 — 재시도 금지
metadata: 
  node_type: memory
  type: reference
  originSessionId: ba2ebbaf-956e-40ee-a102-5a73c7774bcd
  modified: 2026-07-28T07:21:57.411Z
---

발행봇 TabPublisher(`~/Desktop/blog-publisher/tab_publisher/gui`)에서 네이버 SmartEditor **본문/제목 입력의 유일하게 검증된 방식 = `cdp.set_body`/`cdp.set_title`** (JS_SET_BODY의 setDocumentData — SmartEditor 자체 JS API를 `_seWin()` iframe 컨텍스트에서 호출). 수개월 안정 동작.

**하면 안 되는 것(2026-07~28 여러 번 실패 확인):**
- **CDP Input**(`Input.insertText`/`dispatchKeyEvent`/`dispatchMouseEvent`)은 SE3 에디터가 **iframe(mainFrame) 안 contenteditable**이라 **안 닿음**. 포커스가 "OK"로 떠도 타이핑이 씹힘. 글자당 CDP 호출을 길게 하면 웹소켓 `WinError 10053`(연결 끊김)까지 남(1981자에서 죽음).
- **synthetic ClipboardEvent paste**(줄단위 붙여넣기 `type_body_paste`)도 본문에선 실패 — `JS_FOCUS_BODY_JS`가 cross-frame `activeElement` 확인 때문에 ok:false 반환 → 붙여넣기 시도조차 못 함. (사진 삽입용 `JS_PASTE_IMAGE` paste는 별개로 동작함 — 이미지에 한정.)
- 이걸 "누락 방지 = 사람처럼 타이핑" 목적으로 얹었다가 발행 자체가 깨짐. 대표님 크게 화남.

**현재 상태(v1.4.0, 2026-07-28):** 대표님 지시로 '타이핑이 iframe 에 실제로 닿게' 재설계 완료. **핵심 원칙: 타이핑=사람 세션 신호용, 최종 본문 무결성=set_body 가 100% 보장(항상 실행).**
- **페이지내 타이퍼**(`JS_TYPER_START/STATUS/STOP`, `type_body_typer`): 타이핑 루프를 GUI(글자당 웹소켓 왕복=10053 원인) 대신 **페이지 안 JS setTimeout** 에서 돌림 → 웹소켓 끊겨도 계속, GUI 는 2.5초 폴링만(끊기면 `_reconnect`). 입력=검증된 사진 붙여넣기와 동일한 **paste 직접 dispatch**(activeElement 게이트 없음, 본문 editSurface 만 건드려 제목 무오염), 안 늘면 execCommand 전환, SE3 모델 글자수로 자가검증. 기본 경로.
- **set_body 항상 실행**(v1.4.0 리뷰 반영): 본문은 set_body 가 문서 전체 재구성 → 타이핑 잔재·부분입력·seam 전부 덮음. `has_fmt` 없을 때 set_body 생략하던 옛 로직 제거(그게 seam/관용/마커소실 버그를 실발행에 노출시켰음). ⚠ set_body 는 **제목(documentTitle)은 보존**(본문만 재구성) → 제목 정확성은 `type_title`(제목요소 직접 타겟 paste)→`set_title` 폴백이 책임.
- **트러스티드 CDP 타이핑**(`probe_cdp_typing`+`_type_body_cdp`): 더 사람같은 isTrusted 이벤트. 대표님 아이디어 프로브('ㅁ' 삽입→SE3 모델 검증→제거, 미반영 시 Backspace 청소). `config trusted_typing`(기본 **false**) 뒤. 켜면 프로브→CDP(재연결+400자 중간검증)→실패분 타이퍼로 이어서.
- `type_body` 는 **어떤 예외도 밖으로 안 던짐**(→ set_body 폴백, 배치 지속). 검증: node 로 렌더 JS 문법 + 타이퍼 3시나리오 시뮬(paste정상/exec전환/양쪽실패→에러보고) 통과. **실기기 발행 검증은 대표님 미확인**(로컬 시뮬만).

config: `body_typing`(기본 true), `trusted_typing`(기본 false). 유지 개선: 실마우스 발행, `publish_dwell_sec`, emphasis, 사진 파일탐색기, 재활용 목록, 테스트탭, blogdex 토큰.

**v1.4.1(2026-07-28) 대표님 신고 2건 fix:**
- **라이터 원고 `{{color}}` 등 중괄호 마커가 글에 그대로 노출**: 라이터/write-v2 는 **중괄호** 마커(`{{bold}}/{{color}}/{{highlight}}/{{red}}`)를 씀. `_generate_blogdex`(tab_gui) 변환기가 **꺾쇠 `<>` 만 처리**하고 중괄호는 통과시켜 텍스트로 발행됨. fix=`write_api._writer_markers_to_html` 매핑 이식({{color}}=빨강/파랑/분홍 순환, {{bold}}→b, {{highlight}}→mark, {{red}}→빨강 + 깨진마커 제거 + 평문도 마커0). 라이터 body 의 사진마커는 `<사진N>` → '사진 N' 통일.
- **사진 '존재하지 않는 이미지'(발행물 깨짐)**: 🔴 핵심=**사진 삽입 2방식의 커밋 차이**. `_insert_photos_dialog`(파일탐색기)=마커 위치에 SE3 자체 업로드흐름으로 **제자리 삽입 → 발행 시 커밋 정상**. `_insert_photos_paste`(합성 paste 폴백)=끝에 붙인 뒤 `reposition_photos`가 **setDocumentData 로 문서 재구성 → SE3 업로드 커밋이 깨져 발행물이 '존재하지 않는 이미지'** 가 되는 것으로 유력. dialog 가 정답. dialog 가 실패해 paste 로 폴백하던 원인=**set_body 직후 DOM 렌더 지연**으로 `marker_click`(DOM 기반)이 같은 초에 '사진 N' 못 찾음(모델엔 있어 reposition 은 찾음). fix=`marker_click` 재시도(렌더 대기)+dialog 진입 전 `marker_count`(모델기반) 확인. **실기기 발행 검증 대표님 미확인.**
- **v1.4.2(2026-07-28) 대표님 지시: paste 방식 완전 금지.** 사진=파일탐색기만. `_fill` 에서 dialog 실패 시 **paste 폴백 안 하고 `return False`(이 글 발행 중단)**. `_insert_photos_paste` 는 `config photo_mode="paste"` 명시 때만.
- **v1.4.4(2026-07-28) 대표님 새 사진 플로우 = '일괄 다중선택'**(1장씩 per-photo marker_click 이 DOM 렌더 지연으로 계속 실패 → 폐기). `_insert_photos_dialog` 재작성: 노트북 폴더 복사 → `marker_count`(모델)로 마커 확인 → **사진버튼 1번 클릭(실마우스) → 네이티브 '열기'에서 폴더 이동 후 전체 파일명 다중선택(`_fill_open_dialog_multi`, PNG 원본=잘림 없음) → '사진 첨부 방식' 모달 '개별사진' 실마우스 클릭(`pick_photo_layout`/`JS_PHOTO_LAYOUT_RECT`) → image_count 로 전량 업로드 대기 → `reposition_photos`(모델 기반, 커밋 https 대기)로 '사진 N' 위치 배치 → 마커 정리**. 이 방식은 marker_click(DOM) 을 안 써서 렌더지연 무관, 네이버 정식 업로드라 발행 커밋 정상. ⚠ 네이티브 대화상자 다중선택 = **v1.4.6 에서 Win11 창 fix**: 파일이름 Edit 가 `#32770` 직속이 아니라 `ComboBoxEx32>ComboBox>Edit` 로 깊이 중첩 → `_find_edge_open_dialog` 를 `EnumChildWindows` 재귀로 교체(실제 tkinter 파일창으로 Edit 찾고 WM_SETTEXT 다중경로 입력 readback 검증 통과). `_fill_open_dialog_multi`=전체경로 다중선택 우선→폴더이동+파일명 폴백. **단 IDOK 로 다중 full-path 가 실제 전부 선택·업로드되는지는 실기기 미검증**(readback 까지만 검증). +🔒 고정 테스트(test_fixture.json/테스트사진 폴더)로 반복테스트 세팅됨.

**v1.4.3(2026-07-28) 대표님 신고 4건 '한 방' fix — 근본원인 1개:** 라이터(blogdex) `_generate_blogdex` 가 만든 body_html 에 **블록(`<div>`/`<p>`) 래핑이 없어서**(마커→태그 변환만 하고 `\n` 그대로 둠) set_body 의 HTML 파서(DOMParser)가 **`\n` 을 공백으로 뭉갬**. 그 결과 한 번에 터진 증상: ①'사진 1'+다음 목차 병합('사진11.목차입니다') ②줄간격(line-height) 미적용 ③파일탐색기가 '사진1' 요소를 못 찾아(병합돼서) paste 폴백 → 발행물 깨짐. **fix = `write_api.WriteAPI._writer_markers_to_html(raw)` 그대로 사용**(중괄호마커→HTML + `_wrap_writer_html_blocks` 블록래핑 + line-height, 검증된 jgluna 경로). 이 함수는 `_auto_emphasize_plain`(마커 있으면 skip)·`_strip_generated_preamble` 포함 안전. 평문(타이핑용)은 body_html 의 `</div>`→`\n\n`, `<br>`→`\n` 로 되돌려 파생. **교훈: set_body 에 넘기는 body_html 은 반드시 블록요소로 문단을 감싸야 한다(순수 `\n` 텍스트는 뭉개짐).** +강조 글자크기 config `emphasis.sizes=["fs19"]` 고정. +타이퍼 `_text_for_typer`(빈 줄 보존→엔터2번 줄간격).

**🔴🔴 v1.5.0(2026-07-28) 대표님 핵심 발견 — 누락 원인 = 봇의 '통짜 주입':** 대표님이 블덱스라이터 원고를 **수기로 복붙 + 사진 한 장씩** 넣으니 노출 잘 됨 실증 → 결론: 봇이 `setDocumentData`(set_body 본문 통짜 재작성 + reposition 사진 통짜 재배치)로 문서를 **한 방에 통째로 바꾸는 동작**이 AI/누락 신호. 사람은 절대 안 하는 동작. **대응 = '순차구성 모드'(config `sequential_mode`, `_fill_sequential`)**: set_body/reposition 완전 제거, 본문을 '사진 N' 청크로 나눠 타이핑(줄간격 엔터) + 그 자리에 사진 1장씩 인라인 삽입(`insert_photo_inline`, 단일 파일창, 위치 안 찾고 현재 캐럿). ⚠ **서식(볼드/색/글자크기) 포기**(setDocumentData 로만 가능) — 평문+줄간격만. 누락 잡히면 그때 서식은 사람처럼(선택+툴바) 이식. +**사람행동주입**(config `human_fidget`, `cdp.human_fidget`): CDP 마우스 '궤적' 이동(isTrusted=true라 네이버가 진짜입력 인식, 실 OS커서 안움직여 방해 0)/스크롤/본문에 ㅁㄴㅇㄹ 넣고 지우기/랜덤지연 — 청크 사이·발행 전 체류 중. 둘 다 기본 true. **실기기 미검증**(로컬 청크분할·JS문법·좌표만 검증). 끄면 기존 set_body 모드.

**v1.5.1(2026-07-28) 사진 대화상자 실패 대응 + 🔴 미해결(폴더이동):** 사진 '열기' 대화상자 진단 — v1.5.0 실패는 **pid 필터가 Edge 대화상자 소유 pid 를 msedge 목록서 못 잡아** 대화상자를 '못 찾은' 것(로그 ~11초=찾기 타임아웃, 화면엔 떠있음). fix=`_find_edge_open_dialog` pid 필터 완화(Edge 우선, 없으면 아무 #32770). 입력=`WM_SETTEXT`(readback 검증)→실패시 `SendInput` 실키입력(포그라운드+파일이름칸 실클릭)+`_dialog_capture`(단계별 화면캡쳐 publish_captures/). **🔴 여전히 미해결: 사진 폴더로 '이동'이 안 됨** — 파일이름칸에 전체경로 넣어도 대상 폴더(photo_uploads/YYMMDD/testN 또는 테스트사진)로 이동/파일선택 안 됨, 대화상자가 기본 '스크린샷' 폴더에 머묾. 대표님: "폴더 이동은 위 **주소표시줄(breadcrumb 사진>스크린샷)** 을 눌러야 되는데 코드는 아래 파일이름칸만 건드림". **다음 시도**: ①파일이름칸에 폴더경로만+Enter로 cd → 파일명(또는 Ctrl+A)+Enter 2스텝 ②주소표시줄 직접조작 ③대표님원안(폴더이동→사진클릭→Ctrl+A→Enter 전부한번에 + 커서맨앞→텍스트 끼워넣기 →키로 이미지건너뛰기, 몇번인지 불확실) ④사진을 대화상자 기본폴더에 두기. **실기기 검증 필수(대표님 테스트), Claude 라이브 입력주입(SendInput/SetCursorPos/SetForegroundWindow) 절대금지 — 이번 세션 위반해서 대표님 화남, CLAUDE.md 실수기록 2026-07-28 참고.**

**누락 대응 진행중 + 설계 긴장**: 서식(볼드/색/인용구/강조)은 **set_body(setDocumentData) 로만** 가능 → 서식 글은 set_body 불가피. 타이핑은 그 위에 사람 세션 신호를 얹는 것. 순수 평문+타이핑only 로 누락 실험하려면 emphasis 끄고 set_body 우회 경로가 필요(현재는 항상 set_body). 발행되는 글의 누락 여부 보고 다음 판단. [[reference_tabpublisher_v132]] [[reference_tabpublisher_protection_root_cause]]
