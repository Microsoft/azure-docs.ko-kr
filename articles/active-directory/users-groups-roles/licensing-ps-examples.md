---
title: 그룹 라이선스에 대 한 PowerShell 및 그래프 예제-Azure AD | Microsoft Docs
description: Azure Active Directory 그룹 기반 라이선스에 대 한 PowerShell + 그래프 예제 및 시나리오
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: how-to
ms.workload: identity
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 230ccb3d10c7ba6f3abcac9d83309fd7fa3c5c3f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797686"
---
# <a name="powershell-and-graph-examples-for-group-based-licensing-in-azure-ad"></a>Azure AD의 그룹 기반 라이선스에 대 한 PowerShell 및 그래프 예제

[Azure Portal](https://portal.azure.com)를 통해 그룹 기반 라이선스의 전체 기능을 사용할 수 있으며 현재 PowerShell 및 Microsoft Graph 지원은 읽기 전용 작업으로 제한 됩니다. 그러나 기존 [MSOnline PowerShell cmdlet](/powershell/module/msonline) 및 Microsoft Graph를 사용하여 수행할 수 있는 몇 가지 유용한 작업이 있습니다. 이 문서는 가능한 작업에 대한 예제를 제공합니다.

> [!NOTE]
> Cmdlet 실행을 시작 하기 전에 cmdlet을 실행 하 여 조직에 먼저 연결 해야 합니다 `Connect-MsolService`   .

> [!WARNING]
> 이 코드는 데모 용도의 예로 제공됩니다. 사용자 환경에서 사용 하려는 경우 먼저 작은 규모에서 테스트 하거나 별도의 테스트 조직에서 테스트 해 보세요. 환경의 특정 요구에 맞게 코드를 조정해야 할 수도 있습니다.

## <a name="view-product-licenses-assigned-to-a-group"></a>그룹에 할당된 제품 라이선스 보기

[Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0) cmdlet은 그룹 개체를 검색하고 *라이선스* 속성을 확인하는 데 사용됩니다. 그룹에 현재 할당된 모든 제품 라이선스를 나열합니다.

```powershell
(Get-MsolGroup -ObjectId 99c4216a-56de-42c4-a4ac-e411cd8c7c41).Licenses
| Select SkuPartNumber
```
출력:
```
SkuPartNumber
-------------
ENTERPRISEPREMIUM
EMSPREMIUM
```

> [!NOTE]
> 데이터는 제품(SKU) 정보로 제한됩니다. 라이선스에 사용하지 않도록 설정된 서비스 계획은 나열할 수 없습니다.

다음 샘플을 사용 하 여 Microsoft Graph에서 동일한 데이터를 가져올 수 있습니다.

```
GET https://graph.microsoft.com/v1.0/groups/99c4216a-56de-42c4-a4ac-e411cd8c7c41?$select=assignedLicenses
```
출력:
```
HTTP/1.1 200 OK
{
  "value": [
{
  "assignedLicenses": [
     {
          "accountId":"f1b45b40-57df-41f7-9596-7f2313883635",
          "skuId":"c7df2760-2c81-4ef7-b578-5b5392b571df",
      "disabledPlans":[]
     },
     {
          "accountId":"f1b45b40-57df-41f7-9596-7f2313883635",
          "skuId":" b05e124f-c7cc-45a0-a6aa-8cf78c946968",
      "disabledPlans":[]
     },
  ],
}
  ]
}
```

## <a name="get-all-groups-with-licenses"></a>라이선스가 있는 모든 그룹 가져오기

다음 명령을 실행하면 라이선스가 할당되어 있는 모든 그룹을 찾을 수 있습니다.
```powershell
Get-MsolGroup -All | Where {$_.Licenses}
```
어떤 제품이 할당되어 있는지에 대해 자세한 정보를 표시할 수 있습니다.
```powershell
Get-MsolGroup -All | Where {$_.Licenses} | Select `
    ObjectId, `
    DisplayName, `
    @{Name="Licenses";Expression={$_.Licenses | Select -ExpandProperty SkuPartNumber}}
```

출력:
```
ObjectId                             DisplayName              Licenses
--------                             -----------              --------
7023a314-6148-4d7b-b33f-6c775572879a EMS E5 – Licensed users  EMSPREMIUM
cf41f428-3b45-490b-b69f-a349c8a4c38e PowerBi - Licensed users POWER\_BI\_STANDARD
962f7189-59d9-4a29-983f-556ae56f19a5 O365 E3 - Licensed users ENTERPRISEPACK
c2652d63-9161-439b-b74e-fcd8228a7074 EMSandOffice             {ENTERPRISEPREMIUM,EMSPREMIUM}
```

## <a name="get-statistics-for-groups-with-licenses"></a>라이선스가 있는 그룹에 대한 통계 가져오기
라이선스가 있는 그룹에 대해 기본적인 통계를 보고할 수 있습니다. 아래 예제에서 스크립트는 총 사용자 수, 그룹별 라이선스가 이미 할당되어 있는 사용자 수, 그룹별 라이선스가 할당될 수 없는 사용자 수를 나열합니다.

```powershell
#get all groups with licenses
Get-MsolGroup -All | Where {$_.Licenses}  | Foreach {
    $groupId = $_.ObjectId;
    $groupName = $_.DisplayName;
    $groupLicenses = $_.Licenses | Select -ExpandProperty SkuPartNumber
    $totalCount = 0;
    $licenseAssignedCount = 0;
    $licenseErrorCount = 0;

    Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full info about each user in the group
    Get-MsolUser -ObjectId {$_.ObjectId} |     Foreach {
        $user = $_;
        $totalCount++

        #check if any licenses are assigned via this group
        if($user.Licenses | ? {$_.GroupsAssigningLicense -ieq $groupId })
        {
            $licenseAssignedCount++
        }
        #check if user has any licenses that failed to be assigned from this group
        if ($user.IndirectLicenseErrors | ? {$_.ReferencedObjectId -ieq $groupId })
        {
            $licenseErrorCount++
        }     
    }

    #aggregate results for this group
    New-Object Object |
                    Add-Member -NotePropertyName GroupName -NotePropertyValue $groupName -PassThru |
                    Add-Member -NotePropertyName GroupId -NotePropertyValue $groupId -PassThru |
                    Add-Member -NotePropertyName GroupLicenses -NotePropertyValue $groupLicenses -PassThru |
                    Add-Member -NotePropertyName TotalUserCount -NotePropertyValue $totalCount -PassThru |
                    Add-Member -NotePropertyName LicensedUserCount -NotePropertyValue $licenseAssignedCount -PassThru |
                    Add-Member -NotePropertyName LicenseErrorCount -NotePropertyValue $licenseErrorCount -PassThru

    } | Format-Table
```


출력:
```
GroupName         GroupId                              GroupLicenses       TotalUserCount LicensedUserCount LicenseErrorCount
---------         -------                              -------------       -------------- ----------------- -----------------
Dynamics Licen... 9160c903-9f91-4597-8f79-22b6c47eafbf AAD_PREMIUM_P2                   0                 0                 0
O365 E5 - base... 055dcca3-fb75-4398-a1b8-f8c6f4c24e65 ENTERPRISEPREMIUM                2                 2                 0
O365 E5 - extr... 6b14a1fe-c3a9-4786-9ee4-3a2bb54dcb8e ENTERPRISEPREMIUM                3                 3                 0
EMS E5 - all s... 7023a314-6148-4d7b-b33f-6c775572879a EMSPREMIUM                       2                 2                 0
PowerBi - Lice... cf41f428-3b45-490b-b69f-a349c8a4c38e POWER_BI_STANDARD                2                 2                 0
O365 E3 - all ... 962f7189-59d9-4a29-983f-556ae56f19a5 ENTERPRISEPACK                   2                 2                 0
O365 E5 - EXO     102fb8f4-bbe7-462b-83ff-2145e7cdd6ed ENTERPRISEPREMIUM                1                 1                 0
Access to Offi... 11151866-5419-4d93-9141-0603bbf78b42 STANDARDPACK                     4                 3                 1
```

## <a name="get-all-groups-with-license-errors"></a>라이선스 오류가 있는 모든 그룹 가져오기
라이선스가 할당될 수 없는 사용자가 포함된 그룹을 찾으려면:
```powershell
Get-MsolGroup -All -HasLicenseErrorsOnly $true
```
출력:
```
ObjectId                             DisplayName             GroupType Description
--------                             -----------             --------- -----------
11151866-5419-4d93-9141-0603bbf78b42 Access to Office 365 E1 Security  Users who should have E1 licenses
```
다음을 사용하여 Microsoft Graph에서 동일한 데이터 가져오기
```
GET https://graph.microsoft.com/v1.0/groups?$filter=hasMembersWithLicenseErrors+eq+true
```
출력:
```
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.Group",
      "objectType": "Group",
      "id": "11151866-5419-4d93-9141-0603bbf78b42",
      ... # other group properties.
    },
    {
      "odata.type": "Microsoft.DirectoryServices.Group",
      "objectType": "Group",
      "id": "c57cdc98-0dcd-4f90-a82f-c911b288bab9",
      ...
    },
    ... # other groups with license errors.
  ]
