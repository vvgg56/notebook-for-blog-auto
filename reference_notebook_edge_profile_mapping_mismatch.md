---
name: reference-notebook-edge-profile-mapping-mismatch
description: 이 노트북 Edge 프로필↔네이버계정 매핑이 config.json(메인PC 기준)과 불일치 — 연속발행 시 엉뚱한 블로그 발행 위험
metadata: 
  node_type: memory
  type: reference
  originSessionId: 804512cd-283c-4942-b4bb-7b2c79613eca
---

**2026-06-20 검증**: 이 노트북(C:\Users\장영훈)의 Edge 프로필 디렉토리 → 네이버 로그인 계정 매핑이 `blog-publisher/config.json` 의 `profile_to_blog` 와 **안 맞는다**.

- 실측: Edge 폴더 **`Profile 1`** 의 실제 로그인 = **`gmm0301`** ("생기부 추월차선 입시 연구소") — config 는 `Profile 1=joywater2` 라고 함. **불일치.** 게다가 `gmm0301` 은 config 의 72블로그 목록에 아예 없음.
- 원인: config(profile_to_blog)는 메인 PC 의 Edge 프로필 생성 순서 기준. 노트북은 프로필을 독립적으로(다른 순서로) 로그인해서 번호가 어긋남.
- 🔴 **위험**: 이 상태로 TabPublisher 연속/단일발행을 `--profile-directory=Profile N` 으로 돌리면 **config 가 가리키는 계정과 실제 로그인 계정이 달라 엉뚱한 블로그에 발행**된다. 발행 전 반드시 실제 매핑 확인.

**GUI 라벨 ↔ Edge 폴더** (이건 일관됨): Default=프로필1, Profile 1=프로필2, Profile 2=프로필3 ... (Local State 프로필명도 "프로필 N", 네이버계정은 안 드러남).

**실제 계정 확인법(읽기전용, 글 안 씀)**: Edge 를 `--profile-directory=<dir> --remote-debugging-port=9333` 로 띄워 `https://blog.naver.com/MyBlog.naver` 로 이동 → 리다이렉트된 `blog.naver.com/{id}` 의 id 가 그 프로필의 실제 계정. 도구 = `Desktop\blog-publisher\_identify_profile.py "Profile N"`. 로그인 안 됐으면 `nid.naver.com` 으로 튕김.

**해야 할 일**: 발행 전 노트북 실제 프로필→계정 맵을 만들어(각 프로필 MyBlog 스캔) config 를 노트북용으로 교정하거나, 발행 직전 URL 의 blog_id 일치 가드로 차단. fill 검증/발행은 joywater2 가 실제 어느 프로필인지 확정 후에.

관련 [[reference-vscode-extension-bypass-permissions]] · TabPublisher 소스 `Desktop\blog-publisher` · config `profile_to_blog`/`profile_labels`.
