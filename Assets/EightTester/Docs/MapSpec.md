# 8번 테스터 — 맵 사양서 (OfficeMap)

데모 프로젝트에서 스크립트를 모두 뺀 환경 패키지. 로직은 새 프로젝트에서 직접 작성한다.

## 0. 새 프로젝트에서 먼저 할 것
- Unity 6000.3.x, Universal 3D(URP) 템플릿
- 패키지: ProBuilder(사무실 벽·바닥이 ProBuilder 메쉬), Input System
- URP Asset: Use Rendering Layers 켜기 / Renderer: Forward+
- Rendering Layers 이름: Layer 0 사무실(1), Layer 1 계단실(2), Layer 3 직원 통로(8)
- Static 은 전부 꺼져 있음 (배치 변경을 위해 의도적으로 끔)

## 1. 하이어라키
Office
- Env: Shell(ProBuilder 벽·바닥·천장·걸레받이·계단실 사무실 쪽 면), CeilingFixtures(프리팹), Lights(형광등), Windows(창문·아침 햇빛), Furniture(책상 자리·캐비닛·사물함·화분대·휴지통), Props(포스터·소화기·정수기·에어컨·CCTV·화분·화이트보드·안내문·탕비실/회의실 문·벽시계), Signs(승인·롤백·퇴근 전용·소화기 표지)
- Stairwells: Stairwell_Start / Merge / Rollback (모두 Stairwell.prefab 인스턴스), RollbackCorridor(직원 통로)
- Interactables: Elevator, QaLogTerminal(버그 리포트 컴퓨터), ReviewMonitor(코드 보드), RollbackOfficeDoor, StairDoors(계단실 문 9개), DoorFrames
- NPC: Colleague(QA 07), Senior(QA 01) — 자세 없음(Mixamo 애니메이션 연결 예정)
- Markers: 좌표 표시용 빈 오브젝트 (아래 3번)

## 2. 편집 규칙
- 사무실 껍데기(Env/Shell)는 ProBuilder: 면·모서리를 직접 편집해도 질감(월드 UV)이 유지된다.
- 계단실은 프리팹만 수정한다. 인스턴스 하나만 고치면 텔레포트 이음새가 깨진다.
- 계단실 3개는 회전·크기 변경 금지. 위치 이동은 Markers/StairRoots 와 함께.
- 계단실 안쪽 벽에는 무늬 있는 월드 좌표 질감을 쓰지 않는다(텔레포트 순간 무늬가 어긋남).

## 3. Markers (코드에서 숫자 대신 참조할 것)
| 마커 | 의미 |
|---|---|
| Player/PlayerSpawn | 시작 위치, yaw 90 (+x를 봄) |
| StairRoots/StairRoot_Start·Merge·Rollback | 계단실 기준점. 승인 텔레포트 = Start - Merge = (-34.25, -3.2, -6.2), 롤백 텔레포트 = Start - Rollback = (11, 3.2, -4.1) |
| TriggerZones/Zone_CorridorEnter | 통과하면 판정 대기 시작 (BoxCollider isTrigger) |
| TriggerZones/Zone_Forward | 승인 계단 중간 판정 지점 |
| TriggerZones/Zone_Rollback | 롤백 계단(꺾은 뒤) 중간 판정 지점 |
| ColleaguePath/0_Start→1_Doorway→2_Aisle→3_Seat_QA07 | 동료 출근 동선. 앉을 때 바라보는 방향 = 마커 yaw(180, -z), 데모 앉기 하강값 0.36 |
| SeniorSeat/Seat_QA01 | 선임 자리 (+z를 봄) |
| LeavePath_Senior/0~4, LeavePath_Colleague/0~3 | 06:00 퇴근 동선 (마지막 점 = 엘리베이터 안). 데모: 선임 즉시 출발·일어설 때 0.36 상승, 동료 0.6초 뒤 |
| Elevator/ElevatorInside | 엘리베이터 탑승 판정 중심 |
| AnomalyPoints/Extinguisher_OnCeiling | 소화기 천장 이상현상 위치 |

## 4. 문 (데모 값)
| 문 | 움직이는 부분 | 열림 이동(로컬) | 열림/닫힘(초) | 열리는 거리 |
|---|---|---|---|---|
| 승인 문 Merge_LowerDoor | Leaf_A, Leaf_B | (0,0,-0.8), (0,0,0.8) | 1.4 / 1.8 | 3.2 |
| 시작 문 Start_UpperDoor | Panel | (1,0,0) | 1.2 / 1.4 | 2.2, 한 방향(나오면 잠김, exit +z, 잠금 거리 0.8) |
| 롤백 사무실 문 RollbackOfficeDoor | Leaf_A, Leaf_B | (0,0,±0.8) (부모 Scale z 0.75 → 실제 ±0.6) | 1.4 / 1.8 | 2.2 |
| 롤백 계단 문 Rollback_TopDoor | Panel | (1,0,0) | 1.2 / 1.4 | 2.2 |
| 엘리베이터 DoorPanel | Leaf_L, Leaf_R | (0,0,-0.6), (0,0,0.6) | 1.5 / 1.8 | 2.2 (퇴근 때만), 닫힌 뒤 1.5초 |
| 나머지 계단실 문 | — | 열리지 않는 가짜 문 | — | 0 |

## 5. 이상현상용 오브젝트
- 꺼진 상태(ANOM_ 접두어): Whiteboard/ANOM_BloodWriting, TrashCan/ANOM_Overflow, Doors/Door_Meeting/ANOM_Eyes, WaterDispenser/ANOM_BloodLeak, Plants/Plant_B/ANOM_FallenLeaves
- 코드가 바꾸는 대상: Extinguisher(위치), Lights 형광등(색), Posters(재질 → missing texture), Plants(시듦·숨김), FileCabinet(문 경첩·서랍), Whiteboard 마지막 줄 글자, Colleague(숨김·시선)
- 버그 리포트 컴퓨터 고장 연출: QaLogTerminal/Monitor 아래 Cracks, Shards(꺼짐), Sparks, Smoke, Flash

## 6. 기타
- 글자: TextMesh + DepthTestText 재질. 폰트 텍스처를 재질에 넣어 주는 처리(데모 FontTextureSync)는 직접 다시 만든다.
- 조명 레이어: 사무실 1, 계단실 2(세 계단실 공통), 직원 통로 8
- UI 겉모습 프리팹: Prefabs/UI/Tablet(태블릿 모델·페이지 배치), NoticeBulb(알림 전구 아이콘)