"odata.nextLink":"https://graph.microsoft.com/v1.0/ groups?$filter=hasMembersWithLicenseErrors+eq+true&$skipToken=<encodedPageToken>"
}
```


## <a name="get-all-users-with-license-errors-in-a-group"></a>그룹에서 라이선스 오류가 있는 모든 사용자 가져오기

라이선스 관련 오류가 포함된 그룹이 있는 경우 오류의 영향을 받는 모든 사용자를 나열할 수 있습니다. 사용자는 다른 그룹의 오류도 가져올 수 있습니다. 하지만 이 예제에서는 사용자에 대한 **IndirectLicenseError** 항목의 **ReferencedObjectId** 속성을 확인하여 요청된 그룹과 관련된 오류만으로 결과를 제한합니다.

```powershell
#a sample group with errors
$groupId = '11151866-5419-4d93-9141-0603bbf78b42'

#get all user members of the group
Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full information about user objects
    Get-MsolUser -ObjectId {$_.ObjectId} |
    #filter out users without license errors and users with license errors from other groups
    Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId} |
    #display id, name and error detail. Note: we are filtering out license errors from other groups
    Select ObjectId, `
           DisplayName, `
           @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId} | Select -ExpandProperty Error}}
```

출력:

```powershell
ObjectId                             DisplayName      License Error
--------                             -----------      ------------
6d325baf-22b7-46fa-a2fc-a2500613ca15 Catherine Gibson MutuallyExclusiveViolation
```

다음을 사용 하 여 Microsoft Graph에서 동일한 데이터를 가져옵니다.

```powershell
GET https://graph.microsoft.com/v1.0/groups/11151866-5419-4d93-9141-0603bbf78b42/membersWithLicenseErrors
```

출력:
```powershell
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.User",
      "objectType": "User",
      "id": "6d325baf-22b7-46fa-a2fc-a2500613ca15",
      ... # other user properties.
    },
    ... # other users.
  ],
  "odata.nextLink":"https://graph.microsoft.com/v1.0/groups/11151866-5419-4d93-9141-0603bbf78b42/membersWithLicenseErrors?$skipToken=<encodedPageToken>"
}

