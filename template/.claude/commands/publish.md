---
description: 스터디 아카이브 사이트 업데이트 + 배포 (Astro 빌드 + GitHub/Vercel push)
---

스터디 아카이브 사이트를 업데이트하고 배포해줘.

## 실행 절차

### -1. 초기 세팅 + 최신 코드 Pull

배포 전에 두 레포 모두 최신 상태로 동기화한다.
- vault 레포: 현재 디렉토리 (이 레포)
- astro 레포: 현재 디렉토리의 형제 폴더 `../selfish-aaa-site-astro`
  (폴더명은 `selfish-aaa-site-astro`로 고정. 실제 origin 레포는 `selfishclub-all/aaa-archive`)

**1) vault pull**
```bash
git pull
```

**2) astro 레포 준비 — 폴더 없으면 자동 clone** (공식 레포: `selfishclub/aaa-admin`, Vercel 배포)
```bash
if [ ! -d ../selfish-aaa-site-astro ]; then
  echo "astro 레포가 없어서 clone 합니다..."
  git clone -o selfishclub https://github.com/selfishclub/aaa-admin.git ../selfish-aaa-site-astro
fi
cd ../selfish-aaa-site-astro && git pull selfishclub main && cd -
```
- clone 실패(네트워크/권한) 시 에러 메시지 보여주고 중단.
- 리모트 이름은 `selfishclub`로 고정(기존 다다 로컬과 호환).

**3) selfishclub 리모트 보장** (이미 있으면 스킵)
```bash
cd ../selfish-aaa-site-astro
git remote | grep -q '^selfishclub$' || git remote add selfishclub https://github.com/selfishclub/aaa-admin.git
cd -
```

**4) 의존성 설치** — 처음 clone한 경우 `node_modules` 없음
```bash
if [ ! -d ../selfish-aaa-site-astro/node_modules ]; then
  cd ../selfish-aaa-site-astro && npm install && cd -
fi
```

- 충돌이 있으면 사용자에게 알리고 중단한다.

### 0. 콘텐츠 자동 정리 (frontmatter 없는 파일 처리)

배포 전에 vault의 공개 폴더를 스캔하여 자동 정리한다:

**스킬 & 인사이트 (`02_skill&insight/`)**:
- frontmatter가 없는 .md 파일을 찾는다
- 내용을 읽고 `keywords` 태그를 자동 추출한다
- `summary` 한 줄 요약을 자동 생성한다
- frontmatter를 파일 상단에 추가한다
- **키워드 가이드라인:**
  - 키워드는 3~5개로 제한한다
  - 범용적인 키워드(AI, Claude, 자동화)보다 **구체적인 키워드**를 우선한다
    - ❌ "AI", "자동화" → 너무 넓어서 거의 모든 글에 붙음
    - ✅ "DESIGN.md", "n8n 알림톡", "토큰 절감" → 해당 글만의 고유한 주제
  - 이미 다른 인사이트에서 많이 쓰인 키워드는 피하고, 차별화된 키워드를 선택한다
  - 기존 인사이트 파일들의 키워드를 먼저 확인하고, 중복을 최소화한다
  - 도구명, 프레임워크명, 구체적 기법명을 키워드로 우선 사용한다

### 0.5. 메인 랜딩 페이지 최신 주차 업데이트

`../selfish-aaa-site-astro/src/pages/index.astro`의 `latestWeek` 객체를 확인한다:
- vault의 `00_missions/` 에서 가장 최근 주차 폴더를 찾는다
- 해당 주차의 멤버별 미션 파일을 읽고 `latestWeek` 데이터를 업데이트한다:
  - `week`: 최신 주차 번호
  - `title`: 주차 테마 (분석 보고서 참고 또는 내용 기반 생성)
  - `members`: 각 멤버의 `name`, `cardTitle`, `summary`, `link`
- archive 페이지(`archive/index.astro`)의 `weeks` 배열에 새 주차가 없으면 추가한다

### 1. 콘텐츠 동기화
vault의 공개 폴더를 Astro 프로젝트로 복사:
```bash
bash ../selfish-aaa-site-astro/sync-content.sh
```

파일명에 특수문자(`?`, `%`, `()` 등)가 있으면 언더스코어로 변경한다.

### 2. Astro 빌드 테스트
```bash
cd ../selfish-aaa-site-astro && npm run build
```
에러가 있으면 수정 후 다시 빌드.

### 3. 커밋 & Push

배포는 **selfishclub 리모트만** push 한다. (Vercel이 공식 배포 채널)

```bash
cd ../selfish-aaa-site-astro
git add .
git commit -m "콘텐츠 업데이트"
git pull --ff-only selfishclub main   # 원격 변경사항 먼저 가져오기
git push selfishclub main
```

- `selfishclub` (selfishclub/aaa-admin) → **Vercel 배포 (공식)**
- `origin` (selfishclub-all/aaa-archive) → 사용 안 함 (구 GitHub Pages, 분기 상태)

### 4. 결과 확인
- Vercel (공식): https://aaa-admin-nu.vercel.app (또는 selfishclub의 Vercel 대시보드 확인)
