---
title: Azure AD Domain Services에 대한 범위 동기화 Microsoft Docs
description: Azure AD에서 Azure Active Directory Domain Services 관리 되는 도메인으로 범위 동기화를 구성 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: e6645a131766b7ec055ba1c8bb639f054f50c80b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704373"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Azure AD에서 Azure Active Directory Domain Services로 범위 동기화 구성

인증 서비스를 제공 하기 위해 Azure Active Directory Domain Services (Azure AD DS)는 Azure AD에서 사용자 및 그룹을 동기화 합니다. 하이브리드 환경에서 온-프레미스 Active Directory Domain Services (AD DS) 환경의 사용자와 그룹은 먼저 Azure AD Connect를 사용 하 여 Azure AD에 동기화 한 후 Azure AD DS로 동기화 할 수 있습니다.

기본적으로 Azure AD 디렉터리의 모든 사용자 및 그룹은 Azure AD DS 관리 되는 도메인에 동기화 됩니다. 특정 요구 사항이 있는 경우에는 정의 된 사용자 집합만 동기화 하도록 선택할 수 있습니다.

이 문서에서는 범위 지정 된 동기화를 사용 하는 Azure AD DS 관리 되는 도메인을 만든 다음 범위 지정 된 사용자 집합을 변경 하거나 사용 하지 않도록 설정 하는 방법을 보여 줍니다.

## <a name="scoped-synchronization-overview"></a>범위 동기화 개요

기본적으로 Azure AD 디렉터리의 모든 사용자 및 그룹은 Azure AD DS 관리 되는 도메인에 동기화 됩니다. 사용자가 관리 되는 도메인에만 액세스 해야 하는 경우에는 해당 사용자 계정만 동기화 할 수 있습니다. 이 범위 동기화는 그룹을 기반으로 합니다. 그룹 기반 범위 동기화를 구성 하는 경우 지정한 그룹에 속하는 사용자 계정만 Azure AD DS 관리 되는 도메인에 동기화 됩니다.

다음 표에서는 범위 동기화를 사용 하는 방법을 간략하게 설명 합니다.

| 현재 상태 | 필요한 상태 | 필요한 구성 |
| --- | --- | --- |
| 기존 관리 되는 도메인은 모든 사용자 계정 및 그룹을 동기화 하도록 구성 됩니다. | 특정 그룹에 속한 사용자 계정만 동기화 하려고 합니다. | 범위 동기화를 사용 하 여 모든 사용자를 동기화 하도록 변경할 수는 없습니다. [기존 관리 되는 도메인을 삭제](delete-aadds.md)한 다음이 문서의 단계에 따라 범위 동기화를 구성 하 여 Azure AD DS 관리 되는 도메인을 다시 만듭니다. |
| 기존 관리 되는 도메인이 없습니다. | 새 관리되는 도메인을 만들고 특정 그룹에 속한 사용자 계정만 동기화하려고 합니다. | 이 문서의 단계에 따라 범위 동기화를 구성 하 여 Azure AD DS 관리 되는 도메인을 만듭니다. |
| 기존 관리 되는 도메인은 특정 그룹에 속한 계정만 동기화 하도록 구성 됩니다. | 사용자가 Azure AD DS 관리 되는 도메인에 동기화 해야 하는 그룹 목록을 수정 하려고 합니다. | 이 문서의 단계에 따라 범위 동기화를 수정 합니다. |

Azure Portal 또는 PowerShell을 사용 하 여 범위 지정 동기화 설정을 구성 합니다.