```

## <a name="get-all-users-with-license-errors-in-the-entire-organization"></a>전체 조직에서 라이선스 오류가 있는 모든 사용자 가져오기

다음 스크립트는 하나 이상의 그룹에서 라이선스 오류가 있는 모든 사용자를 가져오는 데 사용할 수 있습니다. 이 스크립트는 사용자당, 라이선스 오류당 한 행을 출력하기 때문에 각 오류의 소스를 명확하게 식별할 수 있습니다.

> [!NOTE]
> 이 스크립트는 조직에 있는 모든 사용자를 열거 하며,이는 대기업에는 적합 하지 않을 수 있습니다.

```powershell
Get-MsolUser -All | Where {$_.IndirectLicenseErrors } | % {   
    $user = $_;
    $user.IndirectLicenseErrors | % {
            New-Object Object |
                Add-Member -NotePropertyName UserName -NotePropertyValue $user.DisplayName -PassThru |
                Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                Add-Member -NotePropertyName GroupId -NotePropertyValue $_.ReferencedObjectId -PassThru |
                Add-Member -NotePropertyName LicenseError -NotePropertyValue $_.Error -PassThru
        }
    }  
```

출력:

```powershell
UserName         UserId                               GroupId                              LicenseError
--------         ------                               -------                              ------------
Anna Bergman     0d0fd16d-872d-4e87-b0fb-83c610db12bc 7946137d-b00d-4336-975e-b1b81b0666d0 MutuallyExclusiveViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 f2503e79-0edc-4253-8bed-3e158366466b CountViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 11151866-5419-4d93-9141-0603bbf78b42 MutuallyExclusiveViolation
Drew Fogarty     f2af28fc-db0b-4909-873d-ddd2ab1fd58c 1ebd5028-6092-41d0-9668-129a3c471332 MutuallyExclusiveViolation
```

다음은 라이선스 오류가 포함된 그룹만 검색하는 스크립트의 다른 버전입니다. 문제가 있는 그룹이 많지 않을 것으로 예상되는 시나리오에 더 적합할 수 있습니다.

```powershell
$groupIds = Get-MsolGroup -All -HasLicenseErrorsOnly $true
    foreach ($groupId in $groupIds) {
    Get-MsolGroupMember -All -GroupObjectId $groupId.ObjectID |
        Get-MsolUser -ObjectId {$_.ObjectId} |
        Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId.ObjectID} |
        Select DisplayName, `
               ObjectId, `
               @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId.ObjectID} | Select -ExpandProperty Error}}
 
    } 
