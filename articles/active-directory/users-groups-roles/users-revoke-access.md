---
title: Azure Active Directory의 응급 상황에서 사용자 액세스 취소 | Microsoft Docs
description: Azure Active Directory에서 사용자에 대 한 모든 액세스를 취소 하는 방법
services: active-directory
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 07/15/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76a2e4cb170ca5bee41c0ae8f3e0557eab19562e
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147136"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Azure Active Directory에서 사용자 액세스 취소

관리자가 사용자에 대 한 모든 액세스를 취소 해야 하는 시나리오 중 손상 된 계정, 직원 종료 및 기타 참가자 위협이 포함 됩니다. 환경의 복잡도에 따라 관리자는 액세스가 취소 되도록 여러 단계를 수행할 수 있습니다. 일부 시나리오에서는 액세스 해지의 시작과 액세스가 실제로 취소 될 때까지 시간이 걸릴 수 있습니다.

위험을 완화 하려면 토큰이 작동 하는 방식을 이해 해야 합니다. 다음 섹션에 언급 된 패턴 중 하나에 해당 하는 다양 한 종류의 토큰이 있습니다.

## <a name="access-tokens-and-refresh-tokens"></a>액세스 토큰 및 새로 고침 토큰

액세스 토큰과 새로 고침 토큰은 굵은 클라이언트 응용 프로그램에서 자주 사용 되며, 단일 페이지 앱과 같은 브라우저 기반 응용 프로그램 에서도 사용 됩니다.

- 사용자가 Azure AD에 인증 하는 경우 사용자에 게 특정 리소스에 대 한 액세스 권한을 부여할 수 있는지 여부를 확인 하기 위해 권한 부여 정책이 평가 됩니다.  

- 권한이 부여 된 경우 Azure AD는 리소스에 대 한 액세스 토큰과 새로 고침 토큰을 발급 합니다.  

- 기본적으로 1 시간 동안 Azure AD에서 발급 한 액세스 토큰입니다. 인증 프로토콜에서 허용 하는 경우 액세스 토큰이 만료 되 면 앱에서 새로 고침 토큰을 Azure AD에 전달 하 여 사용자를 자동으로 다시 인증할 수 있습니다.

그런 다음 Azure AD는 해당 권한 부여 정책을 다시 평가 합니다. 사용자에 게 계속 권한이 부여 된 경우 Azure AD는 새 액세스 토큰과 새로 고침 토큰을 발급 합니다.

액세스 토큰은 토큰의 수명 보다 짧은 시간 내에 액세스를 취소 해야 하는 경우에 보안 문제가 될 수 있습니다. 일반적으로 약 1 시간입니다. 이러한 이유로 Microsoft는 거의 실시간으로 액세스 토큰을 무효화 하는 데 도움이 되는 Office 365 응용 프로그램에 [지속적으로 액세스를 평가](../conditional-access/concept-continuous-access-evaluation.md) 하는 데 적극적으로 노력 하 고 있습니다.  

## <a name="session-tokens-cookies"></a>세션 토큰 (쿠키)

대부분의 브라우저 기반 응용 프로그램은 액세스 및 새로 고침 토큰 대신 세션 토큰을 사용 합니다.  

- 사용자가 브라우저를 열고 Azure AD를 통해 응용 프로그램을 인증 하는 경우 사용자는 두 개의 세션 토큰을 받습니다. 하나는 Azure AD에서, 다른 하나는 응용 프로그램에서입니다.  

- 응용 프로그램이 자체 세션 토큰을 발급 하면 응용 프로그램에 대 한 액세스는 응용 프로그램의 세션에 의해 제어 됩니다. 이 시점에서 사용자는 응용 프로그램에서 인식 하는 권한 부여 정책에 의해서만 영향을 받습니다.

- Azure AD의 인증 정책은 응용 프로그램이 사용자를 Azure AD로 다시 보내는 경우에 자주 다시 평가 됩니다. 일반적으로 다시 평가는 응용 프로그램이 구성 된 방식에 따라 달라 지지만 자동으로 발생 합니다. 세션 토큰이 유효한 경우 앱이 사용자를 Azure AD로 다시 보내지 않을 수 있습니다.

