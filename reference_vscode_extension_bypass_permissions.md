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
