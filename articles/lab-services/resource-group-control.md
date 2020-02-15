---
title: Azure DevTest Labs에서 VM에 대한 리소스 그룹 지정 | Microsoft Docs
description: Azure DevTest Labs의 랩에서 VM에 대한 리소스 그룹을 지정하는 방법을 알아봅니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134524"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 가상 머신에 대한 리소스 그룹 지정

랩 소유자는 랩 가상 머신을 특정 리소스 그룹에 만들도록 구성할 수 있습니다. 이 기능은 다음 시나리오에서 도움이 됩니다.

- 구독의 랩에서 더 적은 리소스 그룹을 만들도록 합니다.
- 구성 하는 고정 된 리소스 그룹 집합 내에서 랩을 운영 하도록 합니다.
- Azure 구독 내에서 리소스 그룹을 만드는 데 필요한 승인 및 제한을 해결합니다.
- 단일 리소스 그룹 내의 모든 랩 리소스를 통합 하 여 리소스 그룹 수준에서 리소스를 관리 하는 [정책을](../governance/policy/overview.md) 적용 하 고 해당 리소스를 간편 하 게 추적할 수 있습니다.

이 기능을 사용 하면 스크립트를 사용 하 여 Azure 구독 내에서 모든 랩 Vm에 대 한 새 리소스 그룹 또는 기존 리소스 그룹을 지정할 수 있습니다. 현재 Azure DevTest Labs는 API를 통해이 기능을 지원 합니다.

> [!NOTE]
> DevTest Labs에서 랩을 만들 때 모든 구독 한도가 적용 됩니다. 랩을 구독에서 다른 리소스로 간주 합니다. 리소스 그룹의 경우 [구독 당 980 리소스 그룹이](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)제한 됩니다. 

## <a name="use-azure-portal"></a>Azure Portal 사용
랩에서 생성 된 모든 Vm에 대 한 리소스 그룹을 지정 하려면 다음 단계를 수행 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택 합니다. 
3. 목록에서 **DevTest Labs** 를 선택 합니다.
4. 랩 목록에서 **랩을**선택 합니다.  
5. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책** 을 선택 합니다. 
6. 왼쪽 메뉴에서 **랩 설정** 을 선택 합니다. 
7. **한 리소스 그룹의 모든 가상 컴퓨터를**선택 합니다. 
8. 드롭다운 목록에서 기존 리소스 그룹을 선택 하거나, **새로 만들기**를 선택 하 고, 리소스 그룹의 **이름을** 입력 하 고, **확인**을 선택 합니다. 

    ![모든 랩 Vm에 대 한 리소스 그룹을 선택 합니다.](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>PowerShell 사용 
다음 예에서는 PowerShell 스크립트를 사용 하 여 새 리소스 그룹에 모든 랩 가상 컴퓨터를 만드는 방법을 보여 줍니다.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

다음 명령을 사용 하 여 스크립트를 호출 합니다. ResourceGroup. p s 1은 이전 스크립트를 포함 하는 파일입니다.

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
Azure Resource Manager 템플릿을 사용 하 여 랩을 만드는 경우 다음 예제와 같이 템플릿의 랩 속성 섹션에 있는 **vmCreationResourceGroupId** 속성을 사용 합니다.

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>랩 Vm에 대 한 리소스 그룹을 구성 하는 API
이 API를 사용할 때 랩 소유자는 다음과 같은 옵션을 사용할 수 있습니다.

- 모든 가상 컴퓨터에 대 한 **랩의 리소스 그룹** 을 선택 합니다.
- 모든 가상 컴퓨터에 대해 랩의 리소스 그룹 이외의 **기존 리소스 그룹** 을 선택 합니다.
- 모든 가상 컴퓨터에 대 한 **새 리소스 그룹** 이름을 입력 합니다.
- 랩의 각 VM에 대해 리소스 그룹이 만들어지는 기존 동작을 계속 사용 합니다.
 
이 설정은 랩에 생성된 새 가상 머신에 적용됩니다. 랩에서 자체 리소스 그룹에 생성 된 이전 Vm은 영향을 받지 않습니다. 랩에서 만든 환경은 계속 해 서 자체 리소스 그룹에 남아 있습니다.

이 API를 사용하는 방법:
- API 버전 **2018_10_15_preview**을 사용 합니다.
- 새 리소스 그룹을 지정 하는 경우 구독에서 **리소스 그룹에 대 한 쓰기 권한이** 있는지 확인 합니다. 쓰기 권한이 없으면 지정 된 리소스 그룹에서 새 가상 컴퓨터를 만들 수 없습니다.
- 이 API를 사용하는 동안 **전체 리소스 그룹 ID**를 전달합니다. 예: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>` 리소스 그룹이 랩과 동일한 구독에 있는지 확인 합니다. 


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [랩에 대한 정책 설정](devtest-lab-get-started-with-lab-policies.md)
- [질문과 대답](devtest-lab-faq.md)
