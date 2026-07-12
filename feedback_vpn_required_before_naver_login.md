---
name: feedback_vpn_required_before_naver_login
description: 절대규칙 — VPN 검증 연결 없이는 네이버 로그인/발행 금지. 보호조치의 진짜 원인이었음
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 804512cd-283c-4942-b4bb-7b2c79613eca
---

발행봇(TabPublisher)에서 **VPN(ESvpn) 검증 연결 없이는 네이버 로그인/발행 절대 금지** (2026-06-21 대표님 지정).

**Why:** 그동안 블로그들이 네이버 보호조치(어뷰징 차단) 걸린 진짜 원인 = VPN 없이 맨 IP(노트북 본인 IP)로 네이버 접속·발행. config `esvpn.active_vpns`(수동 목록)가 stale 해서 실제 VPN 결제된 블로그(ho_yu 등)도 목록에 없으면 `[VPN없음]`으로 떠서 맨 IP 발행됐음. 첫 로그인이든 발행이든 VPN 없는 네이버 접속은 1회도 있으면 안 됨.

**How to apply:**
- vpn_id 없거나 VPN 검증 실패하면 그 블로그 엣지를 절대 열지 않음(발행·단일·🔑최초로그인 모든 경로). 코드 게이트 = `tab_gui.py _launch_blog()` 최상단 + `self._vpn_verified` 플래그(v1.1.24).
- "블로그별 VPN 사용" 체크 꺼도 VPN 강제(규칙이 체크박스보다 우선).
- VPN 계정명 = `blog_to_vpn_override` 우선, 없으면 blog_id 와 동일(미배정 26개 전부 로그인ID=VPN ID 확인). active_vpns 에 없다고 [VPN없음] 처리 금지.
- 실제 결제여부는 ESvpn 로그인 때 판정(고정IP=발행 / 서비스미사용=텔레그램+건너뜀 / 연결실패=건너뜀).

상세 = blog-publisher `SKILL.md` 최상단 "🔴🔴 절대 규칙 — VPN 검증 연결 없이는 네이버 로그인/발행 절대 금지". 관련 [[project_blog_publisher_notebook]], [[feedback_no_force_kill_edge]].
