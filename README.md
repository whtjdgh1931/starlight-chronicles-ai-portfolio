# Starlight Chronicles

**조성호 · 1인 개발 · Unity / C# · Windows 2D 액션 RPG**

몬스터를 사냥해 장비를 얻고, 장착하거나 강화한 뒤 혼자 또는 다른 플레이어와 보스에 도전합니다. 전투와 성장 흐름, 협동 플레이, 캐릭터 저장을 구현했습니다. 개발 과정에서 Codex를 활용해 기존 코드의 영향을 조사하고, 기능 구현과 검증 도구 제작을 진행했습니다.

[GDD v4.1 통합본 요약](docs/GAME_DESIGN.md) · [시연 영상](https://youtu.be/G9FgYX6wWyw) · [게임 실행](#게임-실행) · [Unity 데모 실행](#unity-데모-실행) · [공개 C# 소스](Source/Assets/01_Scripts) · [코드 안내](docs/CODE_GUIDE.md) · [AI 활용 사례](#ai-활용-사례) · [검증 기록](#검증-기록)

## 게임 실행

[Windows ZIP 다운로드](https://github.com/whtjdgh1931/starlight-chronicles-ai-portfolio/releases/download/portfolio-demo-v1/Starlight-Chronicles-Windows.zip) · [릴리스 portfolio-demo-v1](https://github.com/whtjdgh1931/starlight-chronicles-ai-portfolio/releases/tag/portfolio-demo-v1)

1. ZIP 파일 전체를 압축 해제합니다.
2. 압축을 푼 폴더에서 `Starlight Chronicles.exe`를 실행합니다.

외부 리소스와 글꼴 라이선스 고지는 ZIP의 `THIRD-PARTY-NOTICES.txt`를 참고하세요.

## 프로젝트 개요

| 항목 | 내용 |
| --- | --- |
| 게임 흐름 | 사냥 → 장비 획득·강화 → 싱글 또는 협동 보스전 |
| 기획·개발 | 조성호. 게임 규칙과 플레이 기대 동작, 개발 범위, 검증 기준 설정 |
| 개발 환경 | Windows, Unity 6000.4.8f1, C#, Git, Codex |
| 사용 기술 | Unity Netcode for GameObjects, Lobby·Relay, Google Sheets 기반 데이터 로딩 |
| 공개 자료 | GDD v4.1 통합본 요약, Windows 실행 빌드, C# 소스 300개, 구현 해설, 독립 Unity 데모, 검증 기록 |

## AI 활용 사례

### 1. 협동 전투에서 내 타격음 구분

여러 사람이 동시에 공격하면 다른 플레이어의 효과음에 내 타격음이 묻힐 수 있다고 봤습니다. 내 공격이 맞았는지 소리로 구분할 수 있도록, 본인과 다른 참가자의 재생 채널을 나누고 원격 효과음의 음량을 낮췄습니다. 타격음의 재생 간격을 관리하고 채널이 모두 사용 중이면 새 효과음만 생략합니다.

- **제가 정한 기준** — 내 타격음을 구별할 수 있고, 다른 참가자의 전투음도 들려야 합니다.
- **Codex 활용** — 기존 효과음 호출 경로를 조사하고, 오디오 소스 선택과 재생 제한을 구현하는 데 활용했습니다.
- **확인 범위** — 채널 분리, 감쇠, 간격 제한은 코드에서 확인했습니다. 실제 다인 전투에서 같은 장면과 기기 음량을 기준으로 한 청취 비교는 후속 검증입니다.

[CombatSoundPlayer.Play() · 채널 선택과 음량 적용](Source/Assets/01_Scripts/Audio/CombatSoundPlayer.cs#L34-L67)

### 2. 퀘스트 저장 실패를 재현하는 Unity Inspector 도구

퀘스트 보상을 확인할 때마다 사냥과 수집을 되풀이해야 했습니다. 전용 슬롯과 퀘스트를 선택하면 기존 게임 API로 수락·처치·보상 요청을 실행할 수 있도록 `QuestTester`를 만들었습니다. 저장을 한 번 실패시킨 뒤 저장만 다시 시도하고, 전후 스냅샷과 파일을 비교해 보상이 중복되는지 확인하도록 구성했습니다.

- **제가 정한 기준** — 저장 재시도에서 보상 수령 요청을 다시 보내지 않습니다. 전후 골드·아이템과 퀘스트 상태를 비교할 수 있어야 합니다.
- **Codex 활용** — 기존 퀘스트·저장 코드 조사, Inspector 실행 버튼, 실패 주입과 스냅샷 비교 구현에 활용했습니다.
- **확인 범위** — Inspector에서 수동 실행하는 개발 도구입니다. 도구 자체의 Play Mode 검증과 시간 절감 측정은 후속 항목입니다.

[QuestTesterEditor · Inspector 진입점](Source/Assets/01_Scripts/Debug/QuestValidation/Editor/QuestTesterEditor.cs) · [QuestValidationRunner.VerifySaveRetry() · 실패와 재시도 비교](Source/Assets/01_Scripts/Debug/QuestValidation/Editor/QuestValidationRunner.cs#L751)

### AI와 작업하는 순서

1. **재현 조건 결정** — 현재 동작, 기대 동작, 유지해야 할 처리를 먼저 적습니다.
2. **기존 코드 조사** — Codex로 관련 클래스와 실제 호출 위치를 찾고 영향 범위를 확인합니다.
3. **변경 범위 선택** — 기존 API를 사용할 수 있는지 검토하고 구현 범위를 정합니다. 프로젝트의 `AGENTS.md`에 코드 위치와 변경·검증 규칙을 기록했습니다.
4. **결과 확인** — 변경 내역과 검사 결과를 대조하고, 코드로 확인한 동작과 Unity에서 실행해 본 동작의 범위를 기록합니다.

요청 예시 · “전용 슬롯에서 퀘스트를 선택해 기존 API로 보상을 수령할 수 있게 해줘. 저장을 한 번 실패시킨 뒤에는 저장만 다시 시도하고, 골드·아이템과 퀘스트 상태를 전후로 비교할 수 있어야 해.”

타이틀·보스맵 배경과 일부 전투 이미지에는 이미지 생성을 활용했습니다. 일부 전투 효과음은 AI의 도움을 받아 작성한 코드로 로컬에서 합성했습니다. 외부 에셋과 라이브러리는 원본 배포처를 표기했습니다. [리소스 출처](docs/THIRD_PARTY_RESOURCES.md)

## 핵심 게임 구현

공개 소스 300개를 [Source/Assets/01_Scripts](Source/Assets/01_Scripts)에서 볼 수 있습니다. Windows 게임은 위 실행 빌드로, 보스 예고의 동작은 이 저장소의 독립 Unity 데모로 확인할 수 있습니다. [코드와 구현 해설 읽는 순서](docs/CODE_GUIDE.md)

| 기능 | 구현 내용과 코드 |
| --- | --- |
| 협동 입력과 서버 판정 | 참가자는 기본 공격을 입력하면 본인 화면에서 준비 자세를 먼저 봅니다. 공격 실행과 피해는 서버가 확정합니다. 참가자 본인의 이동·점프 최대 위치 보간 설정은 0.10초에서 0.04초로 조정했습니다. [입력 처리 발췌](01-coop-input.md) |
| 보스 공격 예고 | 발사 위치와 각 탄의 방향을 확정해 최종 예고와 실제 투사체에 함께 사용합니다. 중간 조준에는 전송 간격을 적용하고 최종 예고는 바로 보냅니다. [공격 예고 발췌](02-boss-telegraph.md) |
| 전투 결과와 저장 | 서버가 보스의 현재·최대 체력을 동기화합니다. 전투 결과는 동일한 결과 ID의 중복 처리를 막고, 저장 실패 시 동일 스냅샷으로 재시도합니다. 보상 저장 후에는 보스 로비로 복귀하는 경로를 구현했습니다. [보스 상태](Source/Assets/01_Scripts/Network/NetworkBossController.cs#L19) · [결과 저장 발췌](03-battle-result-save.md) · [로비 복귀](Source/Assets/01_Scripts/Manager/PlaySessionController.cs#L168) |
| 원격 데이터와 백업 복원 | Google Sheets 로딩 실패 시 유효한 로컬 캐시, 내장 기본값 순으로 확인합니다. 캐릭터 본 파일이 손상되면 백업을 읽고, 백업으로 복구한 직후의 저장이 실패해도 정상 백업을 보존합니다. [DataManager](Source/Assets/01_Scripts/Manager/DataManager.cs) · [SaveFileStorage](Source/Assets/01_Scripts/Save/SaveFileStorage.cs) |

## 검증 기록

다음은 개발 중 해당 작업본에서 수행한 격리 검사 결과입니다.

| 검증 | 확인한 내용 |
| --- | --- |
| 저장·복구 | Unity Editor 격리 검사 48건 통과. 본 파일 손상 후 백업 복구와 복구 직후 쓰기 실패 시 정상 백업 보존을 확인했습니다. |
| 보스 공격 | 실제 프리팹을 사용한 격리 검사에서 최종 예고 방향과 투사체 발사 방향이 일치하는지 확인했습니다. |
| 네트워크 예고 | 로컬 UDP 지연 120±20ms, 손실률 5% 설정에서 최종 예고를 147.4ms 후 수신했습니다. 해당 검사에서 발사점·방향·숨김 상태가 일치했습니다. |
| 참가자 입력 | 격리 프리팹에서 공격 준비 자세의 즉시 표시와 연타 중복 방지를 확인했습니다. 0.04초는 위치 보간 설정값입니다. |

최신 빌드에서는 외부망 2인 전체 전투와 재접속, 실제 입력부터 화면 반응까지의 시간, 다인 전투음 청취가 후속 검증 항목입니다. [공개 검증 기록](docs/VERIFICATION.md)

## Unity 데모 실행

1. 이 저장소를 내려받아 Unity Hub에 [UnityDemo](UnityDemo) 폴더를 추가합니다.
2. Unity **6000.4.8f1**에서 `Assets/Scenes/PortfolioDemo.unity`를 열고 Play를 누릅니다.
3. **Start boss attack**을 눌러 조준을 시작합니다. 분홍색 예고가 플레이어 위치를 추적하고 금색으로 바뀌면 경로가 확정됩니다. **Lane** 버튼으로 경로를 벗어나 회피할 수 있습니다.

예고·경로 확정·회피를 확인할 수 있는 독립 데모입니다. 외부 에셋이나 서비스 계정 없이 Unity 기본 GUI로 실행할 수 있습니다. [데모 폴더](UnityDemo)
