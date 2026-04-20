# Architecture — 내부 작동 원리

이 문서는 AAA Starter Kit의 시스템이 **실제로 어떻게 굴러가는지**를 설명합니다. 운영자가 문제 생겼을 때 디버깅할 수 있는 수준까지 다룹니다.

---

## 1. 데이터 플로우 (전체)

```
┌────────────────────────────────────────────────────────────────┐
│                     멤버 (로컬 Obsidian)                        │
│                                                                │
│  00_missions/Week_0N_submit/Week_0N_닉네임_submit.md 작성       │
│                              ↓                                 │
└────────────────────────────────────────────────────────────────┘
                               │  obsidian-git 플러그인
                               │  (15분 주기 자동 커밋/push)
                               ▼
┌────────────────────────────────────────────────────────────────┐
│                  GitHub Vault 레포 (private)                    │
│                  selfishclub-all/aaa 같은                       │
│                                                                │
│  - 00_missions/ (공개)                                          │
│  - 01_gallery/  (공개)                                          │
│  - 02_skill_insight/ (공개)                                     │
│  - 01_meetings/ (비공개)                                        │
│  - 92_status/   (비공개)                                        │
│  - 99_meta/     (비공개)                                        │
└────────────────────────────────────────────────────────────────┘
                               │
                               │  운영자가 Claude Code에서
                               │  /analyze N 실행
                               ▼
┌────────────────────────────────────────────────────────────────┐
│                     AI 분석 + 제안 갱신                          │
│                                                                │
│  Claude가 읽음:                                                 │
│   - 해당 주차 멤버 과제                                          │
│   - 02_skill_insight/ 전체                                     │
│   - 01_meetings/Week_0N_weekly.md                              │
│   - 90_analysis/members/*_프로필.md                            │
│   - 91_proposals/*.md (누적 상태 파악용)                        │
│                                                                │
│  Claude가 씀:                                                   │
│   - 90_analysis/weekly/Week_0N_분석.md                         │
│   - 91_proposals/스킬_제안.md (증분 추가)                        │
│   - 91_proposals/AAA_봇_인사이트.md (증분 추가)                  │
│   - 90_analysis/members/*_프로필.md (증분/백필)                 │
│   - 92_status/진행현황.md (현황표 갱신)                          │
└────────────────────────────────────────────────────────────────┘
                               │
                               │  운영자가 /publish 실행
                               ▼
┌────────────────────────────────────────────────────────────────┐
│                    sync-content.sh 실행                         │
│                                                                │
│  공개 폴더 화이트리스트만 Astro 레포로 rsync                     │
│   vault/00_missions/      → site/src/content/missions/         │
│   vault/01_gallery/       → site/src/content/gallery/          │
│   vault/02_skill_insight/ → site/src/content/skills/           │
│   vault/90_analysis/      → site/src/content/analysis/         │
│   vault/91_proposals/     → site/src/content/proposals/        │
│                                                                │
│  비공개 폴더는 절대 복사되지 않음 (구조적 방지)                   │
└────────────────────────────────────────────────────────────────┘
                               │
                               │  npm run build
                               ▼
┌────────────────────────────────────────────────────────────────┐
│                 GitHub Site 레포 (public)                       │
│                 selfishclub/aaa-admin 같은                      │
│                                                                │
│  Astro 정적 사이트 + 빌드 산출물                                 │
└────────────────────────────────────────────────────────────────┘
                               │
                               │  git push origin main
                               ▼
┌────────────────────────────────────────────────────────────────┐
│           Vercel / GitHub Pages 자동 배포                       │
│                                                                │
│  → https://{team}-homepage.vercel.app                          │
└────────────────────────────────────────────────────────────────┘
```

---

## 2. 슬래시 명령어 내부 구조

### 2.1 명령어 정의 위치

```
{vault}/.claude/commands/
├── analyze.md       ← /analyze N
├── linkedin.md      ← /ln N
├── archive.md       ← /archive
└── publish.md       ← /publish
```

