---
title: 특정 랩 정책에 사용자 권한 부여| Microsoft 문서
description: 각 사용자의 요구에 따라 DevTest Lab에서 특정 랩 정책에 사용자 권한을 부여하는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 70469a9e8737a9df18628951a061c97081c74080
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127381"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>특정 랩 정책에 사용자 권한 부여
## <a name="overview"></a>개요
이 문서에서는 PowerShell을 사용하여 특정 랩 정책에 사용자 권한을 부여하는 방법을 보여줍니다. 이런 방식으로 각 사용자의 요구 사항에 따라 권한을 적용할 수 있습니다. 예를 들어 특정 사용자에게 VM 정책 설정을 변경할 수 있는 기능은 부여하지만 비용 정책에 대해서는 부여하지 않을 수 있습니다.

## <a name="policies-as-resources"></a>리소스인 정책
[Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md) 문서에 설명된 대로 RBAC를 사용하면 Azure 리소스에 대해 세밀한 리소스 액세스 관리가 가능합니다. RBAC를 사용하여 DevOps 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다.

DevTest Lab에서 정책은 RBAC 작업 **Microsoft.DevTestLab/labs/policySets/policies/** 를 지원하는 리소스 종류입니다. 각 랩 정책은 정책 리소스 종류에 속한 리소스이며 RBAC 역할에 범위로 할당할 수 있습니다.

예를 들어 사용자 읽기/쓰기 권한을 부여 하기 위해 합니다 **허용 된 VM 크기** 정책을 사용 하는 사용자 지정 역할을 만듭니다 합니다 **Microsoft.DevTestLab/labs/policySets/policies/** 작업 에 다음이 사용자 지정 역할의 범위에 적절 한 사용자를 할당 **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**합니다.

RBAC의 사용자 지정 역할에 대한 자세한 내용은 [사용자 지정 역할 액세스 제어](../role-based-access-control/custom-roles.md)를 참조하세요.

## <a name="creating-a-lab-custom-role-using-powershell"></a>PowerShell을 사용하여 랩 사용자 지정 역할 만들기
시작 하기 위해 해야 [Azure PowerShell 설치](/powershell/azure/install-az-ps)합니다. 

Azure PowerShell cmdlet을 설정한 후 다음 작업을 수행할 수 있습니다.

* 리소스 공급자에 대한 모든 작업 나열
* 특정 역할의 작업 나열
* 사용자 지정 역할 만들기

다음 PowerShell 스크립트는 이러한 작업을 수행하는 방법의 예제를 보여 줍니다.

    ‘List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>사용자 지정 역할을 사용하여 특정 정책에 대해 사용자에게 권한 할당
사용자 지정 역할을 정의하고 나면 사용자에게 이 역할을 할당할 수 있습니다. 사용자 지정 역할을 사용자에게 할당하려면 먼저 해당 사용자를 나타내는 **ObjectId** 를 가져와야 합니다. 이렇게 하려면 사용 합니다 **Get AzADUser** cmdlet.

다음 예제에서 **SomeUser** 사용자의 *ObjectId* 는 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3입니다.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

했으면 합니다 **ObjectId** 사용자 및 사용자 지정 역할 이름을 사용 하 여 사용자에 게 해당 역할을 할당할 수 있습니다는 **새로 만들기-AzRoleAssignment** cmdlet:

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

앞의 예제에서는 **AllowedVmSizesInLab** 정책이 사용되었습니다. 다음 정책 중 하나를 사용할 수 있습니다.

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
특정 랩 정책에 사용자 권한을 부여한 경우 다음 몇 가지 단계를 고려해야 합니다.

* [랩에 대한 보안 액세스](devtest-lab-add-devtest-user.md)
* [랩 정책 설정](devtest-lab-set-lab-policy.md)
* [랩 템플릿 만들기](devtest-lab-create-template.md)
* [VM에 대한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)
* [랩에 VM 추가](devtest-lab-add-vm.md)