``` 

## <a name="check-if-user-license-is-assigned-directly-or-inherited-from-a-group"></a>사용자 라이선스가 직접 할당되었는지 또는 그룹에서 상속되었는지 확인

사용자 개체의 경우 특정 제품 라이선스가 그룹으로부터 할당되었는지 또는 직접 할당되었는지 확인하는 것이 가능합니다.

아래 두 가지 샘플 함수를 사용하여 개별 사용자에 대한 할당 유형을 분석할 수 있습니다.

```powershell
#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
            if ($license.GroupsAssigningLicense.Count -eq 0)
            {
                return $true
            }

            #If the collection contains the ID of the user object, this means the license is assigned directly
            #Note: the license may also be assigned through one or more groups in addition to being assigned directly
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                if ($assignmentSource -ieq $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a group
function UserHasLicenseAssignedFromGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                #Note: the license may also be assigned directly in addition to being inherited
                if ($assignmentSource -ine $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
```

이 스크립트는 SKU ID를 입력으로 사용 하 여 조직의 각 사용자에 대해 해당 함수를 실행 합니다 .이 예제에서는 조직에서 ID *contoso: EMS*로 표시 되는 *Enterprise Mobility + Security*에 대 한 라이선스에 관심이 있습니다.

```powershell
#the license SKU we are interested in. use Get-MsolAccountSku to see a list of all identifiers in your organization
$skuId = "contoso:EMS"

#find all users that have the SKU license assigned
Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
    ObjectId, `
    @{Name="SkuId";Expression={$skuId}}, `
    @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
    @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
```

출력:

```powershell
ObjectId                             SkuId       AssignedDirectly AssignedFromGroup
--------                             -----       ---------------- -----------------
157870f6-e050-4b3c-ad5e-0f0a377c8f4d contoso:EMS             True             False
1f3174e2-ee9d-49e9-b917-e8d84650f895 contoso:EMS            False              True
240622ac-b9b8-4d50-94e2-dad19a3bf4b5 contoso:EMS             True              True
```

그래프에는 결과를 표시 하는 간단한 방법이 없지만이 API에서 볼 수 있습니다.

```powershell
GET https://graph.microsoft.com/v1.0/users/e61ff361-5baf-41f0-b2fd-380a6a5e406a?$select=licenseAssignmentStates
```

출력:

```powershell
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.User",
      "objectType": "User",
      "id": "e61ff361-5baf-41f0-b2fd-380a6a5e406a",
      "licenseAssignmentState":[
        {
          "skuId": "157870f6-e050-4b3c-ad5e-0f0a377c8f4d",
          "disabledPlans":[],
          "assignedByGroup": null, # assigned directly.
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "1f3174e2-ee9d-49e9-b917-e8d84650f895",
          "disabledPlans":[],
          "assignedByGroup": "e61ff361-5baf-41f0-b2fd-380a6a5e406a", # assigned by this group.
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "240622ac-b9b8-4d50-94e2-dad19a3bf4b5", 
          "disabledPlans":[
            "e61ff361-5baf-41f0-b2fd-380a6a5e406a"
          ],
          "assignedByGroup": "e61ff361-5baf-41f0-b2fd-380a6a5e406a",
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "240622ac-b9b8-4d50-94e2-dad19a3bf4b5",
          "disabledPlans":[],
          "assignedByGroup": null, # It is the same license as the previous one. It means the license is assigned directly once and inherited from group as well.
          "state": " Active ",
          "error": " None"
        }
      ],
      ...
    }
  ],
}

