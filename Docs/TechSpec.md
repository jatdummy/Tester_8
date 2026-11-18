# 프로젝트 설정 (Tech Specification)

> 프로젝트마다 달라지는 값을 모은 문서다. 새 프로젝트면 `Docs/Claude/Setup.md`에 따라 이 문서부터 채운다.

---

## 1. 프로젝트 제원

- 프로젝트명 : 8번 코딩
- 장르 : 1인칭 이상현상 탐색 루프 (8번 출구 라이크) - 소스 코드(명세)와 복도를 대조해 버그(이상현상)를 찾는 방식
- 팀 구성 : 1인 (`1인` / `팀`)

## 2. Unity 환경

- 버전 : `6000.3.9f1`
- 렌더 파이프라인 : URP
- 빌드 플랫폼 : Windows

## 3. 유저 입력 시스템

- Input System 패키지(`com.unity.inputsystem`)를 사용한다. 구 Input Manager는 사용하지 않는다.
- Player Settings > Active Input Handling = Input System Package (New)
- 액션 활성화 방식 : project-wide actions (`project-wide actions` / `입력 담당 클래스 : 클래스명`)
- 입력 코드 작성 규칙은 `Docs/Code/CodeConvention.md` `8. Unity 작성 원칙`을 따른다.

## 4. 워크플로우 설정

| 항목 | 값 | 선택지 |
|---|---|---|
| 보호 브랜치 | `main` | Develop 브랜치를 쓰면 `main`, `Develop` |
| 기준 브랜치 | `main` | Develop 브랜치를 쓰면 `Develop` |
| 병합 방식 | squash | `squash` / `merge` / `rebase` |
| 에이전트 검수 | 항상 | `항상` / `요청 시`. `요청 시`면 reviewer, doc-auditor를 사용자가 요청할 때만 호출한다. |
| 교육 모드 | 끔 | `켬` / `끔` |
| Unity MCP | 끔 | `켬` / `끔`. `켬`이면 `Docs/Claude/UnityMCP.md`를 채우고 따른다. |
