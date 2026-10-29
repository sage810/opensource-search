# googleworkspace-cli (gws)

- **저장소:** https://github.com/googleworkspace/cli
- **npm:** https://www.npmjs.com/package/@googleworkspace/cli
- **스킬 목록:** https://github.com/googleworkspace/cli/blob/main/docs/skills.md
- **라이선스:** Apache-2.0
- **언어:** Rust (npm으로도 설치 가능, Node.js 18 이상)
- **한 줄 설명:** Drive·Gmail·Calendar·Sheets·Docs·Chat 등 Google Workspace 전체를 명령어 하나(`gws`)로 다루는 CLI. AI 에이전트용 스킬 100여 개 포함

[← 목록으로](../README.md)

> Google 조직 저장소에 있지만 **공식 지원 제품은 아닙니다.** v1.0 전이라 바뀌는 부분이 많을 수 있습니다.

## 무엇인가

Google API를 쓰려면 보통 REST 문서를 보며 `curl`이나 코드를 직접 짜야 합니다. `gws`는 Google의 **Discovery Service**를 실행할 때 읽어서 명령어를 자동으로 만들기 때문에, Google이 API를 새로 내면 따로 업데이트하지 않아도 바로 쓸 수 있습니다.

- **사람에게:** 모든 명령에 `--help`, 요청을 미리 보는 `--dry-run`, 페이지 자동 넘김
- **AI 에이전트에게:** 모든 결과가 JSON으로 나와서 에이전트가 읽기 쉬움. 스킬(`SKILL.md`)을 함께 설치하면 에이전트가 Workspace를 직접 다룸
- **명령 형식:** `gws <서비스> <리소스> <메서드> [플래그]`
- **도우미 명령:** 자주 쓰는 작업은 `+`로 시작하는 짧은 명령으로 제공 (`gws gmail +send` 등)

## 사용법

### 설치

```bash
npm install -g @googleworkspace/cli
```

