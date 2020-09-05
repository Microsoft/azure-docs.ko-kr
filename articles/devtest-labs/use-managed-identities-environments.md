---
title: Azure 관리 되는 id를 사용 하 여 DevTest Labs에서 환경 만들기 | Microsoft Docs
description: Azure에서 관리 되는 id를 사용 하 여 Azure DevTest Labs 랩에서 환경을 배포 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e5bac4210afee6db1c7617dac1cd6d2ff9149439
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718982"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Azure 관리 되는 id를 사용 하 여 랩 환경 배포 

랩 소유자는 관리 되는 id를 사용 하 여 랩에 환경을 배포할 수 있습니다. 이 기능은 환경에 키 자격 증명 모음, 공유 이미지 갤러리 및 환경의 리소스 그룹 외부에 있는 네트워크와 같은 Azure 리소스에 대 한 참조를 포함 하거나 포함 하는 시나리오에서 유용 합니다. 이를 통해 해당 환경의 리소스 그룹으로 제한 되지 않는 샌드박스 환경을 만들 수 있습니다.

> [!NOTE]
> 현재 랩 마다 단일 사용자 할당 id가 지원 됩니다. 

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Portal를 사용 하 여 사용자 할당 관리 id에 대 한 역할을 생성, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)합니다. 
    
    관리 id가 랩과 동일한 지역 및 구독에서 생성 되었는지 확인 합니다. 관리 id는 동일한 리소스 그룹에 있을 필요가 없습니다.

## <a name="use-azure-portal"></a>Azure Portal 사용

이 섹션에서는 랩 소유자로 Azure Portal를 사용 하 여 랩에 사용자 관리 id를 추가 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **DevTest Labs**를 검색 합니다.
1. 랩 목록에서 원하는 랩을 선택 합니다.
1. **구성 및 정책**  ->  **id (미리 보기)** 를 선택 합니다. 
1. 사용자 할당 id를 추가 하려면 **사용자 할당 됨** 탭을 선택 합니다.
1. **추가** 를 누릅니다.
1. 만든 기존 사용자를 선택 하거나 드롭다운에서 액세스할 수 있습니다.
 
    ![사용자 관리 id 추가](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. 페이지 맨 위에 있는 **저장** 을 누릅니다.

    저장 되 면 랩은 모든 랩 환경을 배포 하는 동안이 id를 사용 합니다. 목록에서 id를 선택 하 여 Azure의 id 리소스에 액세스할 수도 있습니다. 

랩에 추가 된 id에는 환경에서 액세스 해야 하는 외부 리소스에 대 한 권한이 있는 한 환경을 배포 하는 동안 랩 소유자는 특별 한 작업을 수행할 필요가 없습니다. 

랩에 할당 된 사용자 관리 id를 변경 하려면 먼저 랩에 연결 된 id를 제거 하 고 랩에 다른 id를 추가 합니다. 랩에 연결 된 id를 제거 **하려면 ...를 선택 합니다. (줄임표)** 를 클릭 하 고 **제거**를 클릭 합니다. 

## <a name="use-api"></a>API 사용

1. Id를 만든 후에는이 id의 리소스 ID를 확인 합니다. 다음 샘플과 같습니다. 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. PUT Https 메서드를 수행 하 여 `ServiceRunner` 다음 예제와 비슷한 랩에 새 리소스를 추가 합니다. Service runner 리소스는 DevTest Labs에서 관리 되는 id를 관리 하 고 제어 하는 프록시 리소스입니다. Service runner 이름은 유효한 이름일 수 있지만 관리 되는 id 리소스의 이름을 사용 하는 것이 좋습니다. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    예를 들면 다음과 같습니다. 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
사용자 할당 id가 랩에 추가 되 면 Azure Resource Manager 환경을 배포 하는 동안 Azure DevTest Labs 서비스에서 사용 됩니다. 예를 들어 리소스 관리자 템플릿이 외부 공유 이미지 갤러리 이미지에 액세스 하는 데 필요한 경우 랩에 추가한 id에 공유 이미지 갤러리 리소스에 대 한 최소 필수 권한이 있는지 확인 합니다. 
