---
name: BlogPublisher v43 — 이 노트북에서의 배포 위치 + 2026-05-07 user_data_dir 수정
description: 발행봇 EXE 위치, 노트북 배포 시 config.json 함정, 소스코드는 F드라이브(이 노트북엔 없음)
type: project
originSessionId: 0f0bd2ec-5b18-4509-827d-8db3b435d280
---
발행봇 EXE 번들이 이 노트북에 배포되어 있음. 소스코드는 본 PC `F:\blog-publisher\`에 있고 **이 노트북엔 F드라이브 자체가 없음** → 소스 수정 필요 시 본 PC에서, 노트북에선 config/profile 정리만 가능.

**Why:** 대표님이 노트북에서 Edge가 안 열린다고 신고. 진단 결과 노트북 배포 시 documented 셋업 단계(`edge.user_data_dir` 노트북 사용자명 맞게 수정)가 누락된 상태였음. 마스터 메모리 `project_blog_publisher.md`의 "위치" 섹션에 명시된 함정.

**How to apply:**
- EXE 위치: `C:\Users\장영훈\Downloads\BlogPublisher_v43\`
- config.json: 같은 폴더, line 11의 `edge.user_data_dir`는 노트북 사용자명(`장영훈`)으로 박혀있음 (zip 기본값은 `C:\Users\PC\...` — 본 PC 기준이라 노트북에선 Edge 즉시 사망)
- Edge 즉시 종료 증상 1순위 의심: config.json `user_data_dir` 사용자명 mismatch
- 로그 함정: `logs/YYYYMMDD.log`의 "엣지 실행됨" 메시지는 launch 호출만 보고 종료 여부 미확인. msedge.exe PID 4초 후 생존 검사로 진단해야 함
- 로컬 Edge 프로필: `Default` + `Profile 1`만 존재 (Profile 2~72은 발행봇 GUI "최초 1회 로그인" 탭에서 순차 로그인 필요)
- EXE 직접 수정 불가, 소스 수정은 본 PC에서
