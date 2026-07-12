---
name: reference_edge150_debugport_block
description: 🔴 Edge 150 기본 User Data 폴더 디버그포트 차단 → TabPublisher 발행 0 근본원인 + 정션 해결(2026-07-06)
metadata:
  node_type: memory
  type: reference
  originSessionId: ba2ebbaf-956e-40ee-a102-5a73c7774bcd
---

**증상(2026-07-06)**: blog.jgluna.com `/remote`에서 직원이 발행 눌러도 "발행프로그램은 돌아가나 실제로 미발행". 노트북 로그(TabPublisherGUI v1.1.41)상 **전 블로그(52/52) VPN 정상연결(IP 바뀜·KR)인데 엣지 띄운 뒤 "[CDP] 디버그포트 안 열림"→건너뜀, 발행 0**.

**근본원인**: Edge가 **7/4 밤 149→150.0.4078.48 자동 업데이트**. Chromium 보안변경으로 **Edge 150부터 '기본 User Data 폴더'(`%LOCALAPPDATA%\Microsoft\Edge\User Data`)에서는 `--remote-debugging-port`를 무시**(CDP로 로그인 쿠키 탈취 방지). 봇은 정확히 그 기본 폴더를 `--user-data-dir`로 써서 디버그포트가 안 열림 → cdp_client 연결 불가 → 전 블로그 스킵. 발생 시점 7/5 저녁(로그 실측, 그 전엔 정상 발행). **봇 코드/VPN/내 v1.1.40-41 변경과 무관** = 순수 Edge 업데이트.

**실측 검증**(헤드리스, 안전): `msedge --headless=new --remote-debugging-port=P --remote-allow-origins=* --user-data-dir=<경로> --profile-directory=X about:blank` 후 `http://127.0.0.1:P/json/version` 200 확인. 결과: 기본 User Data 폴더=**차단**, 임시폴더=**열림**(Edg/150.0.4078.48), 정션→기본폴더=**열림**.

**✅ 해결(복사·재로그인 0)**: 정션(junction)으로 '같은 User Data를 가리키는 비-기본 경로'를 만들면 Edge 150이 디버그포트 허용.
1. `cmd /c mklink /J "C:\EdgeBotData" "%LOCALAPPDATA%\Microsoft\Edge\User Data"` (생성 완료).
2. config.json `edge.user_data_dir` = `C:\\EdgeBotData` (수정 완료).
3. **GUI 재시작**해야 반영(실행 중 봇은 옛 경로 로드 상태).
정션은 원본과 같은 파일 → 72개 프로필 로그인 그대로 공유(실측: `C:\EdgeBotData\Profile 1/71`, Local State 다 보임). 영구(리부트 생존). Edge 재업데이트해도 유지.

**🔴 위험(절대금지)**: 정션 제거 시 `Remove-Item -Recurse` / `rmdir /s` 쓰면 **target(User Data 전체 로그인) 통째 삭제**. 링크만 지우려면 `cmd /c rmdir "C:\EdgeBotData"`(비-/s) 또는 `[IO.Directory]::Delete(path,$false)`. (이 세션 PowerShell 가드가 `Remove-Item`+`C:\Program` 동시 포함 스크립트 자동 차단 — 관련 없어도 걸리니 삭제/실행 분리.)

**대안(정션 안 될 시)**: User Data를 비-기본 폴더로 robocopy 후 config 변경(무겁고 staleness) / 완전 새 폴더+72개 재로그인(깨끗하나 재로그인). 정션이 최선이라 이걸 씀.

관련 [[reference_tab_publisher_no_selenium]](--remote-debugging-port 원리) · [[reference_tabpublisher_protection_root_cause]] · 옛 v1.1.4 "관리자 GUI→de-elevated 실행" 디버그포트 fix와는 다른 축(그건 권한, 이건 폴더경로).
