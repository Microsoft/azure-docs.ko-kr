---
title: 조직 외부에서 공유(ARM 템플릿) - Azure Data Share 빠른 시작
description: 이 빠른 시작에서는 Azure Data Share 및 Resource Manager 템플릿을 사용하여 고객 및 파트너와 데이터를 공유하는 방법을 알아봅니다.
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 76ef44d438b9af7ada6c1c464705a22ee10f4c58
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654109"
---
# <a name="quickstart-share-data-using-azure-data-share-and-resource-manager-templates"></a>빠른 시작: Azure Data Share 및 Resource Manager 템플릿을 사용하여 데이터 공유

Azure Resource Manager 템플릿을 사용하여 Azure 스토리지 계정에서 새로운 Azure Data Share를 설정하고 Azure 조직 외부의 고객 및 파트너와 데이터 공유를 시작하는 방법을 알아봅니다. 지원되는 데이터 저장소 목록은 [Azure Data Share에서 지원되는 데이터 저장소](./supported-data-stores.md)를 참조하세요.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

다음 리소스는 템플릿에 정의되어 있습니다.

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft.Storage/storageAccounts/blobServices/containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft.Storage/storageAccounts/providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft.DataShare/accounts](/rest/api/datashare/accounts/create)
* [Microsoft.DataShare/accounts/shares](/rest/api/datashare/shares/create)
* [Microsoft.DataShare/accounts/shares/dataSets](/rest/api/datashare/datasets/create)
* [Microsoft.DataShare/accounts/shares/invitations](/rest/api/datashare/invitations/create)
* [Microsoft.DataShare/accounts/shares/synchronizationSettings](/rest/api/datashare/synchronizationsettings/create)

템플릿에서는 다음 작업을 수행합니다.

* 스토리지 계정 및 데이터 공유 데이터 원본으로 사용되는 컨테이너를 만듭니다.
* 데이터 공유 계정 및 데이터 공유를 만듭니다.
* 스토리지 Blob 데이터 Reader 역할을 원본 데이터 공유 리소스에 부여할 역할 할당을 만듭니다. [Azure Data Share의 역할 및 요구 사항](./concepts-roles-permissions.md)을 참조하세요.
* 데이터 세트를 데이터 공유에 추가합니다.
* 수신자를 데이터 공유에 추가합니다.
* 데이터 공유에 대해 스냅샷 일정을 사용하도록 설정합니다.

이 템플릿은 학습 목적으로 작성되었습니다. 실제로는 일반적으로 기존 스토리지 계정에 일부 데이터가 있습니다. 데이터 세트를 만들기 위해 템플릿 또는 스크립트를 실행하기 전에 역할 할당을 만들어야 합니다. 경우에 따라 템플릿을 배포할 때 다음과 같은 오류 메시지가 나타날 수 있습니다.

```error message
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

배포가 RBAC 할당을 완료하기 전에 데이터 세트를 만들려고 하기 때문입니다. 오류 메시지에도 불구하고 배포가 성공할 수 있습니다.  [배포된 리소스 검토](#review-deployed-resources) 과정을 계속 진행할 수 있습니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하여 Azure에 로그인하고 템플릿을 엽니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. 다음 값을 선택하거나 입력합니다.

    * **구독**: 데이터 공유 및 기타 리소스를 만드는 데 사용되는 Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기**를 선택하여 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다.
    * **위치**: 리소스 그룹에 대한 위치를 선택합니다.
    * **프로젝트 이름**: 프로젝트 이름을 입력합니다.  프로젝트 이름은 리소스 이름을 생성하는 데 사용됩니다.  이전 템플릿에서 변수 정의를 참조하세요.
    * **위치**: 리소스에 대한 위치를 선택합니다.  리소스 그룹과 동일한 위치를 사용할 수 있습니다.
    * **초대 이메일**: 데이터 공유 수신자의 Azure 로그인 이메일 주소를 입력합니다.  이메일 별칭이 작동하지 않습니다.

    나머지 설정에 대해서는 기본값을 사용합니다.
1. **위에 명시된 사용 약관에 동의함**을 선택한 다음, **구매**를 선택합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 만든 데이터 공유 계정을 엽니다.
1. 왼쪽 메뉴에서 **공유 보내기**를 선택합니다.  나열된 스토리지 계정이 표시됩니다.
1. 저장소 계정을 선택합니다.  **세부 정보**에 템플릿에서 구성한 대로 동기화 설정이 표시됩니다.

    ![Azure Data Share 스토리지 계정 동기화 설정](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. 위쪽에서 **초대**를 선택합니다. 템플릿을 배포할 때 지정한 이메일 주소가 표시됩니다. **상태**는 **보류 중**이 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없으면 리소스 그룹을 삭제합니다. 그러면 리소스 그룹의 리소스가 삭제됩니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 데이터 공유를 만들고 수신자를 초대하는 방법을 알아보았습니다. 데이터 소비자가 데이터 공유를 수락하고 받는 방법을 알아보려면, [데이터 수락 및 받기](subscribe-to-data-share.md) 자습서를 계속 진행하세요.