각 파일 상단:
```yaml
---
description: 한 줄 설명
argument-hint: <인자 이름>
---
```

본문은 Claude가 실행할 **자연어 프롬프트**입니다. `$ARGUMENTS`는 사용자가 입력한 인자로 치환됩니다.

### 2.2 `/analyze N` 내부 단계

1. **데이터 수집** — 해당 주차 관련 파일 모두 읽기 (Glob + Read)
2. **분석 리포트 작성** — 에디토리얼 톤, 닉네임 금칙, 핵심 한 줄
3. **현황 업데이트** — `92_status/진행현황.md` 테이블 갱신
4. **스킬 제안 도출** — 반복 패턴·공통 관심사 기반, 누적 추가
5. **AAA 봇 인사이트** — 프로젝트 아이디어·멤버 Next Step·공백 영역
6. **멤버 프로필 갱신** — 증분 or 백필 모드 자동 판정
7. **frontmatter 갱신** — highlights, count, preview

자세한 규약은 `.claude/commands/analyze.md` 본문 참고.

### 2.3 `/publish` 내부 단계

1. **vault pull** — 최신 동기화
2. **astro 레포 준비** — 없으면 clone, 있으면 pull
3. **의존성 확인** — `node_modules` 없으면 `npm install`
4. **콘텐츠 자동 정리** — frontmatter 없는 파일에 keywords/summary 자동 추가
5. **랜딩 페이지 최신 주차 업데이트** — `src/pages/index.astro`의 `latestWeek` 갱신
6. **sync-content.sh 실행** — 공개 폴더 rsync
7. **`npm run build`** — 실패 시 중단
8. **commit + push** — origin + 배포용 리모트 양쪽

---

## 3. 2-Repo 경계 상세

### 3.1 왜 monorepo가 아닌가

| 단일 레포 방식 | 2-레포 방식 (권장) |
|--------------|------------------|
| 실수로 public 만들면 전부 노출 | vault는 private, site만 public |
| 사이트 CI가 vault에도 붙어 느려짐 | 사이트 CI는 site 레포에만 |
| 기여자 권한 관리 복잡 | vault = 멤버만, site = 운영자만 |
| Astro와 Obsidian이 충돌(.obsidian/) | 완전 분리 |

### 3.2 sync-content.sh의 보호장치

```bash
#!/usr/bin/env bash
# sync-content.sh (요약)

VAULT="../my-team-vault"
SITE_CONTENT="./src/content"

# 화이트리스트 — 이 목록 외에는 절대 복사 안 됨
declare -A MAP=(
  ["$VAULT/00_missions"]="$SITE_CONTENT/missions"
  ["$VAULT/01_gallery"]="$SITE_CONTENT/gallery"
  ["$VAULT/02_skill_insight"]="$SITE_CONTENT/skills"
  ["$VAULT/90_analysis/weekly"]="$SITE_CONTENT/analysis"
  ["$VAULT/91_proposals"]="$SITE_CONTENT/proposals"
  ["$VAULT/90_analysis/members"]="$SITE_CONTENT/members"
)

for src in "${!MAP[@]}"; do
  rsync -a --delete "$src/" "${MAP[$src]}/"
done
```

**핵심:** 쓰기는 화이트리스트 기반. 비공개 폴더(`01_meetings`, `92_status`, `99_meta`)는 MAP에 없으므로 절대 복사되지 않습니다.

---

## 4. Astro Content Collection 구조

```
src/content/
├── config.ts         ← 컬렉션 스키마 정의 (zod)
├── missions/         ← 주차별 과제
├── gallery/          ← 결과물
├── skills/           ← 스킬·인사이트
├── analysis/         ← AI 분석 리포트
├── proposals/        ← AI 제안
└── members/          ← 멤버 프로필
```

각 컬렉션은 frontmatter 스키마를 강제하므로 콘텐츠 형식 불일치를 **빌드 타임에** 잡아냅니다.

---

## 5. 멤버 프로필 증분/백필 알고리즘

