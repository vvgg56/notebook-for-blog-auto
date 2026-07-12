---
name: 마스터 메모리 레포지토리 (vvgg56/claude-memory)
description: 대표님의 전체 프로젝트 메모리/feedback/reference의 마스터 저장소. 다른 PC들과 공유되는 진짜 메모리는 여기.
type: reference
originSessionId: 0f0bd2ec-5b18-4509-827d-8db3b435d280
---
대표님의 통합 메모리 마스터: **https://github.com/vvgg56/claude-memory** (private repo).

이 노트북에는 `C:\Users\장영훈\Desktop\claude-memory\`로 클론되어 있음 (2026-05-07 클론). MEMORY.md가 인덱스, feedback_*/project_*/reference_*/skill_*/user_*.md 파일들이 본문.

**How to apply:**
- 대표님이 "이전에 X에 대해 이야기했잖아" 류로 과거 컨텍스트 언급 시, 먼저 마스터 레포에서 grep — 특히 `project_blog_publisher.md` (50KB, v17~v41 발행봇 패치 이력 전체), `MEMORY.md` (인덱스)
- 발행봇/블로그/Edge/프록시/ESvpn 관련 함정은 마스터 레포의 reference_*.md 에 풍부함 (예: `reference_chromium_proxy_bypass.md`, `reference_edge_profile_mapping.md`, `reference_selenium_react_editor.md`)
- 이 auto-memory 디렉토리(`~/.claude/projects/.../memory`)는 **이 노트북의 로컬 보조 메모리**. 마스터 레포와는 자동 동기화되지 않음 — 마스터 레포는 `setup_new_pc.bat`이 등록하는 별도 스케줄러(20분마다 sync_memory.py)로 갱신됨
- 🆕 **이 노트북 전용 메모리 레포 = `vvgg56/notebook-for-blog-auto`** (private, 2026-07-13 생성). 이 auto-memory 디렉토리(`~/.claude/projects/C--Users----/memory`) 자체가 git 레포로 init됨 → origin=notebook-for-blog-auto. **마스터(`claude-memory`)가 2.5개월치 41파일 충돌로 병합 불가**라(다른 PC들과 갈라짐), 대표님 지시로 노트북 메모리는 이 별도 레포에 백업. 앞으로 이 노트북 메모리 갱신 시 `cd ~/.claude/projects/C--Users----/memory && git add -A && git commit && git push`(identity=장영훈/changgumsu@gmail.com, 토큰은 remote URL에 박힘). 마스터 레포 충돌은 미해결(별도 정리 필요 시).
- 신규 메모리가 다른 PC에서도 유효한 일반 학습이면 → 마스터 레포에 기록 요청
- 이 노트북 한정 컨텍스트(예: EXE 경로, 로컬 Edge 프로필 상태)면 → auto-memory에 기록
