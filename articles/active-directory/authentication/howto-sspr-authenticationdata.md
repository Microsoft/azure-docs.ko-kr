---
title: 셀프 서비스 암호 재설정에 대 한 연락처 정보 미리 채우기-Azure Active Directory
description: 사용자가 등록 프로세스를 완료 하지 않고도 기능을 사용할 수 있도록 SSPR (셀프 서비스 암호 재설정) Azure Active Directory 사용자에 대 한 연락처 정보를 미리 채우는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6fa1c14509a558c1f91d07bf9a73a4ecc39e1413
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068680"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Azure Active Directory 셀프 서비스 암호 재설정에 대 한 사용자 인증 연락처 정보 미리 채우기 (SSPR)

Azure Active Directory (Azure AD) 셀프 서비스 암호 재설정 (SSPR)을 사용 하려면 사용자에 대 한 인증 연락처 정보가 있어야 합니다. 일부 조직에서는 사용자가 자신의 인증 데이터를 등록 합니다. 다른 조직에서는 Active Directory Domain Services (AD DS)에 이미 있는 인증 데이터에서 동기화 하는 것을 선호 합니다. 이 동기화 된 데이터는 사용자 조작이 필요 없이 Azure AD 및 SSPR에서 사용할 수 있게 됩니다. 사용자가 암호를 변경 하거나 다시 설정 해야 하는 경우 이전에 연락처 정보를 등록 하지 않은 경우에도이 작업을 수행할 수 있습니다.

다음 요구 사항을 충족 하는 경우 인증 연락처 정보를 미리 채울 수 있습니다.

* 온-프레미스 디렉터리에서 데이터의 형식을 올바르게 지정 했습니다.
* Azure AD 테 넌 트에 대 한 [Azure AD Connect](../hybrid/how-to-connect-install-express.md) 를 구성 했습니다.

전화 번호는 + *1 4251234567*와 같은 *+ CountryCode PhoneNumber*형식 이어야 합니다.

> [!NOTE]
> 국가 코드와 전화 번호 사이에 공백이 있어야 합니다.
>
> 암호 재설정은 내선 번호를 지원하지 않습니다. *+1 4251234567X12345* 형식에서도 전화를 걸기 전에 내선 번호가 제거됩니다.

## <a name="fields-populated"></a>채워진 필드

Azure AD Connect에서 기본 설정을 사용 하는 경우 SSPR에 대 한 인증 연락처 정보를 채우도록 다음 매핑이 수행 됩니다.

| 온-프레미스 Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | 사무실 전화 |
| mobile                       | 휴대폰 |

사용자가 휴대폰 번호를 확인 한 후에 Azure AD의 **인증 연락처 정보** 에 있는 *전화* 필드도 해당 숫자로 채워집니다.

## <a name="authentication-contact-info"></a>인증 연락처 정보

Azure Portal에서 Azure AD 사용자에 대 한 **인증 방법** 페이지에서 전역 관리자는 다음 예제 스크린샷에 표시 된 것 처럼 인증 연락처 정보를 수동으로 설정할 수 있습니다.

![Azure AD의 사용자에 대 한 인증 연락처 정보][Contact]

이 인증 연락처 정보에는 다음 고려 사항이 적용 됩니다.

* *휴대폰* 필드가 채워지고 SSPR 정책에서 *휴대폰* 필드가 사용 하도록 설정 된 경우 사용자는 암호 재설정 등록 페이지 및 암호 재설정 워크플로 중에 해당 번호를 확인 합니다.
* *대체 전화* 필드는 암호 재설정에 사용 되지 않습니다.
* *전자 메일* 필드가 채워지고 SSPR 정책에서 *전자 메일* 을 사용 하도록 설정 하면 사용자가 암호 재설정 등록 페이지 및 암호 재설정 워크플로 중에 해당 전자 메일을 볼 수 있습니다.
* *대체 전자 메일* 필드가 채워지고 SSPR 정책에서 *전자 메일* 을 사용 하는 경우 사용자는 암호 재설정 등록 페이지에 해당 전자 메일이 표시 되지 않지만 암호 재설정 워크플로 중에 해당 전자 메일이 표시 됩니다.

