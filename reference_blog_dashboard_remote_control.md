---
name: reference_blog_dashboard_remote_control
description: "blog.jgluna.com 대시보드 = vvgg56/blog-dashboard, 서버 3.38.22.7, 원격발행제어 + repo↔서버 divergence 경고"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 804512cd-283c-4942-b4bb-7b2c79613eca
---

**blog.jgluna.com 대시보드** = GitHub `vvgg56/blog-dashboard` (FastAPI `backend/main.py` + `index.html`). 2026-06-23 이 노트북 `~/Desktop/blog-dashboard`에 클론(토큰 리모트). 대표님 "여기서 작업" 명시.

**서버/배포 (2026-06-23 확인):**
- 운영 서버 = **`ubuntu@3.38.22.7`** (AWS Lightsail 서울). `/home/ubuntu/blog-dashboard/backend`, service `blog-dashboard`, python3.12. blog.jgluna.com은 Cloudflare(104.21.x/172.67.x) 뒤.
- ⚠️ `scripts/deploy_production.ps1` 기본 HostName=`43.202.192.31`은 **stale(불통)** → 실제는 3.38.22.7. 배포 시 `-HostName 3.38.22.7`.
- SSH 키 = `C:\Users\장영훈\.ssh\LightsailDefaultKey-ap-northeast-2.pem` (대표님이 Downloads로 줘서 복사·icacls 적용). Git Bash ssh는 한글 사용자명 known_hosts 못써서 `-o StrictHostKeyChecking=accept-new` 필요(경고 무해).
- 배포 = main.py SCP + `sudo systemctl restart blog-dashboard`. 서버 `config.json`은 대표님 어드민 튜닝 정본 → 덮어쓰기 금지.

**🔴🔴 repo↔서버 divergence (배포 전 필독):** 서버 main.py는 git 아님(`no-git`)이고 GitHub repo와 어긋남. 서버에만 있는 핫픽스(스케줄 백그라운드 생성 asyncio + `fill_second_slots`, 14줄)가 repo에 미커밋. repo엔 미배포 72줄 존재. → **`deploy_production.ps1`로 repo 통째 배포하면 서버 핫픽스가 사라짐.** 변경분만 patch로 서버 현재 main.py 위에 얹어야 안전(`git diff <base> -- main.py` → 서버사본에 `git apply`). 2026-06-23 원격제어는 이 방식으로 배포함. divergence 정식 reconcile 필요(저쪽 세션 협의).

**원격 발행 제어 (2026-06-23 구현·배포):** 관리자페이지 `/remote`(블로그·회차 선택+발행/정지+실시간 상태·로그) → 노트북 발행봇이 폴링 수신 자동발행. 엔드포인트: POST `/api/publish-jobs`(멱등·10분쿨다운·active중복), GET `/api/publish-jobs/next`(원자claim), `/api/control`(정지), PUT `.../status`, POST `/api/bot/heartbeat|logs|shutdown-beacon`, GET `/api/admin/bot-status|bot-logs|publish-jobs`. 인증=노트북 `X-Pub-Secret`(헤더), 관리자=직원토큰/IP화이트리스트. secret=`config.json dashboard.command_secret`(노트북) == 서버 `backend/command_secret.txt`. 노트북측=[[feedback_vpn_required_before_naver_login]] 절대규칙 유지(TabPublisher v1.1.28 remote_agent.py). 노트북 fastapi 설치돼 TestClient 검증 가능.
