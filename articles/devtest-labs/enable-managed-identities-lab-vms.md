---
title: Azure DevTest Labs의 랩 Vm에서 관리 되는 id를 사용 하도록 설정
description: 이 문서에서는 랩 소유자가 랩 가상 머신에서 사용자 할당 관리 id를 사용 하도록 설정 하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717638"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 가상 컴퓨터에 대 한 사용자 할당 관리 id 사용
랩 소유자는 Azure DevTest Labs에서 랩 Vm (가상 컴퓨터)에 사용자 할당 관리 id를 사용 하도록 설정할 수 있습니다.

관리 id를 사용 하 여 코드에 자격 증명을 전달 하지 않고 Key Vault를 포함 하 여 AD (Azure Active Directory) 인증을 지 원하는 모든 서비스에 인증할 수 있습니다. 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id 란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.

이 기능을 사용 하 여 랩 사용자는 랩의 컨텍스트에서 Azure SQL Database와 같은 Azure 리소스를 공유할 수 있습니다. Id 자체에서 리소스에 대 한 인증을 처리 합니다. 구성 된 후에는이 id를 사용 하 여 모든 기존/새로 만든 랩 VM을 사용할 수 있습니다. 랩 사용자는 자신의 컴퓨터에 로그인 한 후 리소스에 액세스할 수 있습니다.

> [!NOTE]
> 랩 Vm에서 사용할 수 있도록 여러 사용자 할당 관리 id를 추가할 수 있습니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
랩 Vm에 사용자 할당 관리 id를 추가 하려면 다음 단계를 수행 합니다.

1. [구독에서 사용자 할당 관리 id 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. 랩에 대 한 **구성 및 정책** 페이지로 이동 합니다.
1. 왼쪽 메뉴에서 **id (미리 보기)** 를 선택 합니다.
1. **가상 컴퓨터** 탭을 선택 합니다.
1. **추가** 를 선택 하 여 미리 채워진 드롭다운 목록에서 기존 id를 선택 합니다. 

    > [!div class="mx-imgBorder"]
    > ![Id 추가 단추](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. 드롭다운 목록에서 기존 **사용자 관리 id** 를 선택 하 고 **확인**을 선택 합니다. 

    > [!div class="mx-imgBorder"]
    > ![Id 추가](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>API 사용

1.  Id를 만든 후 id의 리소스 ID를 확인 합니다. 다음 샘플과 같습니다. 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. PUT HTTPS 메서드를 실행 하 여 다음 예제와 같이 새 **ServiceRunner** 리소스를 랩에 추가 합니다. 

    Service runner 리소스는 DevTest Labs에서 관리 되는 id를 관리 하 고 제어 하는 프록시 리소스입니다. Service runner 이름은 유효한 이름일 수도 있지만 관리 되는 id 리소스의 이름을 사용 하는 것이 좋습니다.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>다음 단계
관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id 란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.







