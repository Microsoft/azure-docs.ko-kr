---
title: 'Azure AD Connect: AD DS 커넥터 계정 권한 구성 | Microsoft Docs'
description: 이 문서에서는 새 ADSyncConfig PowerShell 모듈을 사용하여 AD DS 커넥터 계정을 구성하는 방법을 자세히 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce6f0e5e6e958e72ddc04608c5f207fedb18daf9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072267"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: AD DS 커넥터 계정 권한 구성 

PowerShell 모듈 [ADSyncConfig.psm1](reference-connect-adsyncconfig.md)은 2018년 8월에 릴리스된 빌드 1.1.880.0에서 도입되었습니다. 이 빌드에는 Azure AD Connect 배포의 올바른 Active Directory 권한을 구성하는 데 사용할 수 있는 cmdlet 컬렉션이 포함되어 있습니다. 

## <a name="overview"></a>개요 
Azure AD Connect에서 사용하기 위해 선택하는 각 기능에 대해, 다음 PowerShell cmdlet을 사용하여 AD DS 커넥터 계정의 Active Directory 권한을 설정할 수 있습니다. 문제를 방지하려면 사용자 지정 도메인 계정으로 Azure AD Connect를 설치하여 포리스트에 연결할 때마다 Active Directory 권한을 미리 준비해야 합니다. 이 ADSyncConfig 모듈은 Azure AD Connect가 배포된 후 권한을 구성하는 데도 사용할 수 있습니다.

