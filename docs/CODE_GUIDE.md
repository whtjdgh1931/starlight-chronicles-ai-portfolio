# 코드와 구현 해설

게임 코드 300개를 원래 폴더 구조로 제공합니다. 아래 표에서 관심 있는 동작의 구현부터 읽을 수 있습니다. 시연은 [Windows 빌드](../README.md#게임-실행), 보스 예고를 직접 조작하는 예제는 [Unity 데모](../README.md#unity-데모-실행)에서 확인할 수 있습니다.

| 보고 싶은 동작 | 읽을 자료 |
| --- | --- |
| 참가자의 공격 입력과 서버 판정 | [협동 입력 해설](../01-coop-input.md) · [NetworkPlayerController](../Source/Assets/01_Scripts/Network/NetworkPlayerController.cs) |
| 보스의 예고와 투사체 발사 | [보스 공격 예고 해설](../02-boss-telegraph.md) · [데모 프로젝트](../UnityDemo) |
| 전투 결과 수신과 저장 재시도 | [보스 결과 저장 해설](../03-battle-result-save.md) · [SaveFileStorage](../Source/Assets/01_Scripts/Save/SaveFileStorage.cs) |
| 시트 로딩과 대체 데이터 | [DataManager](../Source/Assets/01_Scripts/Manager/DataManager.cs) · [스냅샷 검증](../Source/Assets/01_Scripts/Data/GameDataSnapshotValidator.cs) |
| Codex로 만든 퀘스트 검증 도구 | [Inspector 진입점](../Source/Assets/01_Scripts/Debug/QuestValidation/Editor/QuestTesterEditor.cs) · [검증 실행 코드](../Source/Assets/01_Scripts/Debug/QuestValidation/Editor/QuestValidationRunner.cs) |

`Source`는 열람용 C# 소스이며, 전체 게임 빌드의 씬·외부 에셋·서비스 설정은 별도로 구성됩니다. `UnityDemo`는 Unity Hub에서 열 수 있는 독립 프로젝트입니다. 소스와 데모는 기존 [공개 코드 샘플 저장소](https://github.com/whtjdgh1931/starlight-chronicles-code-samples)에서 가져왔습니다.

[시연 영상](https://youtu.be/G9FgYX6wWyw) · [기획서 통합본 요약](GAME_DESIGN.md) · [실행 확인과 검증 범위](VERIFICATION.md) · [외부 리소스 출처](THIRD_PARTY_RESOURCES.md)
