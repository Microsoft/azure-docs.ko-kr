---
title: Microsoft .Resources에서 리소스에 대 한 영역 이동
description: Microsoft .Resources 네임 스페이스에 있는 리소스를 새 지역으로 이동 하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951055"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Microsoft .Resources 리소스를 새 지역으로 이동

기존 리소스를 새 지역으로 이동 해야 할 수 있습니다. 이 문서에서는 templateSpecs 및 deploymentScripts의 두 리소스 유형 (Microsoft .Resources 네임 스페이스에 있는)을 이동 하는 방법을 보여 줍니다.

## <a name="move-template-specs-to-new-region"></a>템플릿 사양을 새 지역으로 이동

한 지역에 [템플릿 사양이](../templates/template-specs.md) 있고이를 새 지역으로 이동 하려는 경우 템플릿 사양을 내보내고 다시 배포할 수 있습니다.

1. 명령을 사용 하 여 기존 템플릿 사양을 내보냅니다. 매개 변수 값에는 내보내려는 템플릿 사양에 일치 하는 값을 제공 합니다.

   Azure PowerShell의 경우 다음을 사용합니다.

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Azure CLI의 경우

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. 내보낸 템플릿 사양을 사용 하 여 새 템플릿 사양을 만듭니다. 다음 예에서는 `westus` 새 지역에 대해 보여 주지만 원하는 지역을 제공할 수 있습니다.

   Azure PowerShell의 경우 다음을 사용합니다.

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Azure CLI의 경우

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>배포 스크립트를 새 지역으로 이동

1. 새 지역으로 이동 하려는 배포 스크립트를 포함 하는 리소스 그룹을 선택 합니다.

1. [템플릿을 내보냅니다](../templates/export-template-portal.md). 내보내는 경우 배포 스크립트와 기타 필요한 리소스를 선택 합니다.

1. 내보낸 템플릿에서 다음 속성을 삭제 합니다.

   * tenantId
   * principalId
   * clientId

1. 내보낸 템플릿에는 배포 스크립트 영역에 대 한 하드 코드 된 값이 있습니다.

   ```json
   "location": "westus2",
   ```

   위치를 설정 하는 매개 변수를 허용 하도록 템플릿을 변경 합니다. 자세한 내용은 [ARM 템플릿에서 리소스 위치 설정](../templates/resource-location.md) 을 참조 하세요.

   ```json
   "location": "[parameters('location')]",
   ```

1. [내보낸 템플릿을 배포](../templates/deploy-powershell.md) 하 고 배포 스크립트의 새 지역을 지정 합니다.

## <a name="next-steps"></a>다음 단계

* 새 리소스 그룹 또는 구독으로 리소스를 이동 하는 방법에 대 한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조 하세요.
* 리소스를 새 지역으로 이동 하는 방법에 대해 알아보려면 [지역 간에 Azure 리소스 이동](move-region.md)을 참조 하세요.