![AD DS 계정 개요](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Azure AD Connect 기본 설치의 경우, 필요한 모든 권한을 사용하여 자동으로 생성된 계정(MSOL_nnnnnnnnnn)을 Active Directory에 만들므로 Azure AD와 동기화하려는 조직 구성 단위 또는 특정 Active Directory 개체에 대한 권한 상속을 차단하지 않은 경우에는 이 ADSyncConfig 모듈을 사용할 필요가 없습니다. 
 
### <a name="permissions-summary"></a>사용 권한 요약 
다음 표에서는 AD 개체에 필요한 권한을 요약하고 있습니다. 

| 기능 | 사용 권한 |
| --- | --- |
| ms-DS-ConsistencyGuid 기능 |[디자인 개념 - ms-DS-ConsistencyGuid를 sourceAnchor로 사용](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)에 설명된 ms-DS-ConsistencyGuid 특성에 대한 읽기 및 쓰기 권한 | 
| 암호 해시 동기화 |<li>디렉터리 변경 내용 복제</li>  <li>모든 디렉터리 변경 내용 복제 |
| Exchange 하이브리드 배포 |사용자, 그룹 및 연락처에 대한 [Exchange 하이브리드 쓰기 저장](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback)에 설명된 특성에 대한 읽기 및 쓰기 권한 |
| Exchange 메일 공용 폴더 |공용 폴더의 [Exchange Mail 공용 폴더](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder)에서 설명하는 특성에 대한 읽기 권한 | 
| 비밀번호 쓰기 저장 |사용자에 대한 [암호 관리 시작](../authentication/howto-sspr-writeback.md)에 설명된 특성에 대한 읽기 및 쓰기 권한 |
| 디바이스 쓰기 저장 |[디바이스 쓰기 저장](how-to-connect-device-writeback.md)에 설명된 디바이스 개체 및 컨테이너에 대한 읽기 및 쓰기 권한 |
| 그룹 쓰기 저장 |동기화된 **Office 365 그룹**에 대해 그룹 개체를 읽기, 만들기, 업데이트 및 삭제합니다.|

## <a name="using-the-adsyncconfig-powershell-module"></a>ADSyncConfig PowerShell 모듈 사용 
ADSyncConfig 모듈에는 AD DS PowerShell 모듈과 도구에 따라 달라지므로 [AD DS용 RSAT(원격 서버 관리 도구)](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools)가 필요합니다. AD DS용 RSAT를 설치하려면 '관리자 권한으로 실행'을 사용하여 Windows PowerShell 창을 열고 다음을 실행합니다. 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![구성](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>이미 AD DS용 RSAT가 설치된 도메인 컨트롤러에 **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** 파일을 복사하고, 여기에서 이 PowerShell 모듈을 사용할 수도 있습니다.

ADSyncConfig 사용을 시작하려면 Windows PowerShell 창에서 모듈을 로드해야 합니다. 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

이 모듈에 포함된 모든 cmdlet을 확인하려면 다음을 입력할 수 있습니다.  

``` powershell
Get-Command -Module AdSyncConfig  
```

![확인](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

각 cmdlet에는 AD DS 커넥터 계정 및 AdminSDHolder 스위치를 입력하는 동일한 매개 변수가 있습니다. AD DS 커넥터 계정을 지정하려면 계정 이름과 도메인 또는 계정 DN(고유 이름)만 제공할 수 있습니다.

예:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

또는

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

`<ADAccountName>`, `<ADDomainName>` 및 `<ADAccountDN>`을 사용자 환경에 적합한 값으로 바꿔야 합니다.

AdminSDHolder 컨테이너에 대한 권한을 수정하지 않으려면 `-SkipAdminSdHolders` 스위치를 사용합니다. 

기본적으로 모든 권한 설정 cmdlet은 포리스트에 있는 각 도메인의 루트에 AD DS 권한을 설정하려고 합니다. 즉, PowerShell 세션을 실행하는 사용자에게 포리스트의 각 도메인에 대한 도메인 관리자 권한이 필요합니다.  이 요구 사항에 따라 포리스트 루트에서 엔터프라이즈 관리자를 사용하는 것이 좋습니다. Azure AD Connect 배포에 여러 AD DS 커넥터가 있는 경우 AD DS 커넥터가 있는 각 포리스트에서 동일한 cmdlet을 실행해야 합니다. 

또한 권한을 설정하려는 대상 개체의 DN 뒤에 `-ADobjectDN` 매개 변수를 사용하여 특정 OU 또는 AD DS 개체에 대한 권한을 설정할 수도 있습니다. 대상 ADobjectDN을 사용하는 경우 cmdlet에서 이 개체에 대한 권한만 설정하며, 도메인 루트 또는 AdminSDHolder 컨테이너는 해당되지 않습니다. 이 매개 변수는 권한 상속을 사용하지 않도록 설정된 특정 OU 또는 AD DS 개체가 있는 경우 유용할 수 있습니다('권한 상속을 사용하지 않도록 설정된 AD DS 개체 찾기' 참조) 

이러한 일반적인 매개 변수에 대한 예외는 AD DS 커넥터 계정 자체에 대한 권한을 설정하는 데 사용되는 `Set-ADSyncRestrictedPermissions` cmdlet 및 암호 해시 동기화에 필요한 권한이 도메인 루트에서만 설정되어 `-ObjectDN` 또는 `-SkipAdminSdHolders` 매개 변수가 포함되지 않는 `Set-ADSyncPasswordHashSyncPermissions` cmdlet입니다.

### <a name="determine-your-ad-ds-connector-account"></a>AD DS 커넥터 계정 확인 
Azure AD Connect가 이미 설치되어 있고 Azure AD Connect에서 현재 사용하고 있는 AD DS 커넥터 계정을 확인하려면 다음 cmdlet을 실행할 수 있습니다. 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>권한 상속을 사용하지 않도록 설정된 AD DS 개체 찾기 
권한 상속을 사용하지 않도록 설정된 AD DS 개체가 있는지 확인하려는 경우 다음을 실행할 수 있습니다. 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
기본적으로 이 cmdlet은 상속을 사용하지 않도록 설정된 OU만 찾지만, 다음과 같이 `-ObjectClass` 매개 변수에 다른 AD DS 개체 클래스를 지정하거나 모든 개체 클래스에 '*'를 사용할 수 있습니다. 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>개체의 AD DS 권한 보기 
아래 cmdlet을 사용하여 DistinguishedName을 제공함으로써 현재 Active Directory 개체에 설정된 권한의 목록을 볼 수 있습니다. 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>AD DS 커넥터 계정 권한 구성 
 
### <a name="configure-basic-read-only-permissions"></a>기본 읽기 전용 권한 구성 
Azure AD Connect 기능을 사용하지 않는 경우 AD DS 커넥터 계정에 대한 기본 읽기 전용 권한을 설정하려면 다음을 실행합니다. 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


또는 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


이 cmdlet에서 설정하는 권한은 다음과 같습니다. 
 

|Type |속성 |액세스 권한 |적용 대상| 
|-----|-----|-----|-----|
|Allow |AD DS 커넥터 계정 |모든 속성 읽기 |하위 디바이스 개체| 
|Allow |AD DS 커넥터 계정|모든 속성 읽기 |하위 InetOrgPerson 개체| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기 |하위 Computer 개체| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기 |하위 foreignSecurityPrincipal 개체| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기 |하위 Group 개체| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기 |하위 User 개체| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기 |하위 Contact 개체| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>MS-DS-Consistency-Guid 권한 구성 
ms-Ds-Consistency-Guid 특성을 원본 앵커로 사용하는 경우 AD DS 커넥터 계정에 대한 권한을 설정하려면(즉, "Azure가 내 원본 앵커를 관리하도록 합니다." 옵션) 다음을 실행합니다. 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

또는 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

이 cmdlet에서 설정하는 권한은 다음과 같습니다. 

|Type |속성 |액세스 권한 |적용 대상|
|-----|-----|-----|-----| 
|Allow|AD DS 커넥터 계정|읽기/쓰기 속성|하위 User 개체|

### <a name="permissions-for-password-hash-synchronization"></a>암호 해시 동기화에 대한 권한 
암호 해시 동기화를 사용하는 경우 AD DS 커넥터 계정에 대한 권한을 설정하려면 다음을 실행합니다. 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


또는 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

이 cmdlet에서 설정하는 권한은 다음과 같습니다. 

|Type |속성 |액세스 권한 |적용 대상|
|-----|-----|-----|-----| 
|Allow |AD DS 커넥터 계정 |디렉터리 변경 내용 복제 |이 개체만(도메인 루트)| 
|Allow |AD DS 커넥터 계정 |모든 디렉터리 변경 내용 복제 |이 개체만(도메인 루트)| 
  
### <a name="permissions-for-password-writeback"></a>비밀번호 쓰기 저장에 대한 권한 
비밀번호 쓰기 저장을 사용하는 경우 AD DS 커넥터 계정에 대한 권한을 설정하려면 다음을 실행합니다. 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


또는

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
이 cmdlet에서 설정하는 권한은 다음과 같습니다. 

|Type |속성 |액세스 권한 |적용 대상|
|-----|-----|-----|-----| 
|Allow |AD DS 커넥터 계정 |암호 재설정 |하위 User 개체| 
|Allow |AD DS 커넥터 계정 |lockoutTime 속성 쓰기 |하위 User 개체| 
|Allow |AD DS 커넥터 계정 |pwdLastSet 속성 쓰기 |하위 User 개체| 

### <a name="permissions-for-group-writeback"></a>그룹 쓰기 저장에 대한 권한 
그룹 쓰기 저장을 사용하는 경우 AD DS 커넥터 계정에 대한 권한을 설정하려면 다음을 실행합니다. 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
또는 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
이 cmdlet에서 설정하는 권한은 다음과 같습니다. 

|Type |속성 |액세스 권한 |적용 대상|
|-----|-----|-----|-----| 
|Allow |AD DS 커넥터 계정 |일반 읽기/쓰기 |개체 형식 그룹 및 하위 개체의 모든 특성| 
|Allow |AD DS 커넥터 계정 |자식 개체 만들기/삭제 |개체 형식 그룹 및 하위 개체의 모든 특성| 
|Allow |AD DS 커넥터 계정 |삭제/트리 개체 삭제|개체 형식 그룹 및 하위 개체의 모든 특성|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Exchange 하이브리드 배포에 대한 권한 
Exchange 하이브리드 배포를 사용하는 경우 AD DS 커넥터 계정에 대한 권한을 설정하려면 다음을 실행합니다. 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


또는 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

이 cmdlet에서 설정하는 권한은 다음과 같습니다.  
 

|Type |속성 |액세스 권한 |적용 대상|
|-----|-----|-----|-----| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기/쓰기 |하위 User 개체| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기/쓰기 |하위 InetOrgPerson 개체| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기/쓰기 |하위 Group 개체| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기/쓰기 |하위 Contact 개체| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Exchange 메일 공용 폴더(미리 보기)에 대한 권한 
Exchange 메일 공용 폴더 기능을 사용하는 경우 AD DS 커넥터 계정에 대한 권한을 설정하려면 다음을 실행합니다. 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


또는 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
이 cmdlet에서 설정하는 권한은 다음과 같습니다. 

|Type |속성 |액세스 권한 |적용 대상|
|-----|-----|-----|-----| 
|Allow |AD DS 커넥터 계정 |모든 속성 읽기 |하위 PublicFolder 개체| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>AD DS 커넥터 계정에 대한 권한 제한 
이 PowerShell 스크립트는 매개 변수로 제공된 AD 커넥터 계정에 대한 권한을 강화합니다. 권한 강화에 포함되는 단계는 다음과 같습니다. 

- 지정된 개체에서 상속을 사용하지 않도록 설정합니다. 
- SELF와 관련된 기본 권한을 그대로 유지하려면 SELF와 관련된 ACE를 제외하고는 특정 개체의 ACE를 모두 제거합니다. 
 
  -ADConnectorAccountDN 매개 변수는 권한을 강화해야 하는 AD 계정입니다. 이는 일반적으로 AD DS 커넥터에서 구성된 MSOL_nnnnnnnnnnnn 도메인 계정입니다('AD DS 커넥터 계정 확인' 참조). -Credential 매개 변수는 대상 AD 개체에 대한 Active Directory 권한을 제한하는 데 필요한 권한이 있는 관리자 계정을 지정하는 데 필요합니다. 이는 일반적으로 엔터프라이즈 또는 도메인 관리자입니다.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
예를 들면 다음과 같습니다. 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

이 cmdlet에서 설정하는 권한은 다음과 같습니다. 

|Type |속성 |액세스 권한 |적용 대상|
|-----|-----|-----|-----| 
|Allow |SYSTEM |모든 권한 |이 개체 
|Allow |엔터프라이즈 관리자 |모든 권한 |이 개체 
|Allow |도메인 관리자 |모든 권한 |이 개체 
|Allow |관리자 |모든 권한 |이 개체 
|Allow |엔터프라이즈 도메인 컨트롤러 |내용 보기 |이 개체 
|Allow |엔터프라이즈 도메인 컨트롤러 |모든 속성 읽기 |이 개체 
|Allow |엔터프라이즈 도메인 컨트롤러 |읽기 권한 |이 개체 
|Allow |인증된 사용자 |내용 보기 |이 개체 
|Allow |인증된 사용자 |모든 속성 읽기 |이 개체 
|Allow |인증된 사용자 |읽기 권한 |이 개체 

## <a name="next-steps"></a>다음 단계
- [Azure AD Connect: 계정 및 사용 권한](reference-connect-accounts-permissions.md)
- [기본 설치](how-to-connect-install-express.md)
- [사용자 지정 설치](how-to-connect-install-custom.md)
- [ADSyncConfig 참조](reference-connect-adsyncconfig.md)

