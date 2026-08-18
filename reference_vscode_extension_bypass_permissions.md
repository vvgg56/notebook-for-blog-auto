---
name: reference-vscode-extension-bypass-permissions
description: 이 노트북은 VS Code 확장으로 Claude Code 실행 — bypass 권한은 ~/.claude/settings.json 이 아니라 VS Code 설정(claudeCode.*)이 지배
metadata: 
  node_type: memory
  type: reference
  originSessionId: 804512cd-283c-4942-b4bb-7b2c79613eca
---

이 노트북(C:\Users\장영훈)은 Claude Code 를 **VS Code 네이티브 확장**으로 실행한다 (`CLAUDE_CODE_ENTRYPOINT=claude-vscode`). 다른 PC들은 터미널 CLI.

**핵심 함정 (2026-06-19 진단):** 확장의 권한 모드는 `~/.claude/settings.json` 의 `permissions.defaultMode: bypassPermissions` 를 **따르지 않는다**. CLI 에선 그게 먹지만 확장은 무시 → 매 툴콜마다 permission 프롬프트. 그래서 "다른 PC는 자동승인 되는데 이 노트북만 안 됨" 증상이 난다.

**확장의 권한 모드를 지배하는 곳 = VS Code 사용자 설정**
`C:\Users\장영훈\AppData\Roaming\Code\User\settings.json` 에:
```json
"claudeCode.allowDangerouslySkipPermissions": true,   // 이게 있어야 bypass 모드가 선택지에 뜸 (게이트)
"claudeCode.initialPermissionMode": "bypassPermissions" // 새 세션이 bypass 로 시작
```
(이미 `claudeCode.preferredLocation` 으로 `claudeCode.*` 네임스페이스 사용 확인됨.)

**적용 방법:** 위 키 추가 후 **창 다시 로드** 필수 (`Ctrl+Shift+P` → "Developer: Reload Window"). 현재 떠 있는 세션은 설정만 바꿔선 안 바뀌고, 하단 권한모드 인디케이터(또는 Shift+Tab)로 수동 "Bypass permissions" 선택하거나 리로드/새 세션이라야 적용.

**진단 체크리스트 (또 "프롬프트 계속 떠요" 신고 시):**
1. `$env:CLAUDE_CODE_ENTRYPOINT` == `claude-vscode` 면 → 확장. ~/.claude/settings.json 말고 **VS Code 설정** 본다.
2. `~/.claude/settings.json` + `settings.local.json` 의 `defaultMode/skipDangerousModePermissionPrompt` 는 CLI 용 (이미 둘 다 bypassPermissions 박혀있음). 확장엔 무관.
3. managed-settings.json / HKLM·HKCU `Policies\ClaudeCode` 정책 차단 없음 확인됨.
4. 진짜 레버 = VS Code `settings.json` 의 위 2개 `claudeCode.*` 키 + 창 리로드.

관련 [[settings-local-default-mode]] 와는 다른 축 — 그건 CLI 의 project-local override, 이건 확장 vs CLI 차이.

## ECONNRESET / `Clauding…` 고착 (2026-08-18 복구)

**실측 증상:** Claude Code 2.1.234가 `/v1/messages` 첫 청크를 받은 직후 `ECONNRESET`을 반복했다.
로그상 10회 스트리밍 재시도와 non-streaming 폴백까지 모두 실패했고, UI는 `Clauding…`에 고착했다.
`curl.exe -I https://api.anthropic.com`은 정상이고 로그의 `isNetworkDown=false`였으므로 인증/전체 인터넷
장애가 아니었다. `claude.exe` TCP가 전부 IPv6 `2607:6bc0::10:443`인 것을 확인했다.

**확정 대조:**
- 직접 연결: 답변 `CONNECTION_OK`까지 수신 후 스트림 종료에서 exit 1 + ECONNRESET.
- localhost IPv4 프록시 연결: `PROXY_OK`, 이후 3회 연속 OK, 모두 exit 0.

**현재 영구 복구 구성:**
- 프록시: `C:\Users\장영훈\.claude\claude_ipv4_proxy.py`, `127.0.0.1:17654`.
- VS Code 사용자 설정 `claudeCode.environmentVariables`: `HTTPS_PROXY`와 `HTTP_PROXY`를
  `http://127.0.0.1:17654`로 지정.
- 로그인 자동시작: `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`의
  `ClaudeCodeIPv4Proxy` (`pyw.exe -3 ...claude_ipv4_proxy.py`).
- 보안 범위: localhost만 listen, 허용된 Anthropic/Claude/Statsig/Sentry 호스트의 443 CONNECT만,
  upstream DNS/socket만 IPv4 강제. TLS 해독/중간자 없음.

**재발 점검:**
1. `Get-NetTCPConnection -LocalPort 17654 -State Listen`으로 프록시 확인.
2. VS Code settings.json의 두 proxy 환경변수 확인.
3. 프록시가 없으면 `pyw.exe -3 ~/.claude/claude_ipv4_proxy.py` 재실행.
4. 이전 UI가 계속 돌면 해당 `claude.exe` 자식만 종료하고 새 메시지. VS Code 전체/확장호스트 강제종료는
   Codex 세션까지 끊으므로 금지.
