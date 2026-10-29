# book-to-skill

- **저장소:** https://github.com/virgiliojr94/book-to-skill
- **라이선스:** MIT (변환기 코드에만 해당, 변환한 책·문서에는 적용되지 않음)
- **언어:** Python
- **한 줄 설명:** 기술 서적 PDF나 문서 폴더를 AI 에이전트(Claude Code 등)가 필요할 때 불러 쓰는 스킬로 바꿔 주는 변환기

[← 목록으로](../README.md)

> ⚠️ **공식 저장소만 사용하세요.** `Leutenegger/book-to-skill` 이라는 가짜 복제 저장소에는 암호화폐 지갑 정보를 빼가는 악성 코드가 들어 있다고 공식 저장소가 공지했습니다(2026-08-17). 반드시 `virgiliojr94/book-to-skill` 에서만 설치합니다.

## 무엇인가

책을 한 번 읽고 나면 몇 달 뒤엔 내용이 기억나지 않고, AI에게 물으면 책 내용을 모르거나 지어냅니다. book-to-skill은 책을 **구조화된 스킬**로 바꿔서, 에이전트가 질문에 맞는 챕터만 골라 읽고 실제 내용으로 답하게 합니다.

1. **지정:** 파일·폴더·glob 패턴을 넘깁니다. 예: `/book-to-skill ./my-book.pdf`
2. **정리:** 요약이 아니라 프레임워크·판단 규칙·안티패턴·챕터별 파일로 구조화합니다.
3. **사용:** 에이전트가 필요할 때 해당 챕터만 불러 답합니다. 책 전체를 넣는 것보다 토큰이 24~51배 적게 든다고 합니다.

만들어지는 파일:

| 파일 | 내용 |
|------|------|
| `SKILL.md` | 핵심 개념 + 챕터 목차 |
| `chapters/ch01-*.md` … | 챕터별 파일 (질문할 때만 불러옴) |
| `glossary.md` | 핵심 용어 사전 (챕터 참조 포함) |
| `patterns.md` | 기법·알고리즘·디자인 패턴 모음 |
| `cheatsheet.md` | 판단 표·빠른 참조 규칙 |

지원 형식: PDF, EPUB, DOCX, TXT, Markdown, reStructuredText, AsciiDoc, HTML, RTF, MOBI/AZW/AZW3
지원 도구: Claude Code, GitHub Copilot CLI, Codex, Amp, OpenCode, OpenClaw, Hermes Agent

## 사용법

### 설치

```bash
# 방법 1: 스킬 CLI로 한 번에
npx skills add virgiliojr94/book-to-skill

# 방법 2: Claude Code 스킬 폴더에 직접 clone
git clone https://github.com/virgiliojr94/book-to-skill.git ~/.claude/skills/book-to-skill
```

PDF를 읽으려면 추출 도구가 필요합니다. 설치 상태는 `python3 scripts/extract.py --check` 로 확인합니다.

| 책 종류 | 도구 | 설치 |
|---------|------|------|
| 글 위주 | `pypdf` | `pip3 install pypdf` |
| 코드·표·수식이 많은 기술서 | `docling` | `pip3 install docling` |
| EPUB | `ebooklib` + `beautifulsoup4` | `pip3 install ebooklib beautifulsoup4` |

스캔한 PDF(이미지만 있는 PDF)는 먼저 OCR을 해야 합니다: `ocrmypdf input.pdf output.pdf`

### 변환하기

```bash
# 파일 하나
/book-to-skill ~/books/my-book.pdf

# 여러 파일을 하나의 스킬로
/book-to-skill ~/papers/paper1.pdf ~/notes/export.txt unified-research

# 폴더 전체
/book-to-skill ~/workspace/project-docs/ project-knowledge

# 기존 스킬에 새 자료 덧붙이기
/book-to-skill ~/articles/new-paper.pdf ~/.claude/skills/project-knowledge
```

### 만든 스킬 쓰기

```bash
/designing-data-intensive-apps                  # 핵심 개념 불러오기
/designing-data-intensive-apps replication      # 주제 찾아서 설명
/designing-data-intensive-apps ch05             # 5장 자세히
```

Claude Code는 다음 세션부터 새 스킬을 인식합니다.

## 스킬 호출법

```
/book-to-skill <파일|폴더|glob>... [스킬이름]
```

| 인자 | 설명 |
|------|------|
| `<파일\|폴더\|glob>` | 변환할 문서. 여러 개를 주면 하나의 스킬로 합침. 기존 스킬 폴더를 주면 새 자료를 덧붙임 |
| `[스킬이름]` | 만들 스킬의 이름(slug). 생략하면 책 제목으로 정함 |

- **자동 호출:** "이 책 공부하고 싶어", "이 PDF를 스킬로 만들어 줘"처럼 문서를 스킬로 만들거나 공부하려는 요청을 하면 Claude가 스스로 불러 씁니다.
- **만든 스킬 호출:** 변환이 끝나면 `/<스킬이름>`이라는 새 스킬이 생깁니다. 위 "만든 스킬 쓰기"를 참고합니다.

## 활용법

- **기술 서적 학습:** 산 책을 스킬로 만들어 두고, 코딩하다가 `/책이름 주제` 로 바로 찾아봅니다.
- **사내·프로젝트 문서:** 아키텍처 결정 기록, 운영 매뉴얼, 온보딩 가이드가 든 `docs/` 폴더를 통째로 스킬로 만듭니다.
- **디자인·브랜드 가이드:** 수십 쪽짜리 브랜드북을 스킬로 만들어 팀이 질문으로 찾게 합니다.
- **논문 묶음:** 논문 여러 편과 내 메모를 하나의 스킬로 합치고, 새 논문이 나오면 덧붙입니다.
- **명세·표준 문서:** RFC, API 명세, 규정 문서처럼 자주 찾지만 외우지 못하는 문서에 씁니다.

### 주의할 점 (저작권)

- 변환은 내 PC에서 이루어집니다. 다만 에이전트 모델이 클라우드에서 돌면, 넘긴 텍스트는 그 서비스의 데이터 약관을 따릅니다.
- 직접 산 책이나 권리가 있는 문서만 변환합니다.
- **저작권이 있는 책으로 만든 스킬은 공개 배포하지 않습니다.** GitHub에 올릴 때는 비공개(private)로 둡니다. 변환기도 기본값을 비공개로 둡니다.
