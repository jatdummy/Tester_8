# 새 프로젝트 셋업

> 이 템플릿을 새 Unity 프로젝트에 적용할 때 사용자가 수행하는 체크리스트.
> 아직 작업 브랜치가 없으므로 Claude는 보호 브랜치에서 파일을 고치지 않는다. (`CLAUDE.md` 절대 원칙 1)

---

## 1. 파일 배치

- [ ] `CLAUDE.md`를 프로젝트 루트(`Assets`와 같은 위치)에 둔다.
- [ ] `Docs/`, `.claude/agents/`, `.claude/skills/`를 프로젝트 루트에 복사한다.
- [ ] 기술 문서 위치인 `Docs/Tech/` 폴더를 만든다.

## 2. 프로젝트 설정

- [ ] `Docs/TechSpec.md`를 채운다. (Develop 브랜치를 쓰면 보호 / 기준 브랜치 확인)

## 3. Git 저장소

- [ ] Unity용 `.gitignore`(`Library/`, `Temp/`, `Logs/`, `UserSettings/` 등)를 추가한다.
- [ ] 초기 커밋을 원격 `main`에 push하고 `git ls-remote origin main`으로 확인한다.
- [ ] Develop 브랜치를 쓰면 만들어 push하고, GitHub 기본 브랜치를 정한다. (기본 브랜치가 아닌 곳으로 병합하면 `Closes`로 Issue가 자동으로 닫히지 않는다)

## 4. Claude Code 확인

- [ ] `/memory`로 `CLAUDE.md`, `TechSpec.md`가 로드됐는지 확인한다.
- [ ] `/agents`로 `developer`, `reviewer`, `tech-writer`, `doc-auditor`가 보이는지 확인한다.

## 5. Unity

- [ ] Unity가 만든 기본 Input Action 에셋이 `Assets/Input/`이 아니면 에디터에서 옮기고, project-wide actions 지정을 Project Settings에서 확인한다.
- [ ] (MCP를 쓸 때) MCP 패키지 설치 → MCP 패널에서 Claude Code Configure → `/mcp`에서 connected 확인. (`.mcp.json`으로 등록했다면 첫 실행 때 서버 사용 승인)
- [ ] (MCP를 쓸 때) 상태를 바꾸지 않는 조회 도구(콘솔 로그, 씬 / 컴포넌트 조회, 컴파일 중 여부)의 이름과 action을 확인해 `Docs/Claude/UnityMCP.md` `1. 연결 정보`를 채우고, `TechSpec`의 `Unity MCP`를 `켬`으로 바꾼다.
- [ ] (MCP를 쓸 때) 조회 도구 이름(action 제외)을 `developer.md`, `reviewer.md`의 `tools` 끝에 이어 붙인다. (예 : `tools: Read, Glob, Grep, mcp__<서버>__read_console`)

## 6. GitHub

- [ ] `gh auth status`, `git remote -v`로 로그인과 원격 연결을 확인한다.
- [ ] (선택) `CLAUDE.md` `5장`의 Label을 미리 등록한다. (없으면 Issue 등록 때 생성된다)
- [ ] (선택) GitHub에서 보호 브랜치 규칙을 설정한다.

## 7. 공유 설정 (선택)

- [ ] `.claude/settings.json`의 `permissions`에 `CLAUDE.md` `4. Git 권한`을 반영한다. (상시 허용 = allow, 대행 = ask, 금지 = deny)
  - Unity MCP를 쓰면 조회 action만 있는 도구만 allow에 넣는다. 조회와 변경이 섞인 도구와 나머지 MCP 도구는 넣지 않아 확인 창이 뜨게 둔다.
  - deny 패턴은 다른 표기로 우회될 수 있으므로 규칙 문서를 대신하지 않는다.
- [ ] `.editorconfig`에 `[*.cs]` `charset = utf-8`을 지정한다.

## 8. 첫 작업으로 확인

- [ ] 간단한 기능 하나로 Issue → 브랜치 → 브리핑 → 작업 → 검수 → PR → 병합까지 한 번 진행해 본다.
