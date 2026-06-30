## **LANGUAGE:**  [English](Begin_en.md) | [日本語](Begin_jp.md) | [中文](Begin_zh.md) | [한국어](Begin_kr.md)

1. [FModel](https://fmodel.app/)을 다운로드하여 설치하세요. (FModel은 Wine을 통해 Linux에서도 동작합니다.)
2. Directory -> Selector를 클릭해 Octopath Traveler 0를 새 게임으로 추가합니다. DataBase 파일을 자동으로 디시리얼라이즈(deserialize)하려면 UE Version에서 `GAME_OctopathTraveler0`를 반드시 선택하세요. 마지막으로 Directory에서 Octopath 0의 `Paks/` 폴더를 지정합니다.  <img width="488" height="348" alt="image" src="https://github.com/user-attachments/assets/2499b686-2cf9-406d-96bc-03c32c8b0dfa" />
3. Settings로 이동해 `Local Mapping File`을 활성화하고 Octopath Traveler 0의 mappings를 가져옵니다. mappings는 BravelyPath Modular Discord에서 구할 수 있으며, UE4SS로 수동 추출도 가능합니다.
4. 모드 자체는 암호화되어 있지 않지만, Octopath 0의 메인 pak은 암호화되어 있습니다. Directory -> AES로 이동해 Octopath Traveler 0의 AES 키를 입력해야 합니다. 키는 BravelyPath Modular Discord에서 구할 수 있으며, IDA/Ghidra로 수동 추출할 수도 있습니다.
5. Octopath 0의 pak 또는 모드 pak을 열고 수정하고 싶은 에셋으로 이동합니다. 수정할 항목을 추출하세요. DataBase 에셋의 경우 보통 에셋 프로퍼티와 raw data(`.uasset`)를 추출합니다.
6. 수정한 JSON 프로퍼티 파일로 DataBase 에셋을 패치하려면 `UAssetMessagePack.exe`를 사용하세요. 다운로드 링크는 BravelyPath Modular Discord의 #resources에 있습니다.
7. FluffyQuack의 [UnrealPak](https://github.com/Dmgvol/UE_Modding/raw/main/Tools/UnrealPak.zip)을 사용해 에셋을 pak 파일로 다시 패킹(repack)하세요. pak으로 만들 폴더는 게임의 경로 구조를 그대로 반영해야 합니다. 예를 들어 `/Octopath_Traveler0/Content/Local/DataBase/Skill/` 아래의 `SkillID`를 수정했다면, UnrealPak.exe에 드래그하는 폴더는 수정된 파일로 이어지는 경로가 **완전히 동일**해야 합니다(`uasset`와 `uexp` 파일을 둘 다 포함해야 합니다).
8. 마지막으로, 새로 만든 pak 파일을 게임의 `Paks/` 폴더에 넣으세요(또한 [설치](https://github.com/veganprimate/CotCPort/blob/main/README.kr.md#%EC%84%A4%EC%B9%98) 참고).

기타 리소스:
- [커스텀 직업을 위한 직업 데이터 및 캐릭터 스킬 세트 편집에 관한 Johesy의 가이드](https://github.com/dustintchan/OT0-Modding-Job-Skills)
- Celes Corbin의 Modding Tutorial ZIP(다운로드 링크: BravelyPath Modular Discord의 #resources)