## <a name="security-questions-and-answers"></a>보안 질문 및 답변

본인 확인 질문과 답변은 Azure AD 테넌트에 안전하게 저장되며 사용자가 [SSPR 등록 포털](https://aka.ms/ssprsetup)을 통해서만 액세스할 수 있습니다. 관리자는 다른 사용자의 질문과 답변 내용을 보거나, 설정하거나, 수정할 수 없습니다.

## <a name="what-happens-when-a-user-registers"></a>사용자가 등록하면 어떻게 되나요?

사용자가 등록하면 등록 페이지에서 다음 필드를 설정합니다.

* **인증 전화**
* **인증 전자 메일**
* **보안 질문 및 답변**

*휴대폰* 또는 *대체 전자 메일*에 대 한 값을 제공 하는 경우 사용자는 서비스에 등록 하지 않은 경우에도 해당 값을 사용 하 여 암호를 다시 설정할 수 있습니다.

또한 사용자는 처음으로 등록할 때 해당 값을 확인 하 고 원하는 경우 수정할 수 있습니다. 등록을 완료 한 후에는 *인증 전화* 및 *인증 전자 메일* 필드에 각각 해당 값이 유지 됩니다.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>PowerShell을 사용하여 인증 데이터 설정 및 읽기

PowerShell을 사용하여 다음 필드를 설정할 수 있습니다.

* *대체 전자 메일*
* *휴대폰*
* *사무실 전화*
    * 온-프레미스 디렉터리와 동기화 하지 않는 경우에만 설정할 수 있습니다.

> [!IMPORTANT]
> PowerShell v1과 PowerShell v 2의 명령 기능에는 알려진 패리티 부족이 있습니다. [인증 방법에 대 한 Microsoft Graph REST API (베타)](/graph/api/resources/authenticationmethods-overview) 는 최신 상호 작용을 제공 하는 현재 엔지니어링 포커스입니다.

### <a name="use-powershell-version-1"></a>PowerShell 버전 1 사용

시작 하려면 [AZURE AD PowerShell 모듈을 다운로드](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule)하 여 설치 합니다. 설치한 후에는 다음 단계를 사용 하 여 각 필드를 구성 합니다.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>PowerShell 버전 1을 사용하여 인증 데이터 설정

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>PowerShell 버전 1을 사용하여 인증 데이터 읽기

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>인증 전화 및 인증 메일 옵션 읽기

PowerShell 버전 1에서 아래 명령을 사용하여 **인증 전화**와 **인증 메일**을 읽습니다.

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>PowerShell 버전 2 사용

시작 하려면 [AZURE AD 버전 2 PowerShell 모듈을 다운로드](/powershell/module/azuread/?view=azureadps-2.0)하 여 설치 합니다.

를 지 원하는 최신 버전의 PowerShell에서 빠르게 설치 하려면 `Install-Module` 다음 명령을 실행 합니다. 첫 번째 줄은 모듈이 이미 설치 되어 있는지 확인 합니다.

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

모듈을 설치한 후에는 다음 단계를 사용 하 여 각 필드를 구성 합니다.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>PowerShell 버전 2를 사용하여 인증 데이터 설정

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>PowerShell 버전 2를 사용하여 인증 데이터 읽기

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>다음 단계

인증 연락처 정보가 사용자에 대해 미리 채워져 있으면 다음 자습서를 완료 하 여 셀프 서비스 암호 재설정을 사용 하도록 설정 합니다.

> [!div class="nextstepaction"]
> [Azure AD 셀프 서비스 암호 재설정 사용](tutorial-enable-sspr.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "전역 관리자는 사용자의 인증 연락처 정보를 수정할 수 있습니다."