```

## <a name="remove-direct-licenses-for-users-with-group-licenses"></a>그룹 라이선스가 있는 사용자에 대한 직접 라이선스 제거

이 스크립트의 용도는 동일한 라이선스를 그룹에서 이미 상속한(예: [그룹 기반 라이선스로 전환](./licensing-groups-migrate-users.md)의 일환으로) 사용자로부터 불필요한 직접 라이선스를 제거하는 것입니다.
> [!NOTE]
> 우선 제거할 직접 라이선스가 상속된 라이선스보다 더 많은 서비스 기능을 사용하지 않는지 검사하는 것이 중요합니다. 그렇지 않을 경우 직접 라이선스를 제거하면 사용자가 서비스 및 데이터를 액세스할 수 없게 될 수 있습니다. 현재는 어떤 서비스가 상속된 라이선스를 통해 사용되고 어떤 서비스가 직접 라이선스를 통해 사용되는지 PowerShell을 통해 확인할 수 없습니다. 스크립트에서는 우리가 아는 최소 수준의 서비스가 그룹에서 상속되는 것으로 지정하고 사용자가 예기치 않게 서비스 액세스 권한을 상실하지 않는지 확인합니다.

```powershell
#BEGIN: Helper functions used by the script

#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
        if ($license.GroupsAssigningLicense.Count -eq 0)
        {
            return $true
        }

        #If the collection contains the ID of the user object, this means the license is assigned directly
        #Note: the license may also be assigned through one or more groups in addition to being assigned directly
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            if ($assignmentSource -ieq $user.ObjectId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a specific group
function UserHasLicenseAssignedFromThisGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
            #Note: the license may also be assigned directly in addition to being inherited
            if ($assignmentSource -ieq $groupId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}

#Returns the license object corresponding to the skuId. Returns NULL if not found
function GetUserLicense
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)
    #we look for the specific license SKU in all licenses assigned to the user
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

