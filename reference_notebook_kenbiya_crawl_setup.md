---
name: notebook-kenbiya-crawl-setup
description: 이 노트북=가론지 켄비야 주 크롤러 겸용. 04:00 가드 크롤 + 발행 겹침 방지(후순위) + PC1 폴백 호환
metadata: 
  node_type: memory
  type: reference
  originSessionId: 3bfd2176-a887-4f9a-8ab7-d94f7758748f
  modified: 2026-08-16T12:40:57.195Z
---

2026-08-16 세팅: 이 발행 노트북이 garonge.com 켄비야 매물 수집의 주 크롤러가 됨 (PC1은 00:20 폴백).

- repo: `C:\workspace\garonge` (vvgg56/garonge, PAT 내장 URL — claude-memory 클론과 같은 방식)
- Python 3.12.10 = `%LOCALAPPDATA%\Programs\Python\Python312\python.exe` — **PATH에는 스토어 스텁만 있어 `python` 커맨드 안 먹힘, full path 필수**. requests/bs4/selenium 설치됨.
- Task `GarongeKenbiyaDaily` 매일 **04:00** (대표님 "새벽 4시쯤", 원래 21:30에서 변경) → `tools/kenbiya_guarded_daily.py` (실행제한 6h, StartWhenAvailable, WakeToRun)
- Task `GarongeVpnAutoDisconnect` 10분마다 → **pythonw** + CREATE_NO_WINDOW (발행 노트북에서 콘솔 창 포커스 강탈 방지)
- **가드 = 대표님 지시 "발행과 겹치면 안돼, 켄비야 후순위"**: 시작 전 발행 감지 시 대기(최대 90분→포기=PC1 폴백), 크롤 중 감지 시 즉시 크롤 kill+VPN 해제. 감지신호 = ①GUI exe `--worker` 서브프로세스(워커는 유휴 10분 자동종료) ②python worker.py ③msedge/chrome `BotData` 프로필. 패턴은 자기매칭 오탐 방지 위해 분리조립(실측 사고).
- PC1 00:20 폴백 체커(run_date=now-4h, 36h 윈도우)는 04:00 이전과 그대로 호환 — 수정 불필요 (검증 완료).
- 크롤 push는 rebase 방식이라 서버 crawl_daily(05:05~07시)와 겹쳐도 안전. 단 수동 push는 그 시간대 금지.
- 미완(2026-08-16 기준): ExpressVPN 설치 — winget UAC가 원격 세션에서 2회 자동취소 → 바탕화면 `ExpressVPN_설치.exe`(54MB) 받아둠. 대표님이 설치+로그인해야 수동 테스트 1회 가능. 상세는 garonge repo `skill.md` 최상단.

🔴 **ExpressVPN 켜지면 Claude(VS Code) API가 끊긴다** — 전체 터널이라 Claude 통신도 일본 경유가 되며 먹통. 해결 = ExpressVPN **분할 터널링**: Code.exe(`%LOCALAPPDATA%\Programs\Microsoft VS Code`)와 claude.exe(`~\.local\bin`)를 "VPN 우회"로 등록 (2026-08-16 대표님이 설정 완료). **함정: 설정 후 대상 앱(VS Code) 완전 재시작 필수** — 안 하면 기존 프로세스는 여전히 터널로 라우팅돼 그대로 먹통(실측 2회). 크롤 테스트 중 Claude가 조용해지면 이것부터 의심.

관련: [[blog-dashboard-remote-control]], [[jgluna-gui-v251-bizops]]
