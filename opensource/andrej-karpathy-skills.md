# andrej-karpathy-skills

- **저장소:** https://github.com/multica-ai/andrej-karpathy-skills
- **라이선스:** MIT
- **한 줄 설명:** Andrej Karpathy가 지적한 LLM 코딩의 문제점을 바탕으로, Claude Code의 동작을 개선하는 `CLAUDE.md` 파일 하나짜리 가이드라인

[← 목록으로](../README.md)

## 무엇인가

Karpathy는 LLM 코딩 모델이 이런 실수를 한다고 지적했습니다.

- 잘못된 가정을 확인 없이 밀고 나가고, 헷갈려도 묻지 않는다.
- 코드와 API를 과하게 복잡하게 만들고, 죽은 코드를 정리하지 않는다.
- 작업과 상관없는 주석·코드를 이해하지 못한 채 바꾸거나 지운다.

이 저장소는 이를 막는 4가지 원칙을 `CLAUDE.md` 한 파일에 담았습니다.

| 원칙 | 해결하는 문제 |
|------|--------------|
| **Think Before Coding** (코딩 전에 생각하기) | 잘못된 가정, 숨겨진 혼란, 트레이드오프 누락 |
| **Simplicity First** (단순함 우선) | 과도한 복잡화, 부풀린 추상화 |
| **Surgical Changes** (수술적 변경) | 요청과 무관한 수정 |
| **Goal-Driven Execution** (목표 중심 실행) | 테스트·검증 가능한 성공 기준으로 반복 |

## 사용법

### 방법 A: Claude Code 플러그인 (모든 프로젝트에 적용)

Claude Code 안에서 실행합니다.

```
/plugin marketplace add forrestchang/andrej-karpathy-skills
/plugin install andrej-karpathy-skills@karpathy-skills
```

### 방법 B: 프로젝트별 CLAUDE.md

새 프로젝트:

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/multica-ai/andrej-karpathy-skills/main/CLAUDE.md
```

기존 `CLAUDE.md`가 있는 프로젝트(뒤에 덧붙이기):

```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/multica-ai/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

### Cursor에서 쓰기

저장소에 Cursor 규칙 파일(`.cursor/rules/karpathy-guidelines.mdc`)이 들어 있습니다. 자세한 설정은 원본 저장소의 `CURSOR.md`를 참고합니다.

## 스킬 호출법

방법 A(플러그인)로 설치하면 `karpathy-guidelines` 스킬이 생깁니다.

```
/andrej-karpathy-skills:karpathy-guidelines
```

- 인자 없이 호출하면 4가지 원칙을 지금 대화에 불러옵니다.
- **자동 호출:** 코드를 작성·리뷰·리팩터링할 때 Claude가 스스로 불러 씁니다. 그래서 보통은 직접 호출하지 않아도 됩니다.
- 방법 B(`CLAUDE.md`)로 넣었다면 스킬이 아니라 항상 적용되는 규칙이므로 호출할 필요가 없습니다.

## 활용법

- **프로젝트 규칙과 합치기:** 가이드라인 아래에 프로젝트 전용 규칙을 덧붙여 씁니다.

  ```markdown
  ## Project-Specific Guidelines

  - Use TypeScript strict mode
  - All API endpoints must have tests
  ```

- **잘 작동하는지 확인하는 기준**
  - diff에 요청한 변경만 들어간다.
  - 과하게 복잡해서 다시 짜는 일이 줄어든다.
  - 실수한 뒤가 아니라 구현하기 전에 질문이 나온다.
- **주의:** 속도보다 신중함에 무게를 둔 규칙입니다. 오타 수정 같은 간단한 작업에는 상황에 맞게 판단합니다.
- **이 저장소에서의 적용 예:** 루트의 [CLAUDE.md](../CLAUDE.md)가 이 가이드라인을 그대로 담고 있습니다.
