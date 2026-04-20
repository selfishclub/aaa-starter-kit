# QUICKSTART — 30분 안에 내 팀 시스템 세팅하기

이 가이드는 빈 vault 상태에서 **실제로 운영 가능한 팀 시스템까지** 가는 최단 경로입니다.

> **전제 조건**
> - GitHub 계정 + `gh` CLI
> - Obsidian 설치
> - [Claude Code](https://claude.com/claude-code) 설치 및 로그인
> - Node.js 18+ (사이트 빌드용, 선택)

---

## STEP 1 — 키트 받기 (2분)

```bash
# 이 starter-kit 클론
git clone https://github.com/selfishclub/aaa-starter-kit.git
cd aaa-starter-kit

# template 폴더를 내 팀 이름으로 복사
cp -r template ~/github/my-team-vault
cd ~/github/my-team-vault
```

`my-team-vault` 이름은 자유롭게 바꾸세요.

---

## STEP 2 — GitHub 레포 2개 만들기 (5분)

2-repo 아키텍처 원칙(PRD §4.2)을 따릅니다.

### 2-1. Vault 레포 (원본, private 권장)

```bash
# 현재 vault 디렉토리에서
git init
gh repo create my-team-vault --private --source=. --remote=origin
git add .
git commit -m "init: AAA starter kit 기반 vault"
git push -u origin main
```

### 2-2. Site 레포 (공개 사이트 — 나중에)

지금은 비워두고, 첫 `/publish` 실행 시에 생성합니다.

---

## STEP 3 — Obsidian으로 vault 열기 (2분)

1. Obsidian 실행 → **Open folder as vault** 선택
2. 방금 복사한 `~/github/my-team-vault` 폴더 선택
3. **Settings → Files & Links → Detect all file extensions**를 켜면 `.html` 등도 보입니다
4. **obsidian-git 플러그인 설치** (Community plugins → Browse → obsidian-git)
   - 자동 커밋 간격: 15분 권장
   - 자동 push: 켜기

---

## STEP 4 — 기본 정보 채우기 (10분)

다음 3개 파일만 본인 팀 정보로 바꾸면 시작 준비 끝입니다.

### 4-1. `99_meta/멤버목록.md`

```markdown
| 닉네임 | GitHub ID | 역할 |
|-------|-----------|------|
| 누구1 | nickname1 | 운영자 |
| 누구2 | nickname2 | 빌더 |
| ...   | ...       | ...  |
```

### 4-2. `CLAUDE.md` (vault 루트에 생성)

```markdown
# {팀 이름}

> 팀 슬로건

팀 개요 한 줄.

## 멤버

위 멤버목록 링크.

## 커밋 메시지 규칙

`[프리픽스] 내용` 형식 사용:
- `[mission]` 과제 제출
- `[meta]` 문서 갱신
- `[analysis]` AI 분석 결과
```

### 4-3. `00_missions/Week_01_submit/`

- 각 멤버별로 `Week_01_{닉네임}_submit.md` 파일 생성
- 템플릿은 `99_templates/미션_Week_0N.md` 복사해서 사용

---

## STEP 5 — 첫 과제 제출 & 분석 돌려보기 (10분)

### 5-1. 멤버는 과제 작성

```
00_missions/Week_01_submit/Week_01_{내닉네임}_submit.md
```

파일 저장 → obsidian-git이 자동 push → GitHub에 반영

### 5-2. 운영자가 1주차 분석

Claude Code에서:

```
/analyze 1
```

다음 파일들이 자동 생성됩니다.
- `90_analysis/weekly/Week_01_분석.md` — 주간 리포트
- `91_proposals/스킬_제안.md` — 스킬 제안 (누적)
- `91_proposals/AAA_봇_인사이트.md` — 인사이트 (누적)
- `90_analysis/members/*_프로필.md` — 멤버 프로필 (누적)
- `92_status/진행현황.md` — 출석/제출 현황

---

## STEP 6 — (선택) 공개 사이트 배포

사이트 없이 vault만 쓰고 싶다면 여기서 멈춰도 됩니다.

### 6-1. Astro 레포 fork

원본 팀 사이트 레포를 fork하거나 새로 만드세요.

```bash
gh repo fork selfishclub-all/aaa-archive --clone=false
# 또는
gh repo create my-team-site --public
```

### 6-2. `sync-content.sh` 경로 조정

사이트 레포의 `sync-content.sh`에서 vault 경로를 본인 것으로 수정:

```bash
VAULT="../my-team-vault"   # 본인 vault 폴더명
```

### 6-3. `/publish` 실행

```
/publish
```

결과:
- vault의 공개 폴더만 사이트 레포로 sync
- Astro build
- git push → Vercel/Pages 자동 배포

---

## 트러블슈팅

### "Claude Code에서 슬래시 명령어가 안 보입니다"
- vault 루트에 `.claude/commands/*.md`가 있는지 확인
- Claude Code를 vault 루트 디렉토리에서 실행했는지 확인

### "obsidian-git이 push하지 못합니다"
- SSH key 또는 Personal Access Token 설정 필요
- Settings → Obsidian Git → Authentication 확인

### "/publish할 때 sync-content.sh를 찾지 못합니다"
- vault와 site 레포가 **형제 디렉토리**인지 확인
  ```
  ~/github/
    ├── my-team-vault/    ← vault
    └── my-team-site/     ← Astro
  ```

### "Astro 빌드가 깨집니다"
- 파일명 특수문자(`?`, `%`, `(`, `)`) 확인
- frontmatter YAML 문법 오류 확인
- `npm install` 먼저 실행했는지 확인

---

## 다음 읽을 거리

- [PRD.md](./PRD.md) — 전체 요구사항 명세
- [docs/architecture.md](./docs/architecture.md) — 내부 작동 원리
- [docs/system-overview.html](./docs/system-overview.html) — 비개발자용 시각 구조도

---

**첫 주만 한번 돌려보면 나머지는 자연스럽게 굴러갑니다. 막히면 이슈 남겨주세요.**
