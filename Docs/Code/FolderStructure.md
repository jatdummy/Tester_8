# 폴더 구조

> 기본 구조다. 필요 없는 폴더는 만들지 않는다.

```
Assets/
├── Scripts/
│   ├── Core/            # GameManager, 공용 유틸, EnumList.cs
│   ├── Player/
│   ├── UI/
│   ├── Data/            # ScriptableObject 클래스 정의(.cs)
│   └── Editor/          # UnityEditor를 쓰는 에디터 전용 스크립트
├── Data/                # ScriptableObject 에셋(.asset)
├── Input/               # Input Action 에셋 (project-wide actions 포함)
├── Prefabs/             # 기능별 하위 폴더 (예 : Prefabs/Player/)
├── Scenes/              # 메인 씬은 바로 아래
│   └── Test/            # 기능 테스트용 씬 (Test_[기능].unity)
├── Materials/
├── Textures/
├── Models/
├── Animations/
├── Audio/
├── Plugins/
├── Settings/            # 렌더 파이프라인 설정 (Unity 생성)
├── TextMesh Pro/        # TMP Essential Resources (Unity 생성, 옮기지 않음)
├── Tests/               # Unity Test Framework (EditMode/, PlayMode/), 테스트를 쓸 때만
└── Imports/             # 에셋 스토어 등 외부 에셋 전부
    └── Fonts/           # 외부 폰트 원본과 그 폰트로 만든 TMP 폰트 에셋
```

## 규칙

- 직접 만든 에셋은 `Assets/` 바로 아래 용도별 폴더에, 외부 에셋은 `Assets/Imports/` 아래에 둔다.
  - 예외 : 경로가 코드에 고정됐거나 다시 임포트하면 원래 위치에 생기는 에셋(TMP Essential 등)은 원래 위치에 둔다.
  - Package Manager로 설치한 패키지는 `Packages/`에 있으며 이 규칙의 대상이 아니다.
- 새 스크립트는 기능별 폴더에 넣는다. 폴더가 애매하면 Claude가 브리핑에서 위치를 제안한다.
- `UnityEditor`를 쓰는 스크립트는 `Assets/Scripts/Editor/`에 둔다. (`Editor` 폴더 밖에 두면 빌드가 실패한다. asmdef를 쓰면 Editor 전용 asmdef로 분리한다)
- `Resources/`는 꼭 필요할 때만 쓰고 Inspector 참조를 우선한다. `StreamingAssets/`는 원본 파일 그대로 읽어야 하는 데이터에만 쓴다.
- Input Action 에셋은 `Assets/Input/`에 두고 Project Settings에서 project-wide actions로 지정한다.
- 기능 테스트는 `Assets/Scenes/Test/`의 테스트 씬에서 한다. (메인 씬 충돌·오염 방지)
- 폴더 이름은 PascalCase 영어. 패키지가 만든 폴더(`TextMesh Pro` 등)는 이름을 바꾸지 않는다.
- 에셋·폴더의 이동, 이름 변경, 삭제는 사용자가 Unity 에디터 안에서 한다. 탐색기에서 `.meta` 없이 옮기면 GUID가 바뀌어 참조가 끊어진다. Claude는 필요하면 요청만 한다.
