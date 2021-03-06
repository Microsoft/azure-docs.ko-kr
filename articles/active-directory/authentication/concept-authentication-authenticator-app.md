---
title: Microsoft Authenticator 앱 인증 방법-Azure Active Directory
description: Azure Active Directory에서 Microsoft Authenticator 앱을 사용 하 여 로그인 이벤트를 개선 하 고 보안을 유지 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e33c87d53580d96363ba15bccbc889370f2479d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212912"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Azure Active Directory Microsoft Authenticator 앱의 인증 방법

Microsoft Authenticator 앱은 Azure AD 회사 또는 학교 계정 또는 Microsoft 계정에 대 한 추가 보안 수준을 제공 하며 [Android](https://go.microsoft.com/fwlink/?linkid=866594) 및 [iOS](https://go.microsoft.com/fwlink/?linkid=866594)에서 사용할 수 있습니다. Microsoft Authenticator 앱을 사용 하면 사용자가 로그인 중에 암호 없는 방식으로 인증 하거나 SSPR (셀프 서비스 암호 재설정) 또는 Azure AD Multi-Factor Authentication 이벤트 중에 추가 확인 옵션으로 인증할 수 있습니다.

사용자는 승인 또는 거부를 위해 모바일 앱을 통해 알림을 받거나, 인증자 앱을 사용 하 여 로그인 인터페이스에 입력할 수 있는 OAUTH 확인 코드를 생성할 수 있습니다. 알림 및 확인 코드를 모두 사용하는 경우 Authenticator 앱을 등록하는 사용자는 두 방법 중 하나를 사용하여 해당 ID를 확인할 수 있습니다.

사용자 이름 및 암호 조합이 아닌 로그인 프롬프트에서 Authenticator 앱을 사용 하려면 Microsoft Authenticator 앱을 사용 하 [여 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md)을 참조 하세요.

> [!NOTE]
> SSPR를 사용하도록 설정하는 경우 사용자에게 모바일 앱을 등록하는 옵션이 없습니다. 대신 사용자는 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에서 모바일 앱을 등록하거나 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)에서 결합된 보안 정보 등록의 일부로 등록할 수 있습니다.

## <a name="passwordless-sign-in"></a>Passwordless 로그인

사용자 이름을 입력 한 후 암호를 입력 하 라는 메시지가 표시 되는 대신, Microsoft Authenticator 앱에서 휴대폰 로그인을 사용 하도록 설정한 사용자는 앱에서 번호를 탭 하 라는 메시지를 표시 합니다. 올바른 번호를 선택 하면 로그인 프로세스가 완료 된 것입니다.

![사용자가 로그인을 승인 하도록 요청 하는 브라우저 로그인의 예](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

이 인증 방법은 높은 수준의 보안을 제공 하며 사용자가 로그인 할 때 암호를 제공 하지 않아도 됩니다. 

암호 없는 로그인을 시작 하려면 [Microsoft Authenticator 앱을 사용 하 여 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md)을 참조 하세요.

## <a name="notification-through-mobile-app"></a>모바일 앱을 통한 알림

Authenticator 앱을 사용하면 스마트폰 또는 태블릿에 알림을 표시하여 계정에 대한 무단 액세스를 방지하고 사기성 트랜잭션을 중지할 수 있습니다. 사용자가 알림을 확인하고 올바르면 **확인** 을 선택합니다. 그렇지 않으면 **거부** 를 선택할 수 있습니다.

![로그인 프로세스를 완료하기 위한 Authenticator 앱 알림의 웹 브라우저 프롬프트 예시 스크린샷](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 조직에서 근무 하는 직원이 있거나 중국으로 이동 하는 경우 Google play 서비스 (푸시 알림 포함)가 지역에서 차단 됨에 따라 Android 장치에서 *모바일 앱 메서드를 통한 알림이* 해당 국가/지역에서 작동 하지 않습니다. 그러나 iOS 알림이 작동 합니다. Android 장치의 경우 해당 사용자에 대해 대체 인증 방법을 사용할 수 있습니다.

## <a name="verification-code-from-mobile-app"></a>모바일 앱의 확인 코드

Authenticator 앱을 소프트웨어 토큰으로 사용하여 OATH 확인 코드를 생성할 수 있습니다. 사용자 이름 및 암호를 입력한 후 Authenticator 앱에서 제공한 코드를 로그인 인터페이스에 입력합니다. 확인 코드는 두 번째 인증 형식을 제공합니다.

사용자는 언제든지 사용할 수 있도록 구성된 Microsoft Authenticator 앱과 같은 인증자 애플리케이션 또는 최대 5개의 OATH 하드웨어 토큰을 조합할 수 있습니다.

> [!WARNING]
> 재설정에 한 가지 방법만 필요하고 셀프 서비스 암호 재설정을 사용하는 경우 최고 수준의 보안을 유지하기 위해 사용자에게 유일한 옵션으로 확인 코드만 제공됩니다.
>
> 두 방법이 필수인 경우 사용자가 사용하도록 설정한 다른 방법과 함께 알림 또는 확인 코드를 사용하여 재설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

암호 없는 로그인을 시작 하려면 [Microsoft Authenticator 앱을 사용 하 여 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md)을 참조 하세요.

[Microsoft Graph REST API 베타](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)를 사용 하 여 인증 방법 구성에 대해 자세히 알아보세요.
