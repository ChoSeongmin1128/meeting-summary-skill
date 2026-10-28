# meeting-summary-skill

Claude Code와 Codex에서 사용하는 한국어 회의록 요약 스킬.

원본 전사와 회의 자료를 대조해 고유한 업무 정보와 확정 여부를 보존하고, 같은 뜻의 반복 발언을 합쳐 요약한다. 기본 구성은 공유 사항, 논의 사항, 결정 사항이며 사용자가 지정한 구성과 이름 표기 방식을 우선한다. 글자 수나 항목 수에 맞추기 위해 정보를 생략하지 않는다.

요약은 채팅이나 파일로 제공할 수 있다. Notion 저장을 요청하면 기존 내용을 보존하면서 요약본만 추가하거나 수정한다. 원문은 새로 복사해 넣지 않는다.

## 구성

```text
skills/meeting-summary/
  SKILL.md
  LICENSE
  references/
    summary-context.md
    notion-storage.md
```

- [SKILL.md](skills/meeting-summary/SKILL.md): 호출 범위와 진행 방법
- [summary-context.md](skills/meeting-summary/references/summary-context.md): 작성 기준과 예시
- [notion-storage.md](skills/meeting-summary/references/notion-storage.md): Notion 접근 수단과 저장 절차

## 설치

저장소는 배포 원본을 관리하는 곳이며, 도구가 실행할 설치본은 각 스킬 경로에 실제 파일로 복사한다. 설치 후에는 이 저장소의 작업 폴더를 옮기거나 삭제해도 설치본을 사용할 수 있다.

다음은 대상 프로젝트 루트에서 두 도구에 새로 설치하는 예시다. `SKILL_SOURCE`는 내려받은 저장소의 실제 절대 경로로 바꾼다. 두 설치 경로가 아직 없을 때 실행하며, 기존 설치가 있으면 기존 내용과 심볼릭 링크 여부를 먼저 확인하고 해당 설치본만 갱신한다.

```sh
SKILL_SOURCE="/path/to/meeting-summary-skill/skills/meeting-summary"
mkdir -p .agents/skills .claude/skills
cp -R "$SKILL_SOURCE" .agents/skills/meeting-summary
cp -R "$SKILL_SOURCE" .claude/skills/meeting-summary
```

배포 원본을 수정한 뒤에는 사용할 도구의 설치본도 별도로 갱신한다. 설치 경로에서 Git 작업 폴더를 심볼릭 링크로 참조하지 않는다.

요약 작성에는 별도 실행 코드나 패키지가 필요하지 않다. Notion 저장에는 현재 환경에서 사용할 수 있는 Notion 연결 또는 CLI와 대상 페이지의 수정 권한이 필요하다. 이 스킬은 별도 도구를 설치하거나 인증 정보를 만들지 않는다.

## 사용 예시

```text
meeting-summary 스킬로 아래 전사를 요약해 주세요.
```

```text
이 회의 페이지의 기존 요약본만 수정해 주세요.
이름은 원문 표기를 유지하고, 안건과 후속 작업으로 구성해 주세요.
```

## 라이선스

[MIT 라이선스](LICENSE)를 적용합니다. 스킬 폴더에도 동일한 라이선스 전문을 포함합니다.