| 실행력 | | |
|--|--|--|
| Azure AD DS 관리 되는 도메인을 만들고 범위 동기화를 구성 합니다. | [Azure 포털](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| 범위 동기화 수정 | [Azure 포털](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| 범위 동기화 사용 안 함 | [Azure 포털](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> 동기화 범위를 변경 하면 Azure AD DS 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다.
> 
>  * Azure AD DS 관리 되는 도메인에 대한 동기화 범위를 변경 하는 경우 전체 다시 동기화가 발생 합니다.
>  * Azure AD DS 관리 되는 도메인에서 더 이상 필요 하지 않은 개체는 삭제 됩니다. 새 개체는 관리되는 도메인에서 만들어집니다.
>  * 다시 동기화를 완료 하는 데 시간이 오래 걸릴 수 있습니다. 동기화 시간은 Azure AD DS 관리 되는 도메인 및 Azure AD 디렉터리의 사용자, 그룹 및 그룹 멤버 자격과 같은 개체의 수에 따라 달라 집니다. 수십만 개의 많은 개체가 있는 큰 디렉터리의 경우 다시 동기화에 며칠이 걸릴 수 있습니다.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Azure Portal를 사용 하 여 범위 동기화 사용

1. 자습서에 따라 [Azure AD DS 인스턴스를 만들고 구성](tutorial-create-instance-advanced.md)합니다. 동기화 범위 이외의 모든 필수 구성 요소 및 배포 단계를 완료 합니다.
1. 동기화 단계에서 **범위** 를 선택 하 고 azure AD DS 인스턴스에 동기화 할 azure AD 그룹을 선택 합니다.

Azure AD DS 관리 되는 도메인은 배포를 완료 하는 데 최대한 시간이 걸릴 수 있습니다. Azure Portal에서 Azure AD DS 관리 되는 도메인의 **개요** 페이지에는이 배포 단계 전체에서 현재 상태가 표시 됩니다.

Azure Portal Azure AD DS 관리 되는 도메인이 프로 비전을 완료 한 것으로 표시 되는 경우 다음 작업을 완료 해야 합니다.

* 가상 머신이 도메인 가입 또는 인증을 위해 관리되는 도메인을 찾을 수 있도록 가상 네트워크에 대한 DNS 설정을 업데이트합니다.
    * DNS를 구성 하려면 포털에서 Azure AD DS 관리 되는 도메인을 선택 합니다. **개요** 창에서 자동으로 이러한 DNS 설정을 구성 하 라는 메시지가 표시 됩니다.
* 최종 사용자가 회사 자격 증명을 사용 하 여 관리 되는 도메인에 로그인 할 수 있도록 [Azure AD Domain Services에 대해 암호 동기화를 사용 하도록 설정](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) 합니다.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Azure Portal를 사용 하 여 범위 동기화 수정

사용자가 Azure AD DS 관리 되는 도메인에 동기화 해야 하는 그룹 목록을 수정 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색 하 고 선택 합니다. *Aadds.contoso.com*와 같은 인스턴스를 선택 합니다.
1. 왼쪽의 메뉴에서 **동기화** 를 선택 합니다.
1. 그룹을 추가 하려면 위쪽에서 **+ 그룹을 선택** 하 고 추가할 그룹을 선택 합니다.
1. 동기화 범위에서 그룹을 제거 하려면 현재 동기화 된 그룹 목록에서 해당 그룹을 선택 하 고 **그룹 제거**를 선택 합니다.
1. 모든 변경이 수행 되 면 **동기화 범위 저장**을 선택 합니다.

동기화 범위를 변경 하면 Azure AD DS 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. Azure AD DS 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되 고 다시 동기화를 완료 하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Azure Portal를 사용 하 여 범위 동기화 사용 안 함

Azure AD DS 관리 되는 도메인에 대한 그룹 기반 범위 동기화를 사용 하지 않도록 설정 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색 하 고 선택 합니다. *Aadds.contoso.com*와 같은 인스턴스를 선택 합니다.
1. 왼쪽의 메뉴에서 **동기화** 를 선택 합니다.
1. 동기화 범위를 **범위** 에서 **모두**로 설정 하 고 **동기화 범위 저장**을 선택 합니다.

동기화 범위를 변경 하면 Azure AD DS 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. Azure AD DS 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되 고 다시 동기화를 완료 하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="powershell-script-for-scoped-synchronization"></a>범위가 지정 된 동기화를 위한 PowerShell 스크립트

PowerShell을 사용 하 여 범위 동기화를 구성 하려면 먼저 `Select-GroupsToSync.ps1`이라는 파일에 다음 스크립트를 저장 합니다. 이 스크립트는 azure AD에서 선택한 그룹을 동기화 하도록 Azure AD DS를 구성 합니다. 지정 된 그룹에 속하는 모든 사용자 계정은 Azure AD DS 관리 되는 도메인에 동기화 됩니다.

이 스크립트는이 문서의 추가 단계에서 사용 됩니다.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization-using-powershell"></a>PowerShell을 사용 하 여 범위 동기화 사용

PowerShell을 사용하여 이 단계 집합을 완료합니다. [PowerShell을 사용하여 Azure Active Directory Domain Services를 사용](powershell-create-instance.md)하도록 설정하려면 지침을 참조하세요. 이 문서의 몇 단계는 범위 동기화를 구성하기 위해 약간 수정되었습니다.

1. PowerShell을 사용 하 여 Azure AD DS를 사용 하도록 설정 하려면 문서에서 다음 작업을 완료 합니다. 관리 되는 도메인을 실제로 만드는 단계를 중지 합니다. 범위 동기화를 구성 하 여 Azure AD DS 관리 되는 도메인을 만듭니다.

   * [필요한 PowerShell 모듈을 설치](powershell-create-instance.md#prerequisites)합니다.
   * [관리 액세스를 위해 필요한 서비스 주체 및 AZURE AD 그룹을 만듭니다](powershell-create-instance.md#create-required-azure-ad-resources).
   * [가상 네트워크 및 서브넷과 같은 지원 Azure 리소스를 만듭니다](powershell-create-instance.md#create-supporting-azure-resources).

1. Azure AD에서 동기화 하려는 그룹 및 사용자가 포함 되어 있는지 확인 합니다. Azure AD DS와 동기화 할 그룹의 표시 이름 목록을 만듭니다.

1. [이전 섹션에서 스크립트](#powershell-script-for-scoped-synchronization) 를 실행 하 고 *-groupstoadd* 매개 변수를 사용 하 여 동기화 할 그룹 목록을 전달 합니다.

   > [!WARNING]
   > 범위 동기화에 대한 그룹 목록에 *AAD DC 관리자* 그룹을 포함 해야 합니다. 이 그룹을 포함 하지 않으면 Azure AD DS 관리 되는 도메인을 사용할 수 없습니다.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. 이제 Azure AD DS 관리 되는 도메인을 만들고 그룹 기반 범위 동기화를 사용 하도록 설정 합니다. *-Properties* 매개 변수에는 *"filteredSync" = "Enabled"* 를 포함 합니다.

    Azure 구독 ID를 설정 하 고 관리 되는 도메인의 이름 (예: *aadds.contoso.com*)을 입력 합니다. [AzSubscription][Get-AzSubscription] cmdlet을 사용 하 여 구독 ID를 가져올 수 있습니다. 리소스 그룹 이름, 가상 네트워크 이름 및 지역을 이전 단계에서 사용 된 값으로 설정 하 여 지원 되는 Azure 리소스를 만듭니다.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aadds.contoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

리소스를 만들고 PowerShell 프롬프트로 제어를 반환 하는 데 몇 분 정도 걸립니다. Azure AD DS 관리 되는 도메인은 백그라운드에서 계속 프로 비전 되 고 배포를 완료 하는 데 최대한 시간이 걸릴 수 있습니다. Azure Portal에서 Azure AD DS 관리 되는 도메인의 **개요** 페이지에는이 배포 단계 전체에서 현재 상태가 표시 됩니다.

Azure Portal Azure AD DS 관리 되는 도메인이 프로 비전을 완료 한 것으로 표시 되는 경우 다음 작업을 완료 해야 합니다.

* 가상 머신이 도메인 가입 또는 인증을 위해 관리되는 도메인을 찾을 수 있도록 가상 네트워크에 대한 DNS 설정을 업데이트합니다.
    * DNS를 구성 하려면 포털에서 Azure AD DS 관리 되는 도메인을 선택 합니다. **개요** 창에서 자동으로 이러한 DNS 설정을 구성 하 라는 메시지가 표시 됩니다.
* 가용성 영역를 지 원하는 지역에서 Azure AD DS 관리 되는 도메인을 만든 경우 네트워크 보안 그룹을 만들어 가상 네트워크에서 Azure AD DS 관리 되는 도메인에 대한 트래픽을 제한 합니다. 이러한 규칙을 적용 해야 하는 Azure 표준 부하 분산 장치가 생성 됩니다. 이 네트워크 보안 그룹은 Azure AD DS를 보호 하며 관리 되는 도메인이 제대로 작동 하는 데 필요 합니다.
    * 네트워크 보안 그룹 및 필요한 규칙을 만들려면 포털에서 Azure AD DS 관리 되는 도메인을 선택 합니다. **개요** 창에 네트워크 보안 그룹을 자동으로 만들고 구성 하 라는 메시지가 표시 됩니다.
* 최종 사용자가 회사 자격 증명을 사용 하 여 관리 되는 도메인에 로그인 할 수 있도록 [Azure AD Domain Services에 대해 암호 동기화를 사용 하도록 설정](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) 합니다.

## <a name="modify-scoped-synchronization-using-powershell"></a>Powershell을 사용 하 여 범위 동기화 수정

사용자가 Azure AD DS 관리 되는 도메인에 동기화 해야 하는 그룹 목록을 수정 하려면 [PowerShell 스크립트](#powershell-script-for-scoped-synchronization) 를 다시 실행 하 고 새 그룹 목록을 지정 합니다. 다음 예제에서는 동기화 할 그룹이 더 이상 *GroupName2*를 포함 하지 않으며 이제 *GroupName3*를 포함 합니다.

> [!WARNING]
> 범위 동기화에 대한 그룹 목록에 *AAD DC 관리자* 그룹을 포함 해야 합니다. 이 그룹을 포함 하지 않으면 Azure AD DS 관리 되는 도메인을 사용할 수 없습니다.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

동기화 범위를 변경 하면 Azure AD DS 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. Azure AD DS 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되 고 다시 동기화를 완료 하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="disable-scoped-synchronization-using-powershell"></a>PowerShell을 사용 하 여 범위 동기화 사용 안 함

Azure AD DS 관리 되는 도메인에 대한 그룹 기반 범위 동기화를 사용 하지 않도록 설정 하려면 Azure AD DS 리소스에서 *"filteredSync" = "Disabled"* 를 설정 하 고 관리 되는 도메인을 업데이트 합니다. 완료 되 면 모든 사용자 및 그룹이 Azure AD에서 동기화 되도록 설정 됩니다.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

동기화 범위를 변경 하면 Azure AD DS 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. Azure AD DS 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되 고 다시 동기화를 완료 하는 데 시간이 오래 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

동기화 프로세스에 대해 자세히 알아보려면 [Azure AD Domain Services의 동기화 이해](synchronization.md)를 참조 하세요.

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