#produces a list of disabled service plan names for a set of plans we want to leave enabled
function GetDisabledPlansForSKU
{
    Param([string]$skuId, [string[]]$enabledPlans)

    $allPlans = Get-MsolAccountSku | where {$_.AccountSkuId -ieq $skuId} | Select -ExpandProperty ServiceStatus | Where {$_.ProvisioningStatus -ine "PendingActivation" -and $_.ServicePlan.TargetClass -ieq "User"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName
    $disabledPlans = $allPlans | Where {$enabledPlans -inotcontains $_}

    return $disabledPlans
}

function GetUnexpectedEnabledPlansForUser
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [string[]]$expectedDisabledPlans)

    $license = GetUserLicense $user $skuId

    $extraPlans = @();

    if($license -ne $null)
    {
        $userDisabledPlans = $license.ServiceStatus | where {$_.ProvisioningStatus -ieq "Disabled"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName

        $extraPlans = $expectedDisabledPlans | where {$userDisabledPlans -notcontains $_}
    }
    return $extraPlans
}
#END: helper functions

#BEGIN: executing the script
#the group to be processed
$groupId = "48ca647b-7e4d-41e5-aa66-40cab1e19101"

#license to be removed - Office 365 E3
$skuId = "contoso:ENTERPRISEPACK"

#minimum set of service plans we know are inherited from groups - we want to make sure that there aren't any users who have more services enabled
#which could mean that they may lose access after we remove direct licenses
$servicePlansFromGroups = ("EXCHANGE_S_ENTERPRISE", "SHAREPOINTENTERPRISE", "OFFICESUBSCRIPTION")

$expectedDisabledPlans = GetDisabledPlansForSKU $skuId $servicePlansFromGroups

#process all members in the group and get full info about each user in the group looping through group members. 
Get-MsolGroupMember -All -GroupObjectId $groupId | Get-MsolUser -ObjectId {$_.ObjectId} | Foreach {
        $user = $_;
        $operationResult = "";

        #check if Direct license exists on the user
        if (UserHasLicenseAssignedDirectly $user $skuId)
        {
            #check if the license is assigned from this group, as expected
            if (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
            {
                #check if there are any extra plans we didn't expect - we are being extra careful not to remove unexpected services
                $extraPlans = GetUnexpectedEnabledPlansForUser $user $skuId $expectedDisabledPlans
                if ($extraPlans.Count -gt 0)
                {
                    $operationResult = "User has extra plans that may be lost - license removal was skipped. Extra plans: $extraPlans"
                }
                else
                {
                    #remove the direct license from user
                    Set-MsolUserLicense -ObjectId $user.ObjectId -RemoveLicenses $skuId
                    $operationResult = "Removed direct license from user."   
                }

            }
            else
            {
                $operationResult = "User does not inherit this license from this group. License removal was skipped."
            }
        }
        else
        {
            $operationResult = "User has no direct license to remove. Skipping."
        }

        #format output
        New-Object Object |
                    Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                    Add-Member -NotePropertyName OperationResult -NotePropertyValue $operationResult -PassThru
    } | Format-Table
#END: executing the script
```

출력:

```powershell
UserId                               OperationResult
------                               ---------------
7c7f860f-700a-462a-826c-f50633931362 Removed direct license from user.
0ddacdd5-0364-477d-9e4b-07eb6cdbc8ea User has extra plans that may be lost - license removal was skipped. Extra plans: SHAREPOINTWAC
aadbe4da-c4b5-4d84-800a-9400f31d7371 User has no direct license to remove. Skipping.
```
> [!NOTE]
> `$skuId` `$groupId`   위의 스크립트를 실행 하기 전에 테스트 환경에 따라 직접 라이선스 제거를 대상으로 하는 변수에 대 한 값을 업데이트 하십시오. 

## <a name="next-steps"></a>다음 단계

그룹의 라이선스를 관리할 수 있는 기능 집합에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Active Directory의 그룹 기반 라이선스란?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory에서 그룹에 라이선스 할당](licensing-groups-assign.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](licensing-groups-resolve-problems.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](licensing-groups-migrate-users.md)
* [Azure Active Directory에서 그룹 기반 라이선스를 사용하여 제품 라이선스 간에 사용자를 마이그레이션하는 방법](../users-groups-roles/licensing-groups-change-licenses.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](licensing-group-advanced.md)
* [Azure Active Directory의 그룹 기반 라이선싱에 대한 PowerShell 예제](../users-groups-roles/licensing-ps-examples.md)