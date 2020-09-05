---
title: PowerShell을 사용 하 여 그룹 설정 구성-Azure AD | Microsoft Docs
description: Azure Active Directory cmdlet을 사용하여 그룹 설정을 관리하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63f0c55823899be8eb4146860787aede2cd2d6b5
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797907"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>그룹 설정을 구성하는 Azure Active Directory cmdlets

이 문서에서는 Azure Active Directory(Azure AD) PowerShell cmdlet을 사용하여 그룹을 만들고 업데이트하는 방법을 설명합니다. 이 콘텐츠는 Microsoft 365 그룹 (통합 그룹이 라고도 함)에만 적용 됩니다.

> [!IMPORTANT]
> 일부 설정에는 Azure Active Directory Premium P1 라이선스가 필요합니다. 자세한 내용은 [템플릿 설정](#template-settings) 표를 참조하세요.

관리자가 아닌 사용자가 보안 그룹을 만들 수 없도록 하는 방법에 대 한 자세한 내용은  `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` [set-msolcompanysettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)에 설명 된 대로를 설정 하십시오.

Microsoft 365 그룹 설정은 Settings 개체 및 SettingsTemplate 개체를 사용 하 여 구성 됩니다. 처음에는 디렉터리가 기본 설정으로 구성되어 있으므로 디렉터리에 설정 개체가 표시되지 않습니다. 기본 설정을 변경하려면 설정 템플릿을 사용하여 새 설정 개체를 만들어야 합니다. 설정 템플릿은 Microsoft가 정의합니다. 여러 종류의 설정 템플릿이 있습니다. 디렉터리에 대 한 Microsoft 365 그룹 설정을 구성 하려면 "그룹 통합" 템플릿을 사용 합니다. 단일 그룹에 대 한 Microsoft 365 그룹 설정을 구성 하려면 "그룹별" 템플릿을 사용 합니다. 이 템플릿은 Microsoft 365 그룹에 대 한 게스트 액세스를 관리 하는 데 사용 됩니다. 

cmdlet은 Azure Active Directory PowerShell V2 모듈의 일부입니다. 컴퓨터에 모듈을 다운로드하여 설치하는 방법에 대한 지침은 [Azure Active Directory PowerShell 버전 2](/powershell/azure/active-directory/overview)를 참조하세요. 모듈의 버전 2 릴리스를 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAD/)에서 설치할 수 있습니다.

## <a name="install-powershell-cmdlets"></a>PowerShell cmdlet 설치

