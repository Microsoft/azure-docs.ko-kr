---
title: Azure Multi-Factor Authentication에 대 한 앱 암호 구성-Azure Active Directory
description: Azure Multi-Factor Authentication에서 레거시 응용 프로그램에 대 한 앱 암호를 구성 하 고 사용 하는 방법을 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4e34c6f13f7013847e99a362716fc9c570cdaf
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224920"
---
# <a name="enable-and-use-azure-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>앱 암호를 사용 하 여 레거시 응용 프로그램에서 Azure Multi-Factor Authentication 사용 및 사용

Office 2010 이전 및 iOS 11 이전의 Apple 메일 등의 일부 응용 프로그램은 multi-factor authentication을 지원 하지 않습니다. 앱이 보조 형태의 인증 또는 프롬프트를 허용 하도록 구성 되지 않았습니다. 사용자 계정에 대해 사용 하도록 설정 된 Azure Multi-Factor Authentication를 사용 하 여 이러한 응용 프로그램을 안전 하 게 사용 하려면 앱 암호를 사용할 수 있습니다. 이러한 앱 암호는 기존 암호를 대체 하 여 앱이 multi-factor authentication을 바이패스 하 고 제대로 작동할 수 있도록 합니다.

Microsoft Office 2013 클라이언트 이상에 대해 최신 인증이 지원됩니다. Outlook을 포함 한 Office 2013 클라이언트는 최신 인증 프로토콜을 지원 하 고 2 단계 인증을 사용 하도록 설정할 수 있습니다. 클라이언트가 사용하도록 설정된 후에는 클라이언트에 대해 앱 암호가 필요하지 않습니다.

이 문서에서는 multi-factor authentication 프롬프트를 지원 하지 않는 레거시 응용 프로그램에 대해 앱 암호를 사용 하도록 설정 하 고 사용 하는 방법을 보여 줍니다.

>[!NOTE]
> 앱 암호는 multi-factor authentication 정책 및 최신 인증을 기반으로 조건부 액세스와 작동 하지 않습니다.

## <a name="overview-and-considerations"></a>개요 및 고려 사항

Azure Multi-Factor Authentication에 대 한 사용자 계정을 사용 하도록 설정 하면 추가 확인 요청에 의해 일반 로그인 프롬프트가 중단 됩니다. 일부 이전 응용 프로그램은 로그인 프로세스에서이 중단을 인식 하지 않으므로 인증이 실패 합니다. 사용자 계정 보안을 유지 하 고 Azure Multi-Factor Authentication 사용 하도록 설정 하려면 사용자의 일반 사용자 이름 및 암호 대신 앱 암호를 사용할 수 있습니다. 로그인 하는 동안 앱 암호를 사용 하는 경우 추가 확인 메시지가 표시 되지 않으므로 인증에 성공 합니다.

앱 암호가 자동으로 생성 되며 사용자가 지정 하지 않습니다. 이 자동으로 생성 된 암호를 사용 하면 공격자가 추측 하기 어렵고 더 안전 합니다. 사용자는 앱 암호를 응용 프로그램당 한 번만 입력 하면 암호를 추적 하거나 매번 입력할 필요가 없습니다.

앱 암호를 사용 하는 경우 다음 사항을 고려해 야 합니다.

* 사용자 당 앱 암호는 40 개로 제한 됩니다.
* 앱 암호는 회사 또는 학교 계정 외부에서 알려지지 않기 때문에 암호를 캐시 하 고 온-프레미스 시나리오에서 사용 하는 응용 프로그램은 실패할 수 있습니다. 이러한 시나리오의 예로 온-프레미스에 있지만 보관된 메일은 클라우드에 있는 Exchange 전자 메일이 있습니다. 이 시나리오에서 동일한 암호는 작동하지 않습니다.
* Azure Multi-Factor Authentication 사용자 계정에서 사용 하도록 설정 된 후에는 Outlook 및 Microsoft Skype for Business와 같은 대부분의 비 브라우저 클라이언트에서 앱 암호를 사용할 수 있습니다. 그러나 Windows PowerShell과 같은 비 브라우저 응용 프로그램을 통해 앱 암호를 사용 하 여 관리 작업을 수행할 수 없습니다. 이 작업은 사용자가 관리자 계정을 가지고 있는 경우에도 수행할 수 없습니다.
    * PowerShell 스크립트를 실행하려면 강력한 암호로 서비스 계정을 만들고 해당 계정에 2단계 인증을 사용하도록 설정하지 않습니다.
* 사용자 계정이 손상 된 것으로 의심 되는 경우 계정 암호를 해지/재설정 하면 앱 암호도 업데이트 해야 합니다. 사용자 계정 암호가 취소/다시 설정 되 면 앱 암호가 자동으로 해지 되지 않습니다. 사용자는 기존 앱 암호를 삭제 하 고 새 암호를 만들어야 합니다.
   * 자세한 내용은 [추가 보안 인증 페이지에서 앱 암호 만들기 및 삭제](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)를 참조 하세요.

>[!WARNING]
> 앱 암호는 클라이언트가 온-프레미스 및 클라우드 자동 검색 엔드포인트와 통신하는 하이브리드 환경에서는 작동하지 않습니다. 온-프레미스를 인증하려면 도메인 암호가 필요합니다. 클라우드를 통해 인증하려면 앱 암호가 필요합니다.

### <a name="app-password-names"></a>앱 암호 이름

앱 암호 이름은 해당 이름이 사용될 디바이스를 반영해야 합니다. Outlook, Word 및 Excel 등의 비 브라우저 애플리케이션이 있는 노트북을 가지고 있는 경우 이러한 앱에 대해 **Laptop**이라는 하나의 앱 암호를 만듭니다. 그런 다음, 데스크톱 컴퓨터에서 실행하는 동일한 애플리케이션에 대해 **Desktop**이라는 다른 앱 암호를 만듭니다.

