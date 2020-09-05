---
title: PowerShell을 사용 하 여 청사진 가져오기 및 내보내기
description: 청사진 정의를 코드로 사용 하는 방법에 대해 알아봅니다. Export 및 import 명령을 사용 하 여 공유, 소스 제어 및 관리 합니다.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: 87e8f00013fec3abb8132f92c1c717f152d1e264
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051443"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>PowerShell을 사용 하 여 청사진 정의 가져오기 및 내보내기

Azure 청사진은 Azure Portal를 통해 완벽 하 게 관리할 수 있습니다. 조직에서 Azure 청사진을 사용 하 여 이동할 때 청사진 정의를 관리 코드로 간주 해야 합니다. 이 개념을 종종 IaC (Infrastructure as Code) 라고 합니다. 청사진 정의를 코드로 처리 하면 Azure Portal 제공 하는 것 이상의 추가 이점을 제공 합니다. 이러한 이점은 다음과 같습니다.

- 청사진 정의 공유
- 청사진 정의 백업
- 다른 테 넌 트 또는 구독에서 청사진 정의 재사용
- 소스 제어에 청사진 정의 배치
  - 테스트 환경에서 청사진 정의의 자동화 된 테스트
  - CI/CD (연속 통합 및 연속 배포) 파이프라인 지원

코드에 따라 청사진 정의를 관리 하는 것은 어떤 이유로 든 도움이 됩니다. 이 문서에서는 `Import-AzBlueprintWithArtifact` `Export-AzBlueprintWithArtifact` [Az. 청사진](https://powershellgallery.com/packages/Az.Blueprint/) 모듈에서 및 명령을 사용 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure 청사진에 대 한 일반적인 실무 지식을 전제로 합니다. 아직 수행 하지 않은 경우 다음 문서를 진행 합니다.

- [포털에서 청사진 만들기](../create-blueprint-portal.md)
- [배포 단계](../concepts/deployment-stages.md) 및 [청사진 수명 주기](../concepts/lifecycle.md) 에 대해 자세히 알아보세요.
- PowerShell을 사용 하 여 청사진 정의 및 할당 [만들기](../create-blueprint-powershell.md) 및 [관리](./manage-assignments-ps.md)

아직 설치되지 않은 경우 [Az.Blueprint 모듈 추가](./manage-assignments-ps.md#add-the-azblueprint-module)를 수행하여 PowerShell 갤러리에서 **Az.Blueprint** 모듈을 설치하고 유효성을 검사합니다.

## <a name="folder-structure-of-a-blueprint-definition"></a>청사진 정의의 폴더 구조

청사진을 내보내고 가져오는 방법을 살펴보기 전에 청사진 정의를 구성 하는 파일이 어떻게 구조화 되었는지 살펴보겠습니다. 청사진 정의는 자체 폴더에 저장 해야 합니다.

> [!IMPORTANT]
> Cmdlet의 **Name** 매개 변수에 값을 전달 하지 않으면 `Import-AzBlueprintWithArtifact` 청사진 정의가 저장 되는 폴더의 이름이 사용 됩니다.

명명 되어야 하는 청사진 정의와 함께 `blueprint.json` 청사진 정의가 구성 된 아티팩트입니다. 각 아티팩트는 이라는 하위 폴더에 있어야 합니다 `artifacts` .
청사진 정의의 구조는 폴더의 JSON 파일로 다음과 같이 표시 되어야 합니다.

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>청사진 정의 내보내기

청사진 정의를 내보내는 단계는 간단 합니다. 청사진 정의를 내보내면 소스 제어에 공유, 백업 또는 배치 하는 데 유용할 수 있습니다.

- **청사진** [필수]
  - 청사진 정의를 지정 합니다.
  - `Get-AzBlueprint`참조 개체를 가져오는 데 사용
- **OutputPath** [필수]
  - 청사진 정의 JSON 파일을 저장할 경로를 지정 합니다.
  - 출력 파일은 청사진 정의의 이름이 포함 된 하위 폴더에 있습니다.
- **Version** (선택 사항)
  - **청사진** 참조 개체에 둘 이상의 버전에 대 한 참조가 포함 된 경우 출력할 버전을 지정 합니다.

1. 다음과 같이 표시 된 구독에서 내보낼 청사진 정의에 대 한 참조를 가져옵니다 `{subId}` .

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Cmdlet을 사용 `Export-AzBlueprintWithArtifact` 하 여 지정 된 청사진 정의를 내보냅니다.

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>청사진 정의 가져오기

[내보낸 청사진 정의가](#export-your-blueprint-definition) 있거나 [필요한 폴더 구조](#folder-structure-of-a-blueprint-definition)에 수동으로 만든 청사진 정의를 포함 하는 경우 해당 청사진 정의를 다른 관리 그룹 또는 구독으로 가져올 수 있습니다.

기본 제공 청사진 정의의 예는 [GitHub 리포지토리 Azure Blueprint](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)를 참조 하세요.

- **이름** [필수]
  - 새 청사진 정의의 이름을 지정 합니다.
- **Inputpath** [필수]
  - 청사진 정의를 만들 경로를 지정 합니다.
  - [필요한 폴더 구조](#folder-structure-of-a-blueprint-definition) 와 일치 해야 합니다.
- **Managementgroupid** (선택 사항)
  - 현재 컨텍스트 기본값이 아닌 경우 청사진 정의를 저장할 관리 그룹 ID입니다.
  - **Managementgroupid** 또는 **SubscriptionId** 를 지정 해야 합니다.
- **SubscriptionId** (선택 사항)
  - 현재 컨텍스트 기본값이 아닌 경우 청사진 정의를 저장할 구독 ID입니다.
  - **Managementgroupid** 또는 **SubscriptionId** 를 지정 해야 합니다.

1. Cmdlet을 사용 `Import-AzBlueprintWithArtifact` 하 여 지정 된 청사진 정의를 가져옵니다.

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

청사진 정의를 가져온 후에는 [PowerShell을 사용 하 여 할당](./manage-assignments-ps.md#create-blueprint-assignments)합니다.

고급 청사진 정의를 만드는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [정적 및 동적 매개 변수를](../concepts/parameters.md)사용 합니다.
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정 합니다.
- [청사진 리소스 잠금으로](../concepts/resource-locking.md)배포를 보호 합니다.
- [청사진을 코드로 관리](https://github.com/Azure/azure-blueprints/blob/master/README.md)합니다.

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결