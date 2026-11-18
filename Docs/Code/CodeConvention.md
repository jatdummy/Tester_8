# C# 코드 컨벤션

> Microsoft C# 가이드를 바탕으로 Unity 프로젝트에 맞게 조정했다. 예시의 이름은 설명용이다.
> 포맷(중괄호, 들여쓰기, 인코딩)은 프로젝트 루트의 `.editorconfig`가 강제한다. 이 문서와 다르면 이 문서를 따르고 `.editorconfig`를 고친다.

## 1. 네이밍

| 대상 | 규칙 | 예시 |
|---|---|---|
| 클래스 / 구조체 / enum 타입 | PascalCase. enum은 단수 명사(`[Flags]`만 복수) | `PlayerController`, `TowerType` |
| 인터페이스 | `I` + PascalCase | `IDamageable` |
| 메서드 | PascalCase, 동사로 시작 | `TakeDamage()` |
| async 메서드 / 코루틴 | 끝에 `Async` / `Routine` | `LoadStageAsync()`, `FadeRoutine()` |
| public 프로퍼티 | PascalCase | `Gold`, `IsTopView` |
| private 필드 | `_` + camelCase | `_moveSpeed` |
| private static 필드 | `s_` + camelCase | `s_instance` |
| 상수 (`const`, `static readonly`) | PascalCase (`s_`보다 우선) | `MaxTowerLevel` |
| 지역 변수 / 매개변수 | camelCase | `targetPos` |
| bool | `is / has / can` 접두사 | `_isTopView`, `CanJump` |
| 이벤트 | `On` + 과거형·동사 | `OnGoldChanged` |
| 이벤트 핸들러 | `Handle` + 이벤트 이름에서 `On`을 뺀 것 | `HandleGoldChanged()` |

- 이벤트 이름은 Unity 메시지(`OnEnable`, `OnTriggerEnter`, `OnDestroy` 등)와 겹치지 않게 짓는다.
- 축약어는 피한다. (`plr` ✕ → `player` ○) `Hp`, `UI`, `Id` 같은 관용어는 허용한다.
- 파일명 = 클래스명. 한 파일에 public 클래스 하나.
- 타입을 이름에 넣지 않는다. (`_speedFloat` ✕)
- 철자를 확인한다. 직렬화 필드 이름을 바꿀 때는 `3. Inspector 노출`을 따른다.

## 2. enum

- 모든 enum은 `Assets/Scripts/Core/EnumList.cs` 한 파일에 모은다. (`1. 네이밍`의 파일명 규칙의 예외)
- enum 타입 위에만 용도 주석을 적는다.
- "없음"이 필요하면 맨 앞에 `None = 0`을 둔다. 기본값이 0이므로 초기화하지 않은 값이 자동으로 "없음"이 된다.
- 모든 값에 번호를 직접 적고, 기존 번호는 바꾸거나 재사용하지 않는다. Unity는 enum을 숫자로 저장하므로 번호가 바뀌면 씬·프리팹의 값이 조용히 바뀐다.
- `[Flags]` enum은 `None = 0`과 2의 거듭제곱(1, 2, 4, 8…)으로 둔다.
- enum을 배열 인덱스로 바꾸는 코드는 메서드 하나에 모은다.

```csharp
// 타워 타입
public enum TowerType
{
    None = 0,
    ArrowTower = 1,
    FireTower = 2,
}
```

## 3. Inspector 노출

- Inspector 노출은 `[SerializeField] private`으로 한다. 외부에서 읽어야 하면 프로퍼티를 제공한다.
  - 예외 : `[Serializable]`을 붙인 데이터 전용 struct / class(메서드 없이 값만 담는 타입)는 public 필드를 쓸 수 있다. 이름은 PascalCase.
- 튜닝 수치에는 범위가 있으면 `[Range]`, 하한만 있으면 `[Min]`을 붙인다.
- 관련 필드가 많으면 `[Header("이동")]`으로 묶는다.
- 직렬화 필드 이름을 바꿀 때는 `[FormerlySerializedAs("_이전이름")]`을 붙인다. 붙이지 않으면 Inspector 값이 사라진다. 관련 씬·프리팹을 다시 저장한 뒤 지워도 된다.