응용 프로그램 별로 하나의 앱 암호 보다는 장치별로 하나의 앱 암호를 만드는 것이 좋습니다.

## <a name="federated-or-single-sign-on-app-passwords"></a>페더레이션된 또는 Single Sign-On 앱 암호

Azure AD는 온-프레미스 Active Directory Domain Services (AD DS)를 사용 하 여 페더레이션 또는 SSO (Single Sign-On)를 지원 합니다. 조직이 Azure AD를 사용 하 여 페더레이션 되 고 Azure Multi-Factor Authentication를 사용 하는 경우 다음과 같은 앱 암호 고려 사항이 적용 됩니다.

>[!NOTE]
> 다음 사항은 페더레이션된(SSO) 고객에게만 적용됩니다.

* Azure AD에서 앱 암호를 확인하기 때문에 페더레이션을 바이패스합니다. 앱 암호를 설정할 때만 페더레이션이 능동적으로 사용됩니다.
* 페더레이션된(SSO) 사용자의 경우 수동 흐름과 달리 ID 공급자(IdP)에 연결되지 않습니다. 앱 암호는 회사 또는 학교 계정에 저장됩니다. 사용자가 회사에서 떠나는 경우 사용자의 정보는 **DirSync**를 사용하여 실시간으로 회사 또는 학교 계정으로 흐릅니다. 계정 사용 안 함/삭제는 동기화 하는 데 최대 3 시간이 걸릴 수 있으며,이로 인해 Azure AD에서 앱 암호의 사용 안 함/삭제가 지연 될 수 있습니다.
* 앱 암호 기능을 사용할 경우 온-프레미스 클라이언트 Access Control 설정은 적용되지 않습니다.
* 온-프레미스 인증 로깅 또는 감사 기능은 앱 암호 기능과 함께 사용할 수 없습니다.

일부 고급 아키텍처에는 클라이언트와의 multi-factor authentication에 대 한 자격 증명 조합이 필요 합니다. 이러한 자격 증명은 회사 또는 학교 계정 사용자 이름과 암호 및 앱 암호를 포함할 수 있습니다. 요구 사항은 인증이 수행되는 방법에 따라 달라집니다. 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 회사 또는 학교 계정의 사용자 이름과 암호가 필요합니다. Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호가 필요합니다.

예를 들어 다음과 같은 아키텍처가 있다고 가정하겠습니다.

* Azure AD를 사용하여 Active Directory의 온-프레미스 인스턴스를 페더레이션합니다.
* Exchange online을 사용 합니다.
* 비즈니스용 Skype 온-프레미스를 사용 합니다.
* Azure Multi-Factor Authentication을 사용 합니다.

이 시나리오에서는 다음과 같은 자격 증명을 사용합니다.

* Skype for Business에 로그인하려면 회사 또는 학교 계정 사용자 이름 및 암호를 사용합니다.
* 온라인으로 Exchange에 연결하는 Outlook 클라이언트에서 주소록에 액세스하려면 앱 암호를 사용합니다.

## <a name="allow-users-to-create-app-passwords"></a>사용자가 앱 암호를 만들 수 있음

기본적으로 사용자가 앱 암호를 만들 수 없습니다. 앱 암호 기능은 사용자가 사용할 수 있도록 설정 해야 합니다. 사용자에 게 앱 암호를 만들 수 있도록 하려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**를 검색 하 고 선택한 다음 **사용자**를 선택 합니다.
3. *사용자* 창 위쪽의 탐색 모음에서 **Multi-Factor Authentication** 를 선택 합니다.
4. Multi-Factor Authentication 섹션 아래에서 **서비스 설정**을 선택합니다.
5. **서비스 설정** 페이지에서 **사용자가 비 브라우저 앱에 로그인하기 위해 앱 암호를 만들 수 있음** 옵션을 선택합니다.

    ![다단계 인증에 앱 암호의 사용자를 허용하는 서비스 설정을 보여 주는 Azure Portal의 스크린샷](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> 사용자가 앱 암호를 만들 수 있는 기능을 사용 하지 않도록 설정 하면 기존 앱 암호는 계속 작동 합니다. 그러나이 기능을 사용 하지 않도록 설정 하면 사용자는 기존 앱 암호를 관리 하거나 삭제할 수 없습니다.
>
> 앱 암호를 만드는 기능을 사용 하지 않도록 설정 하는 경우 [레거시 인증의 사용을 비활성화 하는 조건부 액세스 정책을 만드는](../conditional-access/block-legacy-authentication.md)것이 좋습니다. 이 방법은 기존 앱 암호가 작동 하지 않도록 하 고 최신 인증 방법을 강제로 사용 합니다.

## <a name="create-an-app-password"></a>앱 암호 만들기

사용자가 Azure Multi-Factor Authentication에 대 한 초기 등록을 완료 하면 등록 프로세스가 끝날 때 앱 암호를 만들 수 있는 옵션이 있습니다.

사용자는 등록 후 앱 암호를 만들 수도 있습니다. 사용자에 대한 자세한 내용과 세부 단계는 [Azure Multi-factor Authentication에서 앱 암호란?](../user-help/multi-factor-authentication-end-user-app-passwords.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

사용자가 Azure Multi-Factor Authentication에 빠르게 등록할 수 있도록 하는 방법에 대 한 자세한 내용은 [결합 된 보안 정보 등록 개요](concept-registration-mfa-sspr-combined.md)를 참조 하세요.
