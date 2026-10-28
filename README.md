# meeting-summary-skill

에이전트용 회의록 요약 스킬.

`SKILL.md`와 참고 문서의 지침을 읽을 수 있는 에이전트에서 사용한다.

원본 전사와 회의 자료를 대조해 고유한 업무 정보와 확정 여부를 보존하고, 같은 뜻의 반복 발언을 합쳐 요약한다. 기본 구성은 공유 사항, 논의 사항, 결정 사항이며 사용자가 지정한 구성, 작성 언어와 이름 표기 방식을 우선한다. 기본 작성 언어는 한국어다. 글자 수나 항목 수에 맞추기 위해 정보를 생략하지 않는다.

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

다음은 Claude Code와 Codex를 예로 들어 대상 프로젝트 루트에 새로 설치하는 방법이다. 다른 에이전트에서는 해당 도구의 스킬 설치 경로를 사용한다. `SKILL_SOURCE`는 내려받은 저장소의 실제 절대 경로로 바꾼다. 두 설치 경로가 아직 없을 때 실행하며, 기존 설치가 있으면 기존 내용과 심볼릭 링크 여부를 먼저 확인하고 해당 설치본만 갱신한다.

```sh
SKILL_SOURCE="/path/to/meeting-summary-skill/skills/meeting-summary"
mkdir -p .agents/skills .claude/skills
cp -R "$SKILL_SOURCE" .agents/skills/meeting-summary
cp -R "$SKILL_SOURCE" .claude/skills/meeting-summary
```

배포 원본을 수정한 뒤에는 사용할 도구의 설치본도 별도로 갱신한다. 설치 경로에서 Git 작업 폴더를 심볼릭 링크로 참조하지 않는다.

요약 작성에는 별도 실행 코드나 패키지가 필요하지 않다. Notion 저장에는 현재 환경에서 사용할 수 있는 Notion 연결 또는 CLI와 대상 페이지의 수정 권한이 필요하다. 이 스킬은 별도 도구를 설치하거나 인증 정보를 만들지 않는다.

### 설치 확인

아래 명령도 대상 프로젝트 루트에서 실행한다. 한 에이전트에만 설치했다면 해당 경로만 확인한다. 새 터미널에서는 설치할 때 사용한 `SKILL_SOURCE`를 다시 지정한다.

```sh
ls -ld .agents/skills/meeting-summary .claude/skills/meeting-summary
diff -r "$SKILL_SOURCE" .agents/skills/meeting-summary
diff -r "$SKILL_SOURCE" .claude/skills/meeting-summary
```

- `ls -ld` 결과의 첫 글자가 `d`이면 실제 디렉터리다. `l`이거나 `->`가 표시되면 심볼릭 링크다.
- 각 설치 폴더에 `SKILL.md`, `LICENSE`, `references/summary-context.md`, `references/notion-storage.md`가 있어야 한다. `diff`가 아무 내용도 출력하지 않고 종료 코드 0으로 끝나면 배포 원본과 일치한다.
- 에이전트에서 `meeting-summary`를 명시해 호출하고 설치 경로의 스킬을 읽는지 확인한다. 목록 갱신이 필요한 도구는 해당 도구의 재로드 또는 새 세션 안내를 따른다.
- 배포 원본 폴더를 이미 삭제했다면 설치 폴더의 파일과 에이전트의 호출 여부로 확인한다. 원본과 비교하려면 저장소를 다시 내려받아 `SKILL_SOURCE`를 지정한다.

### 갱신

1. 배포 원본 저장소의 로컬 수정 사항을 먼저 보존한다. 원본 작업 폴더를 삭제했다면 저장소를 다시 내려받는다. 로컬 수정이 없는 경우 다음 명령으로 최신 변경을 받는다.

   ```sh
   git -C "/path/to/meeting-summary-skill" pull --ff-only
   ```

2. 최신 원본의 `skills/meeting-summary/SKILL.md`와 참고 파일이 있는지 확인하고 `SKILL_SOURCE`를 지정한다. 설치본을 직접 수정했다면 그 내용도 별도로 보관한다.
3. 갱신할 설치 폴더만 휴지통으로 옮긴다. `trash`가 있는 환경에서 두 설치본을 모두 갱신하는 명령은 다음과 같다. `trash`가 없으면 파일 관리자의 휴지통 기능을 사용한다.

   ```sh
   trash .agents/skills/meeting-summary .claude/skills/meeting-summary
   ```

4. 기존 설치 폴더가 없어진 것을 확인한 뒤 위의 설치 명령을 다시 실행하고, 설치 확인 절차로 원본과 비교한다. 기존 폴더에 그대로 `cp -R`하면 스킬 폴더가 중첩되거나 이전 파일이 남을 수 있으므로 새 폴더로 복사한다.

### 삭제

사용하지 않을 에이전트의 `meeting-summary` 설치 폴더만 휴지통으로 옮긴다. 두 설치본을 모두 삭제하는 경우:

```sh
trash .agents/skills/meeting-summary .claude/skills/meeting-summary
```

`trash`가 없으면 파일 관리자의 휴지통 기능을 사용한다. 상위 `skills` 폴더나 다른 스킬은 삭제하지 않는다. 다른 위치에 설치했다면 실제 설치 경로를 대상으로 한다.

두 설치 경로가 더 이상 존재하지 않는지 확인한다. 에이전트에 이전 스킬이 계속 표시되면 해당 도구의 목록 재로드 또는 새 세션 안내를 따른다. 설치본 삭제와 배포 원본 저장소의 삭제는 별개다.

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