npm 외에 [GitHub Releases](https://github.com/googleworkspace/cli/releases)에서 OS별 실행 파일을 받아 `PATH`에 넣어도 됩니다. (macOS·Linux는 `brew install googleworkspace-cli`도 가능)

### 로그인 (처음 한 번)

Google Cloud 프로젝트와 OAuth 설정이 필요합니다.

```bash
gws auth setup     # gcloud CLI가 있으면 프로젝트 생성·API 켜기·로그인을 한 번에
gws auth login     # 이후 로그인·권한(scope) 선택
```

- `gcloud`가 없으면 Google Cloud Console에서 직접 설정합니다.
  1. OAuth 동의 화면에서 앱 유형 **External**(테스트 모드) 선택
  2. **Test users**에 내 Google 계정 추가 (안 하면 "Access blocked" 오류)
  3. OAuth 클라이언트를 **Desktop app** 유형으로 만들기
  4. 받은 JSON을 `~/.config/gws/client_secret.json`에 저장 후 `gws auth login`
- **주의:** 검증 안 된 앱(테스트 모드)은 권한을 약 25개까지만 받을 수 있습니다. 필요한 서비스만 고릅니다.

  ```bash
  gws auth login -s drive,gmail,sheets
  ```

- 서버·CI에서는 `GOOGLE_WORKSPACE_CLI_CREDENTIALS_FILE`(서비스 계정 키 또는 `gws auth export`로 뽑은 파일)이나 `GOOGLE_WORKSPACE_CLI_TOKEN`(액세스 토큰)을 씁니다.

### 기본 명령

```bash
# 최근 파일 10개
gws drive files list --params '{"pageSize": 10}'

# 스프레드시트 만들기
gws sheets spreadsheets create --json '{"properties": {"title": "Q1 Budget"}}'

# 어떤 메서드의 요청·응답 구조 보기
gws schema drive.files.list

# 모든 페이지를 한 줄씩(NDJSON) 받기
gws drive files list --params '{"pageSize": 100}' --page-all
```

- `--params`: URL 쿼리 값, `--json`: 요청 본문, `--dry-run`: 실제로 보내지 않고 미리 보기
- Sheets 범위의 `!`는 bash가 다르게 해석하므로 **작은따옴표**로 감쌉니다. (`'Sheet1!A1:C10'`)

### 도우미 명령 (`+`)

| 명령 | 하는 일 |
|------|--------|
| `gws gmail +send` | 메일 보내기 |
| `gws gmail +reply` / `+forward` | 답장 / 전달 |
| `gws gmail +triage` | 안 읽은 메일 요약 |
| `gws calendar +agenda` | 다가오는 일정 보기 |
| `gws calendar +insert` | 일정 만들기 |
| `gws sheets +append` / `+read` | 시트에 행 추가 / 값 읽기 |
| `gws docs +write` | 문서에 글 덧붙이기 |
| `gws drive +upload` | 파일 업로드 |
| `gws script +push` | 로컬 파일로 Apps Script 프로젝트 덮어쓰기 |
| `gws workflow +standup-report` | 오늘 회의 + 할 일 요약 |
| `gws workflow +weekly-digest` | 이번 주 회의 + 안 읽은 메일 수 |

```bash
gws gmail +send --to alice@example.com --subject "Hello" --body "Hi there"
gws sheets +append --spreadsheet SPREADSHEET_ID --values "Alice,95"
gws drive +upload ./report.pdf --name "Q1 Report"
```

전체 목록은 `gws <서비스> --help`로 확인합니다.

## 스킬 호출법

저장소의 `skills/` 폴더에 에이전트 스킬이 들어 있습니다. Claude Code 플러그인이 아니라 개별 스킬이므로 `npx skills add`로 설치합니다. 스킬은 `gws` 명령을 실행하므로 **위의 설치·로그인을 먼저** 해 둬야 합니다.

```bash
# 전부 설치
npx skills add https://github.com/googleworkspace/cli

# 필요한 것만 설치
npx skills add https://github.com/googleworkspace/cli/tree/main/skills/gws-gmail
npx skills add https://github.com/googleworkspace/cli/tree/main/skills/gws-drive
```

설치하면 스킬 이름 그대로 슬래시 명령이 됩니다. 인자는 따로 정해져 있지 않고, 명령 뒤에 하고 싶은 일을 말로 적습니다.

```
/gws-gmail 안 읽은 메일 중 오늘 온 것만 요약해 줘
/gws-sheets-append 스프레드시트 <ID>에 "홍길동,95" 행 추가
/gws-calendar-agenda 이번 주 일정 보여 줘
/persona-team-lead 오늘 스탠드업 준비해 줘
/recipe-save-email-attachments 첨부파일 있는 메일을 Drive 폴더에 저장
```

| 종류 | 이름 형식 | 예시 |
|------|----------|------|
| **서비스** (API별) | `gws-<서비스>` | `gws-drive`, `gws-gmail`, `gws-sheets`, `gws-calendar`, `gws-docs`, `gws-classroom`, `gws-forms`, `gws-script` |
| **도우미** (`+` 명령별) | `gws-<서비스>-<명령>` | `gws-gmail-send`, `gws-drive-upload`, `gws-workflow-standup-report` |
| **페르소나** (역할 묶음) | `persona-<역할>` | `persona-exec-assistant`, `persona-project-manager`, `persona-researcher` |
| **레시피** (여러 단계 작업) | `recipe-<작업>` | `recipe-create-doc-from-template`, `recipe-find-free-time`, `recipe-backup-sheet-as-csv` |
| **공통** | `gws-shared` | 인증·공통 플래그·보안 규칙 (다른 스킬이 먼저 읽음) |

- **자동 호출:** 각 스킬의 description이 "Gmail: Send, read, and manage email."처럼 서비스와 작업을 적고 있어서, "메일 보내 줘", "시트에 추가해 줘"처럼 해당 작업을 부탁하면 에이전트가 알맞은 스킬을 스스로 불러 씁니다.
- 스킬 전체 목록과 설명은 [Skills Index](https://github.com/googleworkspace/cli/blob/main/docs/skills.md)에 있습니다.
- Gemini CLI에서는 `gemini extensions install https://github.com/googleworkspace/cli`로 확장 프로그램으로 설치할 수 있습니다.

## 활용법

- **학교 업무 자동화:** `gws-classroom`으로 수업·학생 명단을, `gws-forms` + `recipe-collect-form-responses`로 설문 응답을, `gws-sheets`로 결과 정리를 에이전트에게 맡깁니다.
- **Apps Script 배포:** `gws script +push`로 로컬에서 작성한 Apps Script 파일을 프로젝트에 바로 올립니다. (clasp 대신 쓸 수 있음)
- **스크립트에서 쓰기:** 결과가 JSON이고 종료 코드가 정해져 있어(`0` 성공, `1` API 오류, `2` 인증 오류, `3` 잘못된 인자) 셸 스크립트에서 분기하기 쉽습니다. `jq`와 함께 쓰면 좋습니다.
- **안전하게 쓰기:** 메일 발송·파일 삭제처럼 되돌리기 어려운 명령은 먼저 `--dry-run`으로 확인합니다.
- **자주 막히는 곳**
  - "Access blocked"/403 → OAuth 동의 화면의 **Test users**에 내 계정 추가
  - `accessNotConfigured` → 오류에 나온 `enable_url`에서 해당 API를 **Enable** 후 다시 실행
  - `redirect_uri_mismatch` → OAuth 클라이언트를 **Desktop app** 유형으로 다시 만들기
- **Windows 주의:** `--params '{...}'`처럼 작은따옴표로 JSON을 넘기는 예시는 bash 기준입니다. PowerShell에서는 따옴표 처리가 달라 깨질 수 있으니 Git Bash에서 실행하는 편이 편합니다.
