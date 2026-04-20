# 02_skill_insight — 스킬 · 인사이트

팀이 발견한 도구, 스킬, 인사이트를 모읍니다. 홈페이지 `/tools/`에 **공개**됩니다.

## 파일 형식

frontmatter 없이도 작성 가능합니다. `/publish` 실행 시 Claude가 키워드와 요약을 자동 생성합니다.

직접 쓰는 경우:

```markdown
---
title: Claude Code 토큰 90% 절감 기법
author: 홍길동
category: 자동화
keywords:
  - 토큰 절감
  - CLAUDE.md
summary: 긴 컨텍스트를 슬림하게 유지하는 5가지 실전 패턴
link: https://youtube.com/watch?v=...
---

본문...
```

## 카테고리

- 자동화
- 콘텐츠
- 분석
- 생산성
- 디자인

(커스터마이즈 가능 — 사이트 레포의 `src/pages/tools/index.astro`에서 컬러 매핑 조정)