PowerShell 명령을 실행 하기 전에 Windows PowerShell 용 Azure Active Directory PowerShell for Graph 모듈의 모든 이전 버전을 제거 하 고 [그래프-공개 미리 보기 릴리스 (2.0.0.137 이후 버전) 용 powershell Azure Active Directory](https://www.powershellgallery.com/packages/AzureADPreview) 을 설치 해야 합니다.

1. 관리자로 Windows PowerShell 앱을 엽니다.
2. AzureADPreview의 이전 버전을 제거합니다.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. AzureADPreview의 최신 버전을 설치합니다.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 만들기
이러한 단계는 디렉터리 수준에서 설정을 만듭니다 .이 설정은 디렉터리에 있는 모든 Microsoft 365 그룹에 적용 됩니다. Get-AzureADDirectorySettingTemplate cmdlet은 [Graph에 대한 Azure AD PowerShell 미리 보기 모듈](https://www.powershellgallery.com/packages/AzureADPreview)에서만 지원됩니다.

1. DirectorySettings cmdlet에서 사용하려는 SettingsTemplate의 ID를 지정해야 합니다. 이 ID를 모르면 cmdlet이 모든 설정 템플릿 목록을 반환합니다.
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   이 cmdlet을 호출하면 사용할 수 있는 모든 템플릿이 반환됩니다.
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Microsoft 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. 사용 지침 URL을 추가하려면 사용 지침 URL 값을 정의하는 SettingsTemplate 개체를 가져와야 합니다. 즉, Group.Unified 템플릿입니다.
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. 다음에는 위 템플릿에 기초하여 새 설정 개체를 만듭니다.
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. 그런 다음 사용 지침 값을 업데이트합니다.
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. 그런 다음 설정을 적용 합니다.
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. 다음을 사용 하 여 값을 읽을 수 있습니다.

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 업데이트
설정 템플릿에서 UsageGuideLinesUrl에 대 한 값을 업데이트 하려면 Azure AD에서 현재 설정을 읽습니다. 그렇지 않으면 UsageGuideLinesUrl 이외의 기존 설정을 덮어쓸 수 있습니다.

1. 그룹에서 현재 설정을 가져옵니다. 통합 SettingsTemplate:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. 현재 설정을 확인 합니다.
   
   ```powershell
   $Setting.Values
   ```
   
   출력:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               false
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. UsageGuideLinesUrl의 값을 제거 하려면 URL을 빈 문자열로 편집 합니다.
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. 업데이트를 디렉터리에 저장 합니다.
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>템플릿 설정
다음은 Group.Unified 설정 템플릿에서 정의된 설정입니다. 달리 명시되지 않은 한 이 기능에는 Azure Active Directory Premium P1 라이선스가 필요합니다. 

| **설정** | **설명** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>형식: Boolean<li> 기본값: True |관리자가 아닌 사용자가 디렉터리에서 Microsoft 365 그룹을 만들 수 있는지 여부를 나타내는 플래그입니다. 이 설정에는 Azure Active Directory Premium P1 라이선스가 필요하지 않습니다.|
|  <ul><li>GroupCreationAllowedGroupId<li>유형: String<li>기본값: "" |EnableGroupCreation = = false 인 경우에도 구성원이 Microsoft 365 그룹을 만들 수 있는 보안 그룹의 GUID입니다. |
|  <ul><li>UsageGuidelinesUrl<li>유형: String<li>기본값: "" |그룹 사용 지침 링크입니다. |
|  <ul><li>ClassificationDescriptions<li>유형: String<li>기본값: "" | 쉼표로 구분된 분류 설명 목록입니다. ClassificationDescriptions 값은 다음 형식일 때만 유효합니다.<br>$setting ["ClassificationDescriptions"] = "분류: 설명, 분류: 설명"<br>여기서 분류가 ClassificationList 항목을 찾습니다.<br>EnableMIPLabels = = True 인 경우에는이 설정이 적용 되지 않습니다.|
|  <ul><li>DefaultClassification<li>유형: String<li>기본값: "" | 설정이 지정되지 않은 경우에 그룹의 기본 분류로 사용되는 분류입니다.<br>EnableMIPLabels = = True 인 경우에는이 설정이 적용 되지 않습니다.|
|  <ul><li>PrefixSuffixNamingRequirement<li>유형: String<li>기본값: "" | Microsoft 365 그룹에 대해 구성 된 명명 규칙을 정의 하는 최대 길이 64 자의 문자열입니다. 자세한 내용은 [Microsoft 365 그룹에 대 한 명명 정책 적용](groups-naming-policy.md)을 참조 하세요. |
| <ul><li>CustomBlockedWordsList<li>유형: String<li>기본값: "" | 사용자가 그룹 이름 또는 별칭에서 사용하도록 허용되지 않은 쉼표로 구분된 구의 문자열입니다. 자세한 내용은 [Microsoft 365 그룹에 대 한 명명 정책 적용](groups-naming-policy.md)을 참조 하세요. |
| <ul><li>EnableMSStandardBlockedWords<li>형식: Boolean<li>기본값: "False" | 사용 안 함
|  <ul><li>AllowGuestsToBeGroupOwner<li>형식: Boolean<li> 기본값: False | 게스트 사용자가 그룹의 소유자일 수 있는지 여부를 나타내는 부울 값입니다. |
|  <ul><li>AllowGuestsToAccessGroups<li>형식: Boolean<li> 기본값: True | 게스트 사용자가 Microsoft 365 그룹 콘텐츠에 액세스할 수 있는지 여부를 나타내는 부울입니다.  이 설정에는 Azure Active Directory Premium P1 라이선스가 필요하지 않습니다.|
|  <ul><li>GuestUsageGuidelinesUrl<li>유형: String<li>기본값: "" | 게스트 사용 지침의 링크 url입니다. |
|  <ul><li>AllowToAddGuests<li>형식: Boolean<li> 기본값: True | 이 디렉터리에 게스트를 추가하는 것이 허용되는지 여부를 나타내는 부울 값입니다. <br>이 설정은 *Enablemiplabels* 가 *True* 로 설정 되 고 게스트 정책이 해당 그룹에 할당 된 민감도 레이블과 연결 된 경우 재정의 될 수 있으며 읽기 전용으로 설정 됩니다.<br>조직 수준에서 AllowToAddGuests 설정이 False로 설정 된 경우 그룹 수준에서 AllowToAddGuests 설정이 무시 됩니다. 소수의 그룹에만 게스트 액세스를 사용 하려면 조직 수준에서 AllowToAddGuests를 true로 설정 하 고 특정 그룹에 대해 선택적으로 사용 하지 않도록 설정 해야 합니다. |
|  <ul><li>ClassificationList<li>유형: String<li>기본값: "" | Microsoft 365 그룹에 적용할 수 있는 유효한 분류 값을 쉼표로 구분한 목록입니다. <br>EnableMIPLabels = = True 인 경우에는이 설정이 적용 되지 않습니다.|
|  <ul><li>EnableMIPLabels<li>형식: Boolean<li>기본값: "False" |Microsoft 365 준수 센터에 게시 된 민감도 레이블을 Microsoft 365 그룹에 적용할 수 있는지 여부를 나타내는 플래그입니다. 자세한 내용은 [Microsoft 365 그룹의 민감도 레이블 할당](groups-assign-sensitivity-labels.md)을 참조 하세요. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>예: 디렉터리 수준에서 그룹에 대 한 게스트 정책 구성
1. 모든 설정 템플릿을 가져옵니다.
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. 디렉터리 수준에서 그룹에 대 한 게스트 정책을 설정 하려면 그룹이 필요 합니다. 통합 템플릿
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. 다음에는 위 템플릿에 기초하여 새 설정 개체를 만듭니다.
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. 그런 다음 AllowToAddGuests 설정을 업데이트 합니다.
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. 그런 다음 설정을 적용 합니다.
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. 다음을 사용 하 여 값을 읽을 수 있습니다.

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 읽기

검색할 설정의 이름을 알고 있는 경우 아래 cmdlet을 사용하여 현재 설정 값을 검색할 수 있습니다. 이 예제에서는 "UsageGuidelinesUrl"이라는 설정의 값을 검색합니다. 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
다음 단계는 디렉터리 수준에서 설정을 읽는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 그룹에 적용됩니다.

1. 모든 기존 디렉터리 설정 읽기:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   이 cmdlet은 모든 디렉터리 설정 목록을 반환합니다.
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. 특정 그룹의 모든 설정 읽기:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. 설정 ID GUID를 사용 하 여 특정 디렉터리 설정 개체의 모든 디렉터리 설정 값을 읽습니다.
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   이 cmdlet은 이 특정 그룹에 대한 이 설정 개체의 이름과 값을 반환합니다.
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 제거
다음 단계는 디렉터리 수준에서 설정을 제거하는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 그룹에 적용됩니다.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>특정 그룹에 대 한 설정 만들기

1. "Groups.Unified.Guest"라는 설정 템플릿 검색
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Microsoft 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Groups.Unified.Guest 템플릿에 대한 템플릿 개체를 검색합니다.
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. 템플릿으로 새로운 설정 개체를 만듭니다.
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. 필요한 값의 설정을 지정합니다.
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. 이 설정을 적용 하려는 그룹의 ID를 가져옵니다.
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. 디렉터리에 필요한 그룹의 새로운 설정을 만듭니다.
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. 설정을 확인 하려면 다음 명령을 실행 합니다.
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>특정 그룹의 설정 업데이트
1. 업데이트 하려는 설정이 있는 그룹의 ID 가져오기:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. 그룹의 설정을 검색 합니다.
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. 필요한 경우 그룹의 설정을 업데이트 합니다. 예를 들어
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. 그런 다음이 특정 그룹에 대 한 설정의 ID를 가져옵니다.
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   다음과 유사한 응답을 받게 됩니다.
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. 그런 다음이 설정에 대 한 새 값을 설정할 수 있습니다.
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. 설정이 올바르게 업데이트 되었는지 확인 하기 위해 설정 값을 읽을 수 있습니다.
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet 구문 참조
[Azure Active Directory Cmdlets](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)에서 더 많은 Azure Active Directory PowerShell 설명서를 찾을 수 있습니다.

## <a name="additional-reading"></a>추가 자료

* [Azure Active Directory 그룹을 사용 하 여 리소스에 대 한 액세스 관리](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md)