```csharp
// 초당 이동 거리
[SerializeField, Min(0f)] private float _panSpeed = 15f;

public float PanSpeed => _panSpeed;
```

## 4. 클래스 내부 순서

1. 상수 / static 필드
2. `[SerializeField]` 필드
3. private 필드
4. 프로퍼티
5. 이벤트
6. Unity 생명주기 메서드 (`Awake → OnEnable → Start → Update → FixedUpdate → LateUpdate → OnDisable → OnDestroy` 순. 작성 순서이며 실행 순서가 아니다)
7. 그 밖의 Unity 메시지 (`OnTriggerEnter`, `OnCollisionEnter`, `OnValidate`, `OnDrawGizmos` 등)
8. public 메서드 (인터페이스 구현 포함)
9. private 메서드

- 필드는 클래스 위쪽에 모은다. 메서드 사이에 필드를 선언하지 않는다.

## 5. 생명주기 메서드

- Unity 메시지 메서드(`Awake`, `Update`, `OnTriggerEnter` 등)는 `private`으로 선언한다.
  - 예외 : 상속용 부모 클래스는 `protected virtual`로 선언하고, 자식은 `override`한 뒤 `base.Awake()`를 호출한다. 둘 다 `private`이면 부모 쪽이 실행되지 않는다.
- 하는 일이 메서드 하나로 위임되면 `=>` 한 줄로 쓴다.

```csharp
private void Awake() => CacheComponents();
private void OnEnable() => BindInputEvents();
```

## 6. 포맷

- 중괄호는 새 줄에서 시작한다. (Allman)
- 중괄호 생략은 같은 줄에 끝나는 조기 반환·건너뛰기만 허용한다. 그 밖에는 본문이 한 줄이어도 중괄호를 쓴다.
  - ○ `if (!_isPlaying) return;`, `if (tile == null) continue;`
- 들여쓰기 공백 4칸.
- 필드는 타입이 같으면 `new()`를 쓴다. (`private List<Monster> _monsters = new();`)
- `var`는 오른쪽에서 타입이 분명할 때(`new`, 리터럴, 캐스트)만 쓴다. (`var monsters = new List<Monster>();` ○, `var gold = GetGold();` ✕)
- `foreach`의 반복 변수는 타입을 적는다. (`foreach (Monster monster in _monsters)`)

## 7. 파일 인코딩

- `.cs` 파일은 UTF-8(BOM 없음)로 저장한다. `.editorconfig`의 `[*.cs]` `charset = utf-8`로 강제한다. (CP949로 저장되면 다른 환경에서 한글 주석이 깨진다)

## 8. Unity 작성 원칙

- 자기 컴포넌트는 `Awake`에서, 다른 오브젝트에 의존하는 초기화는 `Start`에서 한 번만 가져와 캐싱한다. `Update` 안에서 `GetComponent`·`Find`류를 호출하지 않는다.
- 참조는 Inspector 연결을 우선한다. `GameObject.Find("이름")`, `SendMessage`, 문자열 태그 비교(`tag ==`)는 쓰지 않고 `CompareTag()`를 쓴다.
- 컴포넌트가 없을 수 있으면 `GetComponent` + null 검사 대신 `TryGetComponent`를 쓴다.
- 입력 값은 `Update`에서 읽고, 물리 적용은 `FixedUpdate`에서 한다.
- 튜닝·밸런스에 쓰는 의미 있는 수치는 `[SerializeField]`나 상수로 뺀다. (0, 1, -1 같은 계산용 값은 해당 없음)
- 여러 오브젝트가 공유하는 밸런스 데이터는 `ScriptableObject`로 분리하는 것을 우선 고려한다.
- 자주 생성·파괴되는 오브젝트는 `UnityEngine.Pool.ObjectPool<T>`로 풀링한다.
- `Update` 등 매 프레임 코드에서 할당(`new`, LINQ, 문자열 연결, 람다 캡처)을 하지 않는다.
- 싱글톤은 꼭 필요한 매니저(GameManager 등)에만 쓴다.
- 스크립트 간 결합은 직접 참조보다 이벤트(`event Action`)나 인터페이스를 우선한다.