`/analyze N` 실행 시 각 멤버 프로필을 다음 규칙으로 갱신합니다.

```python
# 의사코드
for member in active_members:
    profile = read(f"90_analysis/members/{member}_프로필.md")
    last_week = last_week_in_profile(profile)

    if last_week == N - 1:
        # 증분 모드: 이번 주차만 추가
        append_week_row(profile, N)
        append_week_section(profile, N)
    elif last_week < N - 1:
        # 백필 모드: 빠진 주차부터 N까지 모두 채움
        for w in range(last_week + 1, N + 1):
            if submitted(member, w):
                append_week_row(profile, w)
                append_week_section(profile, w)
            else:
                append_week_row(profile, w, status="X")
                # 섹션은 생성 안 함

    update_keywords(profile, new_keywords=extract_new(member, N))
    update_frontmatter(profile, last_updated=today)
```

---

## 6. 흔한 장애 시나리오와 복구

| 증상 | 원인 | 복구 |
|------|------|------|
| `/publish` 실행 시 "sync-content.sh not found" | site 레포 경로가 형제 디렉토리가 아님 | vault와 site를 같은 부모 폴더로 옮김 |
| 홈페이지에 오래된 주차가 계속 표시됨 | `src/pages/index.astro`의 `latestWeek` 하드코딩 누락 | `/publish` 스크립트의 Step 0.5(latestWeek 갱신)가 제대로 작동하는지 확인 |
| 사이트에 비공개 콘텐츠 유출 | 누군가 `sync-content.sh`를 직접 수정했을 가능성 | git log로 변경 이력 확인 후 원복 |
| Claude가 회의록을 못 읽음 | `01_meetings/Week_0N_weekly.md` 파일명 규칙 위반 | 파일명을 정확히 맞춤 |
| 멤버 프로필이 중복 생성됨 | `99_meta/멤버목록.md`의 닉네임과 실제 파일명 불일치 | 둘 다 같은 닉네임으로 통일 |

---

## 7. 보안 고려사항

### 7.1 민감 정보 경로

이런 정보가 vault에 들어있다면 **반드시 비공개 폴더에**:
- 멤버 개인 이메일·전화번호 → `99_meta/`
- 회비·계좌 정보 → `92_status/`
- 회의 녹음 링크(Lilys/Tiro) → `01_meetings/`

### 7.2 GitHub 토큰

- `/publish`가 사용하는 토큰은 **site 레포에만 write 권한** 필요
- vault 레포에는 멤버 전원 write 권한 부여
- admin 페이지의 GUI 편집기 토큰은 별도 fine-grained PAT 발급 권장

---

## 8. 확장 포인트

시스템을 확장하고 싶다면 여기를 건드리세요.

### 8.1 새 슬래시 명령어 추가
```
{vault}/.claude/commands/{새명령어}.md
```
frontmatter + 자연어 프롬프트로 끝.

### 8.2 새 공개 폴더 추가
1. vault에 `XX_새폴더/` 생성
2. site 레포에 `src/content/새이름/` 생성 + `content/config.ts`에 스키마 추가
3. `sync-content.sh`의 MAP에 매핑 추가
4. `src/pages/새이름/index.astro`와 `[id].astro` 생성
5. `Layout.astro`의 내비게이션에 링크 추가

### 8.3 AI 모델 교체
슬래시 명령어 본문은 자연어 프롬프트이므로, Claude 외 다른 모델(GPT-4, Gemini)로도 재사용 가능. 각 모델의 컨텍스트 윈도 / 도구 호출 방식에 맞게 약간 조정만 필요.

---

## 9. 참고 구현

- **원본 팀 vault**: https://github.com/selfishclub-all/aaa (private)
- **원본 팀 사이트**: https://github.com/selfishclub/aaa-admin
- **원본 홈페이지**: https://aaa-homepage.vercel.app

원본 구현과 이 starter-kit은 의도적으로 비슷하지만, starter-kit은 어떤 팀이든 쓸 수 있도록 **추상화·일반화된 버전**입니다.
