# AAA Starter Kit

> **AI 스터디/실무 팀을 위한 Obsidian + Claude Code + Astro 기반 운영 시스템 템플릿**
>
> 멤버가 Obsidian에 쓰면 → GitHub에 모이고 → AI가 정리해서 → 공개 홈페이지로 나갑니다.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-ready-orange.svg)](https://claude.com/claude-code)

---

## 이 키트는 무엇인가요

실존하는 AI 스터디 팀(AAA TEAM · Selfish Club)이 2026년 3~4월에 운영하면서 만들어낸 **콘텐츠·자동화·배포 시스템 전체**를 누구나 복제해서 쓸 수 있게 추려낸 템플릿입니다.

- **Obsidian vault**를 콘텐츠 원본으로 사용
- **Claude Code 슬래시 명령어**로 분석·링크드인 초안·아카이브·배포를 자동화
- **Astro 정적 사이트**로 공개 폴더만 홈페이지에 배포
- **2-repo 아키텍처**로 원본(비공개 포함)과 공개 사이트를 분리

## 누구에게 유용한가요

- AI/개발 스터디를 운영하는 **운영진**
- 멤버 기록을 주기적으로 공개 아카이브로 내보내고 싶은 **커뮤니티 리더**
- Obsidian을 넘어 자동 배포까지 가고 싶은 **지식 작업자**
- Claude Code를 팀 단위로 도입해보고 싶은 **빌더**

## 구성

```
aaa-starter-kit/
├── README.md              ← 지금 읽고 계신 파일
├── PRD.md                 ← 표준 PRD (제품 요구사항 문서)
├── QUICKSTART.md          ← 5단계 따라하기
├── docs/
│   ├── architecture.md    ← 전체 시스템 아키텍처 설명
│   └── system-overview.html  ← 비개발자용 구조도 (브라우저에서 열기)
└── template/              ← 복사해서 바로 쓰는 vault 템플릿
    ├── .claude/commands/  ← /analyze /ln /archive /publish 슬래시 명령어
    ├── 00_missions/       ← 주차별 과제 폴더 골격
    ├── 01_gallery/        ← 결과물 갤러리
    ├── 01_meetings/       ← 회의록 (비공개)
    ├── 02_skill_insight/  ← 스킬·인사이트
    ├── 90_analysis/       ← AI 분석 리포트 (자동 생성)
    ├── 91_proposals/      ← AI 제안 (자동 생성)
    ├── 92_status/         ← 출석·벌금 현황 (비공개)
    ├── 99_meta/           ← 멤버목록·파일명규칙·운영가이드
    └── 99_templates/      ← 회의록·미션 템플릿
```

## 빠른 시작 (3분)

```bash
# 1. 이 레포 받기
git clone https://github.com/selfishclub/aaa-starter-kit.git
cd aaa-starter-kit

# 2. template 폴더를 내 vault로 복사 (이름은 자유)
cp -r template ~/github/my-team-vault
cd ~/github/my-team-vault

# 3. Obsidian에서 "Open folder as vault"로 이 폴더 열기

# 4. Claude Code에서 99_meta/멤버목록.md 편집해 팀원 추가

# 5. 매주 운영자가 한 번씩 실행
#    /analyze 1     ← 1주차 분석
#    /publish       ← 홈페이지 배포
```

자세한 단계는 [QUICKSTART.md](./QUICKSTART.md)를 참고하세요.

## 시스템 한눈에 보기

### 전체 플로우

```
멤버 (Obsidian)
      ↓ 자동 git push
GitHub vault 레포 (원본 · 비공개 폴더 포함)
      ↓ /publish (공개 폴더만 sync)
GitHub site 레포 (Astro)
      ↓ 자동 빌드
Vercel / GitHub Pages (공개 홈페이지)
```

### 공개 vs 비공개 폴더

| 폴더 | 공개 | 용도 |
|------|:---:|------|
| `00_missions/` | ✅ | 주차별 과제 |
| `01_gallery/` | ✅ | 결과물 갤러리 |
| `02_skill_insight/` | ✅ | 스킬·인사이트 |
| `90_analysis/` | ✅ | AI 분석 |
| `91_proposals/` | ✅ | AI 제안 |
| `01_meetings/` | ❌ | 회의록 원본 |
| `92_status/` | ❌ | 출석·벌금 |
| `99_meta/` | ❌ | 운영 가이드 |

### 슬래시 명령어

| 명령어 | 기능 |
|--------|------|
| `/analyze N` | N주차 통합 분석 + 제안 갱신 |
| `/ln N` | N주차 링크드인 초안 생성 |
| `/archive` | 아카이브 북 갱신 |
| `/publish` | 홈페이지 배포 |

자세한 아키텍처는 [docs/architecture.md](./docs/architecture.md)를, 시각적 구조도는 [docs/system-overview.html](./docs/system-overview.html)을 브라우저로 열어보세요.

## 내 팀에 맞게 변형하기

- **회비/벌금 제도 없앨 경우** → `99_meta/운영가이드.md`에서 해당 섹션 삭제, `92_status/`도 제거
- **주간 운영이 아닐 경우** → `00_missions/Week_XX/` 폴더명을 `Sprint_01/`, `Month_01/` 등으로 변경
- **공개 사이트 필요 없을 경우** → `/publish` 명령어 비활성, vault만 사용
- **다른 AI 도구(ChatGPT, Gemini 등) 쓰는 경우** → 슬래시 명령어 내용을 해당 도구 프롬프트 형식으로 변환

구조 그 자체보다 **"비공개 원본 / 공개 가공본 분리"** 패턴이 핵심입니다.

## 기술 스택

- **콘텐츠**: [Obsidian](https://obsidian.md) — 마크다운 vault
- **AI**: [Claude Code](https://claude.com/claude-code) — 슬래시 명령어 + 에이전트
- **사이트**: [Astro](https://astro.build) — 정적 사이트 생성기
- **배포**: [Vercel](https://vercel.com) 또는 [GitHub Pages](https://pages.github.com)
- **버전 관리**: GitHub + [obsidian-git](https://github.com/Vinzent03/obsidian-git) 플러그인

## 크레딧

이 키트는 **Selfish Club의 AAA TEAM**이 2026년 3~4월 스터디를 운영하며 실제로 사용한 시스템을 추려낸 결과물입니다.

- **AAA TEAM · Selfish Club** — 콘텐츠·운영·검증
- **다다 (김다솔)** — 전체 시스템 구조 설계 및 실현
  - Obsidian vault ↔ Claude Code 슬래시 명령어 ↔ Astro 사이트로 이어지는 2-repo 아키텍처 전반을 설계하고, 실제 운영 가능한 형태로 구현
- 그 외 AAA TEAM 빌더 멤버들 — 주차별 과제 제출 및 피드백 루프 검증

이 키트를 사용하거나 변형해 공개하실 때, 위 크레딧을 어딘가에 남겨주시면 감사하겠습니다.

## 라이선스

MIT — 자유롭게 복제·변형·재배포 가능합니다. 출처만 밝혀주시면 감사하겠습니다.

## 도움 / 기여

- **이슈 제보**: [GitHub Issues](https://github.com/selfishclub/aaa-starter-kit/issues)
- **원본 팀**: AAA TEAM by [Selfish Club](https://github.com/selfishclub-all)
- **원본 홈페이지**: [aaa-homepage.vercel.app](https://aaa-homepage.vercel.app/) — 이 키트로 실제 운영되는 사이트

---

> **이기적으로 만들고, 이기적으로 공유한다.** — AAA TEAM