- 세션 토큰이 해지 되려면 응용 프로그램은 자체 권한 부여 정책에 따라 액세스를 취소 해야 합니다. Azure AD는 응용 프로그램에서 발급 한 세션 토큰을 직접 해지할 수 없습니다.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>하이브리드 환경에서 사용자에 대 한 액세스 취소

온-프레미스 Active Directory Azure Active Directory와 동기화 된 하이브리드 환경의 경우 Microsoft는 IT 관리자가 다음 작업을 수행 하도록 권장 합니다.  

### <a name="on-premises-active-directory-environment"></a>온-프레미스 Active Directory 환경

Active Directory 관리자는 온-프레미스 네트워크에 연결 하 고 PowerShell을 연 후 다음 작업을 수행 합니다.

1. Active Directory에서 사용자를 사용 하지 않도록 설정 합니다. [사용 안 함-ADAccount](/powershell/module/addsadministration/disable-adaccount?view=win10-ps)를 참조 하세요.

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Active Directory에서 사용자의 암호를 두 번 다시 설정 합니다. [Set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps)를 참조 하세요.

    > [!NOTE]
    > 사용자 암호를 두 번 변경 하는 이유는 특히 온-프레미스 암호 복제에서 지연이 발생 하는 경우 해시 패스의 위험을 완화 하는 것입니다. 안전 하 게이 계정이 손상 되었다고 가정할 수 있는 경우 암호를 한 번만 다시 설정할 수 있습니다.

    > [!IMPORTANT] 
    > 다음 cmdlet에 예제 암호를 사용 하지 마세요. 암호를 임의의 문자열로 변경 해야 합니다.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory 환경

Azure Active Directory 관리자 권한으로 PowerShell을 열고를 실행 ``Connect-AzureAD`` 하 고 다음 작업을 수행 합니다.

1. Azure AD에서 사용자를 사용 하지 않도록 설정 합니다. [Get-azureaduser](/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0)를 참조 하세요.

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. 사용자의 Azure AD 새로 고침 토큰을 해지 합니다. [AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)를 참조 하세요.

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. 사용자의 장치를 사용 하지 않도록 설정 합니다. [AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0)를 참조 하세요.

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>선택적 단계

- [Intune 관리 응용 프로그램에서 회사 데이터를 초기화](/mem/intune/apps/apps-selective-wipe)합니다.

- [회사 소유의 장치 초기화 장치를 공장 기본 설정으로 다시 설정](/mem/intune/remote-actions/devices-wipe)합니다.

> [!NOTE]
> 초기화 후에는 장치의 데이터를 복구할 수 없습니다.

## <a name="when-access-is-revoked"></a>액세스가 취소 되는 경우

관리자가 위의 단계를 수행한 후에는 Azure Active Directory에 연결 된 응용 프로그램에 대 한 새 토큰을 얻을 수 없습니다. 응용 프로그램에서 액세스 권한을 부여 하는 방법에 따라 해지와 사용자의 액세스를 손실 하는 시간 사이의 경과 시간을 결정 합니다.

- 액세스 토큰을 **사용 하는 응용 프로그램**의 경우 액세스 토큰이 만료 되 면 사용자가 액세스 권한을 잃게 됩니다.

- **세션 토큰을 사용 하는 응용 프로그램**의 경우 기존 세션은 토큰이 만료 되는 즉시 종료 됩니다. 사용 하지 않도록 설정 된 사용자의 상태를 응용 프로그램에 동기화 하는 경우 응용 프로그램에서 사용자의 기존 세션을 자동으로 취소할 수 있습니다.  소요 시간은 응용 프로그램과 Azure AD 간의 동기화 빈도에 따라 달라 집니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 관리자에 대 한 보안 액세스 방법](directory-admin-roles-secure.md)
- [사용자 프로필 정보 추가 또는 업데이트](../fundamentals/active-directory-users-profile-azure-portal.md)