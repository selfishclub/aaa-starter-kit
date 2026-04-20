# 01_gallery — 결과물 갤러리

배포한 프로젝트·웹사이트·도구를 카드 형태로 모읍니다. 홈페이지 `/gallery/`에 **공개**됩니다.

## 파일 형식

```markdown
---
name: 프로젝트 이름
maker: 홍길동
thumbnail: 🚀
link: https://my-project.vercel.app
week: 3
tags:
  - 웹사이트
  - Claude Code
---

프로젝트 상세 설명 (마크다운 본문).
```

## 필수 frontmatter 필드

| 필드 | 타입 | 설명 |
|------|------|------|
| `name` | string | 서비스명 |
| `maker` | string | 만든 사람 (닉네임) |
| `thumbnail` | string | 이모지 또는 이미지 URL |
| `link` | string | 배포 URL |
| `week` | number | 관련 주차 |
| `tags` | list | 태그 복수 |

## 파일명

자유 (예: `my-landing.md`, `ai-chatbot.md`). 특수문자만 금지.
