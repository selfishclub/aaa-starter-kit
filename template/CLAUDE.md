# {팀 이름}

> {팀 슬로건}

{팀 한 줄 소개}

---

## 레포지토리 구조

| 레포 | 역할 | 배포 |
|------|------|------|
| `{org}/{team-vault}` (이 레포) | Obsidian Vault — 콘텐츠 원본 | - |
| `{org}/{team-site}` | 공식 홈페이지 (Astro) | Vercel |

### 데이터 흐름

```
Obsidian Vault (이 레포)
       ↓ /publish (공개 폴더만)
{team-site} (Astro 빌드)
       ↓ git push
Vercel 자동 배포
```

---

## 폴더 구조

### 멤버가 쓰는 폴더

| 폴더 | 설명 | 홈페이지 반영 |
|------|------|-------------|
| `00_missions/` | 주차별 과제 | `/archive/` |
| `01_gallery/` | 결과물 갤러리 | `/gallery/` |
| `02_skill_insight/` | 스킬·인사이트 | `/tools/` |
| `01_meetings/` | 회의록 | 비공개 |

### AI 자동 생성 폴더

| 폴더 | 설명 | 홈페이지 반영 |
|------|------|-------------|
| `90_analysis/weekly/` | AI 분석 리포트 | `/analysis/` |
| `91_proposals/` | 스킬 제안 + 인사이트 | `/proposals/` |
| `92_status/` | 출석/벌금 대시보드 | 비공개 |

### 운영

| 폴더 | 설명 |
|------|------|
| `99_meta/` | 멤버목록·파일명규칙 |
| `99_templates/` | 템플릿 |
| `.claude/commands/` | 슬래시 명령어 |

---

## 슬래시 명령어

| 명령어 | 기능 | 누가 |
|--------|------|------|
| `/analyze N` | N주차 통합 분석 | 운영자 |
| `/ln N` | N주차 링크드인 초안 | 운영자 |
| `/archive` | 아카이브 북 갱신 | 운영자 |
| `/publish` | 홈페이지 배포 | 운영자 |

---

## 커밋 메시지 규칙

```
[프리픽스] 변경 내용
```

| 프리픽스 | 용도 |
|---------|------|
| `[mission]` | 주차별 과제 제출 |
| `[analysis]` | 분석 리포트 |
| `[meeting]` | 회의록 |
| `[meta]` | 운영 문서 |
| `[command]` | 슬래시 명령어 변경 |

---

## 멤버

`99_meta/멤버목록.md` 참고.

---

## 변형 가이드

이 `CLAUDE.md`는 [AAA Starter Kit](https://github.com/selfishclub/aaa-starter-kit) 템플릿입니다. `{ ... }` 플레이스홀더를 본인 팀 정보로 바꾸세요.
