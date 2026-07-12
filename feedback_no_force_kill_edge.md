---
name: 진단 시 Edge 강제 종료 금지 — 세션 복원 유령 탭 발생
description: msedge.exe를 Stop-Process -Force로 죽이면 다음 launch 때 Edge가 그 탭들을 자동 복원함. 발행봇 깨끗한 launch 검증 시 함정.
type: feedback
originSessionId: 0f0bd2ec-5b18-4509-827d-8db3b435d280
---
Edge launch 동작을 PowerShell smoke test로 검증할 때, **`Stop-Process -Force`로 msedge.exe를 종료하지 말 것**. Edge는 정상 종료되지 않으면 Sessions 폴더에 탭 상태를 남기고, 다음 launch 시 그 탭들을 복원하여 발행봇이 의도한 단일 탭 외에 유령 탭(예: 이전 테스트의 `https://example.com/`, `0.0.0.1` 등)이 함께 뜸.

**Why:** 2026-05-07 BlogPublisher v43 user_data_dir 수정 검증 중 smoke test 2회를 Stop-Process -Force로 종료. 직후 발행봇이 launch한 Edge에 유령 탭 2개 표시 → 대표님이 "창 2개 열림" 보고. 원인은 이전 강제종료 잔재의 `Profile 1\Sessions\Session_*` 파일 자동 복원.

**How to apply:**
- Edge 검증 launch 후 종료가 필요하면, taskbar의 X 클릭 또는 Send-Keys로 정상 close 신호 보내고 잠시 대기
- 부득이 강제 종료했으면 다음 launch 전에 `<user_data_dir>\<Profile>\Sessions\*` 파일 삭제로 복원 데이터 청소 (정상 launch 후엔 자동 갱신됨)
- 발행봇 사용자가 "갑자기 탭이 여러 개 뜬다" 신고 시: 이전 비정상 종료(크래시 / kill / 강제 셧다운) 의심. Profile별 Sessions 폴더 청소가 1순위 처치