### 입력 (Input System)

- 패키지와 설정은 `Docs/TechSpec.md` `3. 유저 입력 시스템`을 따른다.
- 입력 액션은 `InputActionReference`로 참조한다.
- 액션(맵)의 `Enable()` / `Disable()`은 `TechSpec`의 `액션 활성화 방식`이 정한 한 곳에서만 한다. 사용하는 쪽은 호출하지 않는다. (한 컴포넌트가 `Disable()`하면 같은 액션을 쓰는 다른 컴포넌트의 입력도 꺼진다)
- `PlayerInput` 컴포넌트와 `InputActionReference`를 같은 액션에 섞어 쓰지 않는다.

### 이벤트 구독

- 이벤트와 입력 콜백은 `OnEnable`에서 구독하고 `OnDisable`에서 해제한다. 항상 쌍으로 쓴다.
- 람다가 아닌 메서드로 구독한다. (람다는 같은 인스턴스를 다시 넘길 수 없어 해제가 누락되기 쉽다)
- 이벤트 호출은 `OnGoldChanged?.Invoke(value);`로 한다. (델리게이트는 아래 null 검사 규칙의 대상이 아니다)

### null 검사

- `UnityEngine.Object` 상속 타입(`MonoBehaviour`, `GameObject` 등)에는 `?.`, `??`, `??=`, `is null`을 쓰지 않고 `== null`을 쓴다. 파괴된 오브젝트는 `== null`에서만 null로 판정된다.
- 인터페이스 타입 변수는 `x == null || (x is UnityEngine.Object o && o == null)`로 검사한다.

### 비동기

- 지연·순차 처리는 코루틴 또는 `Awaitable`(Unity 6)을 쓴다. `Task.Delay`는 쓰지 않는다.
- `async void`는 쓰지 않는다. (Unity 이벤트 핸들러로 연결하는 경우만 예외)
- `MonoBehaviour`의 async 메서드는 `destroyCancellationToken`을 넘겨, 오브젝트가 파괴되면 멈추게 한다.

### Unity 6 API

- Obsolete 경고가 나는 API는 쓰지 않는다. `TechSpec` 버전이 `6000.x` 이상이면 아래를 따른다.
  - `velocity` → `linearVelocity`
  - `drag` / `angularDrag` → `linearDamping` / `angularDamping`
  - `FindObjectOfType` → `FindFirstObjectByType` / `FindAnyObjectByType`
  - `FindObjectsOfType` → `FindObjectsByType` (정렬이 필요 없으면 `FindObjectsSortMode.None`)

## 9. 주석

- 한국어, `//` 일반 주석만 쓴다. XML 주석(`///`)과 `[Tooltip]`은 쓰지 않는다.
- 대상의 바로 위 줄에 적는다. 줄 끝에 적지 않는다.
- 명사형으로 끝낸다. 한 줄을 원칙으로 하되, 이유 설명이 필요하면 두 줄까지 허용한다.
- 주석을 다는 곳 :
  - 클래스 : 항상 한 줄 요약.
  - `[SerializeField]` 필드 : 이름만으로 단위·범위·의미가 드러나지 않을 때. (`_panSpeed` → "초당 이동 거리")
  - 메서드, 코드 줄 : 이름과 코드만으로 의도나 이유를 알기 어려울 때만.
- 표시용 주석 : `// TODO:` 나중에 할 일, `// FIXME:` 알려진 문제, `// 테스트 로그` 지워야 할 `Debug.Log`.

```csharp
// 게임 전체 골드 관리
public class GoldManager : MonoBehaviour
{
    // 탑뷰 카메라 맵 밖 이동 방지
    private void ClampToMapBounds() { ... }
}
```

## 10. 불필요 코드

- 쓰지 않는 변수·메서드, 빈 메서드(빈 `Update` 포함), 주석 처리된 코드를 남기지 않는다. (이력은 git에 있다)
- PR 전에 `// 테스트 로그` 표시가 붙은 `Debug.Log`를 지운다.
