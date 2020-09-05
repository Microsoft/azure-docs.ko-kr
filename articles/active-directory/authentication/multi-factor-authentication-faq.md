---
title: Azure Multi-Factor Authentication FAQ-Azure Active Directory
description: Azure Multi-Factor Authentication과 관련된 질문과 대답입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4e09637a7d990e1555b5443b9931cf8ff8c271b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718869"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에 대한 질문과 대답

이 FAQ에서는 Azure Multi-factor Authentication 및 Multi-Factor Authentication 서비스 사용에 대한 일반적인 질문에 대한 답변을 제공합니다. 서비스 전반, 청구 모델, 사용자 경험 및 문제 해결에 대한 질문으로 세분화됩니다.

> [!IMPORTANT]
> Microsoft는 2019년 7월 1일부터 더 이상 새 배포를 위한 MFA 서버를 제공하지 않습니다. 신규 사용자의 다단계 인증이 필요한 고객은 클라우드 기반 Azure Multi-Factor Authentication을 사용해야 합니다. 7월 1일 이전에 MFA 서버를 활성화한 기존 고객은 종전과 같이 최신 버전 및 이후 업데이트를 다운로드하고 활성화 자격 증명을 생성할 수 있습니다.
>
> Azure Multi-Factor Authentication 서버와 관련 하 여 아래에서 공유 하는 정보는 이미 MFA 서버를 실행 하는 사용자 에게만 적용 됩니다.
>
> 2018년 9월 1일을 기준으로 새 고객은 소비 기반 라이선스를 더 이상 사용할 수 없습니다.
> 2018 년 9 월 1 일부 터 새 인증 공급자를 더 이상 만들 수 없습니다. 기존 인증 공급자는 계속 사용하고 업데이트할 수 있습니다. Multi-Factor Authentication은 Azure AD Premium 라이선스에서 계속 사용할 수 있게 지원됩니다.

## <a name="general"></a>일반

* [Azure Multi-Factor Authentication 서버 사용자 데이터를 어떻게 처리 하나요?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [SMS 메시지를 사용자에 게 보내는 데 사용 되는 SMS 짧은 코드는 무엇입니까?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Azure Multi-Factor Authentication 서버는 사용자 데이터를 어떻게 처리하나요?

Multi-Factor Authentication 서버에서 사용자 데이터는 온-프레미스 서버에만 저장 됩니다. 영구 사용자 데이터는 클라우드에 저장되지 않습니다. 사용자가 2단계 인증을 수행하는 경우 Multi-factor Authentication 서버는 인증을 위해 Azure Multi-factor Authentication 클라우드 서비스에 데이터를 보냅니다. Multi-factor Authentication 서버와 Multi-factor Authentication 클라우드 서비스 간의 통신에는 포트 443 아웃바운드를 통해 SSL(Secure Sockets Layer) 또는 TLS(전송 계층 보안)가 사용됩니다.

클라우드 서비스에 인증 요청을 보내는 경우 인증 및 사용 보고서를 위한 데이터를 수집합니다. 2 단계 인증 로그에 포함 되는 데이터 필드는 다음과 같습니다.

* **고유 ID** (사용자 이름 또는 온-프레미스 Multi-Factor Authentication 서버 ID)
* **First 및 Last Name** (선택 사항)
* **전자 메일 주소** (선택 사항)
* **전화 번호** (음성 통화 또는 SMS 인증을 수행할 때)
* **디바이스 토큰** (모바일 앱 인증을 수행할 때)
* **인증 모드**
* **인증 결과**
* **Multi-Factor Authentication 서버 이름**
* **Multi-Factor Authentication 서버 IP**
* **클라이언트 IP** (사용 가능한 경우)

Multi-Factor Authentication 서버에 선택적 필드를 구성할 수 있습니다.

인증 결과(성공 또는 거부) 및 거부 사유는 인증 데이터와 함께 저장됩니다. 이 데이터는 인증 및 사용 보고서에서 사용할 수 있습니다.

자세한 내용은 [Data 상주 and customer data For Azure Multi-Factor Authentication](concept-mfa-data-residency.md)를 참조 하세요.

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>사용자에게 SMS 메시지를 보낼 때 사용되는 SMS 짧은 코드는 무엇인가요?

미국에서는 다음과 같은 SMS 짧은 코드를 사용 합니다.

* *97671*
* *69829*
* *51789*
* *99399*

캐나다에서는 다음과 같은 SMS 짧은 코드를 사용 합니다.

* *759731*
* *673801*

동일한 번호를 사용 하 여 일관 된 SMS 또는 음성 기반 Multi-Factor Authentication 프롬프트를 전달 하는 것은 보장 되지 않습니다. 사용자를 위해 SMS 이행성을 향상하기 위한 조정 작업을 수시로 진행하면서 언제든지 짧은 코드를 추가하거나 제거할 수 있습니다.

미국 및 캐나다 외의 국가 또는 지역에 대해서는 짧은 코드를 지원 하지 않습니다.

## <a name="billing"></a>결제

[Multi-Factor Authentication 가격 책정 페이지](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 또는 [Azure Multi-Factor Authentication 버전 및 소비 계획](concept-mfa-licensing.md)에 대 한 설명서를 참조 하 여 대부분의 청구 관련 질문에 대답할 수 있습니다.

* [인증에 사용 되는 전화 통화 및 문자 메시지 전송에 대 한 요금이 조직에 청구 되나요?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [사용자 당 청구 모델은 사용 하도록 설정 된 모든 사용자 또는 2 단계 인증을 수행 하는 사용자에 대해 요금을 청구 하나요?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Multi-Factor Authentication 대금 청구는 어떻게 작동 하나요?](#how-does-multi-factor-authentication-billing-work)
* [Azure Multi-Factor Authentication의 무료 버전이 있나요?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [조직에서 사용자 당 및 인증 당 소비 청구 모델 간에 언제 든 지 전환할 수 있나요?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [조직에서 언제 든 지 사용량 기반 요금 청구 및 구독 (라이선스 기반 모델) 사이를 전환할 수 있나요?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [조직에서 Azure Multi-Factor Authentication를 사용 하기 위해 id를 사용 하 고 동기화 해야 하나요?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>인증하는 데 사용되는 전화 통화 및 문자 메시지에 대한 요금이 조직에 부과되나요?

아니요, Azure Multi-Factor Authentication를 통해 사용자에 게 전달 되는 개별 전화 통화 또는 문자 메시지에 대 한 요금이 청구 되지 않습니다. 인증 당 MFA 공급자를 사용 하는 경우 각 인증에 대 한 요금이 청구 되지만 사용 되는 방법에 대해서는 요금이 청구 되지 않습니다.

사용자는 자신의 개인 전화 서비스에 따라 수신한 전화 통화 또는 문자 메시지 수신에 대한 요금이 부과될 수 있습니다.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>사용자별 청구 모델에서는 활성화된 모든 사용자에 대해 요금이 청구되나요, 아니면 2단계 인증을 수행한 사용자에 대해서만 요금이 청구되나요?

대금 청구는 해당 월에 2단계 인증을 수행했는지 여부에 관계없이 Multi-Factor Authentication을 사용하도록 구성된 사용자 수를 기준으로 합니다.

### <a name="how-does-multi-factor-authentication-billing-work"></a>다단계 인증의 요금은 어떤 방식으로 청구되나요?

사용자 단위 또는 인증 단위 MFA 공급자를 만들 때 해당 조직의 Azure 구독이 사용량을 기준으로 매월 청구됩니다. 이 청구 모델은 Azure에서 가상 컴퓨터 사용 및 Web Apps에 대 한 요금 청구 방법과 비슷합니다.

Azure Multi-Factor Authentication에 대한 구독을 구매하는 경우 조직은 각 사용자에 대해 연간 라이선스 사용료만 지불합니다. MFA 라이선스 및 Office 365, Azure AD Premium 또는 Enterprise Mobility + Security 번들은 이 방법으로 청구됩니다.

자세한 내용은 [Azure Multi-Factor Authentication 획득 방법](concept-mfa-licensing.md)을 참조하세요.

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication의 무료 버전이 있나요?

보안 기본값은 Azure AD Free 계층에서 사용 하도록 설정할 수 있습니다. 보안 기본값을 사용 하 여 모든 사용자는 Microsoft Authenticator 앱을 사용 하 여 multi-factor authentication을 사용할 수 있습니다. 보안 기본값에는 문자 메시지 또는 전화 확인을 사용할 수 없으며 Microsoft Authenticator 앱만 사용할 수 있습니다.

자세한 내용은 [보안 기본값 이란?](../fundamentals/concept-fundamentals-security-defaults.md) 을 참조 하세요.

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>조직에서 사용자당 요금 청구 모델과 인증당 사용량 요금 청구 모델 간을 언제든지 전환할 수 있나요?

조직에서 사용량 기반 청구를 포함하는 독립 실행형 서비스로 MFA를 구입한 경우 MFA 공급자를 만들 때 청구 모델을 선택합니다. MFA 공급자를 만든 후에는 청구 모델을 변경할 수 없습니다. 

MFA 공급자가 Azure AD 테 넌 트에 연결 되어 *있지* 않거나 새 mfa 공급자를 다른 azure ad 테 넌 트에 연결 하는 경우 사용자 설정 및 구성 옵션이 전송 되지 않습니다. 또한 새 MFA 공급자를 통해 생성된 활성화 자격 증명을 사용하여 기존 Azure MFA 서버를 다시 활성화해야 합니다. MFA 서버를 새 MFA 공급자에 연결하기 위해 다시 활성화해도 전화 및 문자 메시지 인증에는 영향을 미치지 않지만 모바일 앱을 다시 활성화할 때까지 모바일 앱 알림이 모든 사용자에 대해 작동 중지됩니다.

[Azure Multi-Factor Auth 공급자 시작](concept-mfa-authprovider.md)에서 MFA 공급자에 대해 자세히 알아보세요.

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>조직에서 사용량 기반 요금 청구 모델과 구독(라이선스 기반 모델) 간을 언제든지 전환할 수 있나요?

일부 경우에 그렇습니다.

디렉터리에 *사용자당* Azure Multi-Factor Authentication 공급자가 있는 경우 MFA 라이선스를 추가할 수 있습니다. 라이선스가 있는 사용자는 사용자별 사용량 기반 청구 요금에 계산되지 않습니다. 라이선스가 없는 사용자는 계속해서 MFA 공급자를 통해 MFA 사용을 설정할 수 있습니다. Multi-Factor Authentication을 사용하도록 구성된 모든 사용자를 위한 라이선스를 구입 및 할당하면 Azure Multi-Factor Authentication 공급자를 삭제할 수 있습니다. 향후 라이선스보다 더 많은 사용자가 생기면 항상 다른 사용자 단위 MFA 공급자를 만들 수 있습니다.

디렉터리에 *인증 단위* Azure Multi-Factor Authentication 공급자가 있는 경우 MFA 공급자가 구독에 연결 되어 있기만 하면 항상 각 인증에 대해 요금이 청구 됩니다. 사용자에게 MFA 라이선스를 할당할 수 있지만 사용자의 MFA 라이선스 할당 여부에 관계없이 2단계 인증 요청마다 요금이 계속 청구됩니다.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication을 사용하려는 조직은 ID를 사용하고 동기화해야 하나요?

조직에서 사용량 기반 청구 모델을 사용하는 경우 Azure Active Directory는 선택 사항이며 필수가 아닙니다. MFA 공급자가 Azure AD 테 넌 트에 연결 되지 않은 경우 Azure Multi-Factor Authentication 서버 온-프레미스만 배포할 수 있습니다.

라이선스를 구입하고 디렉터리의 사용자에게 할당할 때 라이선스가 Azure AD 테넌트에 추가되기 때문에 라이선스 모델에 대해 Azure Active Directory가 필요합니다.

## <a name="manage-and-support-user-accounts"></a>사용자 계정 관리 및 지원

* [휴대폰에서 응답을 받지 못하는 경우 사용자에 게 알릴 내용](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [내 사용자 중 한 명에 게 계정으로 이동할 수 없는 경우 어떻게 해야 하나요?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [내 사용자 중 하나가 앱 암호를 사용 하는 휴대폰을 분실 한 경우 어떻게 해야 하나요?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [사용자가 비 브라우저 앱에 로그인 할 수 없는 경우는 어떻게 되나요?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [사용자가 문자 메시지를 수신 하지 않거나 확인 시간이 초과 되는 경우도 있습니다.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [시스템 시간이 초과 되기 전에 사용자가 문자 메시지에서 확인 코드를 입력 해야 하는 시간을 변경할 수 있나요?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Azure Multi-Factor Authentication 서버에서 하드웨어 토큰을 사용할 수 있나요?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Azure Multi-Factor Authentication 서버을 사용 하 여 터미널 서비스를 보호할 수 있나요?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [MFA 서버에서 호출자 ID를 구성 했지만 사용자가 익명 호출자의 Multi-Factor Authentication 호출을 계속 받습니다.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [사용자에 게 보안 정보를 등록 하 라는 메시지가 표시 되는 이유는 무엇 인가요?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>휴대폰에서 응답을 받지 못하는 경우 사용자에 게 알릴 내용

사용자가 5 분 내에 최대 5 번 시도 하 여 인증을 위해 전화 통화 또는 SMS를 받을 수 있도록 합니다. Microsoft는 여러 공급자를 통해 전화를 걸고 SMS 메시지를 전송합니다. 이 방법이 작동 하지 않는 경우 추가 문제 해결을 위해 지원 사례를 엽니다.

타사 보안 앱은 확인 코드 문자 메시지 또는 전화 통화를 차단할 수도 있습니다. 타사 보안 앱을 사용 하는 경우 보호를 사용 하지 않도록 설정 하 고 다른 MFA 확인 코드를 보내도록 요청 하세요.

위의 단계가 작동 하지 않는 경우 사용자에 게 둘 이상의 인증 방법을 구성 했는지 확인 합니다. 다시 로그인을 시도 하지만 로그인 페이지에서 다른 확인 방법을 선택 합니다.

자세한 내용은 [최종 사용자 문제 해결 가이드](../user-help/multi-factor-authentication-end-user-troubleshoot.md)를 참조 하세요.

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>사용자 한 명이 계정에 로그인할 수 없는 경우 어떻게 해야 하나요?

등록 프로세스를 다시 진행하도록 하여 사용자 계정을 재설정할 수 있습니다. [클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 및 디바이스 설정 관리](howto-mfa-userdevicesettings.md)에 대해 자세히 알아보세요.

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>사용자 한 명이 계정에 앱 암호를 사용하는 전화를 분실한 경우 어떻게 해야 하나요?

무단 액세스를 방지하려면 모든 사용자 앱 암호를 삭제합니다. 사용자에게 교체용 디바이스가 있는 경우 암호를 다시 만들 수 있습니다. [클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 및 디바이스 설정 관리](howto-mfa-userdevicesettings.md)에 대해 자세히 알아보세요.

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>사용자가 비브라우저 앱에 로그인할 수 없으면 어떻게 하나요?

조직에서 레거시 클라이언트를 계속 사용하고 [앱 암호 사용을 허용](howto-mfa-app-passwords.md)한 경우 사용자는 자신의 사용자 이름 및 암호로 이러한 레거시 클라이언트에 로그인할 수 없습니다. 대신, [앱 암호를 설정](../user-help/multi-factor-authentication-end-user-app-passwords.md)해야 합니다. 사용자는 로그인 정보를 지우고(삭제)하고 앱을 다시 시작한 후 일반 암호 대신 *앱 암호*와 사용자 이름을 사용하여 로그인해야 합니다.

조직에 레거시 클라이언트가 없는 경우 사용자가 앱 암호를 만들 수 있도록 허용 해서는 안 됩니다.

> [!NOTE]
> **Office 2013 클라이언트에 대한 최신 인증**
>
> 앱 암호는 최신 인증을 지원하는 않는 앱에만 필요합니다. Office 2013 클라이언트는 최신 인증 프로토콜을 지원하지만 구성되어야 합니다. 최신 인증은 Office 2013의 3 월 2015 이상 업데이트를 실행 하는 모든 고객에 게 제공 됩니다. 자세한 내용은 블로그 게시물 [업데이트 된 Office 365 최신 인증](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)을 참조 하세요.

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>사용자가 문자 메시지를 수신 하지 않거나 확인 시간이 초과 되는 경우도 있습니다.

서비스의 안정성에 영향을 줄 수 있는 제어할 수 없는 요인이 있으므로 SMS 메시지 배달이 보장 되지 않습니다. 이러한 요인에는 대상 국가 또는 지역, 휴대 전화 통신 업체 및 신호 강도가 포함 됩니다.

타사 보안 앱은 확인 코드 문자 메시지 또는 전화 통화를 차단할 수도 있습니다. 타사 보안 앱을 사용 하는 경우 보호를 사용 하지 않도록 설정 하 고 다른 MFA 확인 코드를 보내도록 요청 하세요.

사용자에 게 문자 메시지를 안정적으로 수신 하는 데 문제가 있는 경우 대신 Microsoft Authenticator 앱 또는 전화 통화 방법을 사용 하도록 지시 합니다. Microsoft Authenticator 셀룰러 및 Wi-fi 연결 모두에 대 한 알림을 받을 수 있습니다. 또한 디바이스에 신호가 전혀 없는 경우에도 모바일 앱은 인증 코드를 생성할 수 있습니다. Microsoft Authenticator 앱은 [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)및 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)에서 사용할 수 있습니다.

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>시스템 시간이 초과되기 전에 사용자가 문자 메시지를 통해 확인 코드를 입력해야 하는 시간을 변경할 수 있나요?

일부 경우에 가능합니다.

Azure MFA 서버 v7.0 이상을 사용하는 단방향 SMS의 경우 레지스트리 키를 설정하여 시간 제한 설정을 구성할 수 있습니다. MFA 클라우드 서비스가 텍스트 메시지를 보내면 확인 코드(또는 일회용 암호)가 MFA 서버에 반환됩니다. MFA 서버는 코드를 기본적으로 300초 동안 메모리에 저장합니다. 300초가 경과하기 전에 사용자가 코드를 입력하지 않으면 인증이 거부됩니다. 기본 시간 제한 설정을 변경하려면 다음 단계를 사용합니다.

1. `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`로 이동합니다.
2. *Pfsvc_pendingSmsTimeoutSeconds* 라는 **DWORD** 레지스트리 키를 만들고 Azure MFA 서버에서 일회성 암호을 저장 하는 시간 (초)을 설정 합니다.

>[!TIP]
>
> 여러 MFA 서버를 설정한 경우 원래 인증 요청을 처리하는 서버만 사용자에게 전송된 확인 코드를 알고 있습니다. 사용자가 코드를 입력하면 유효성을 검사할 인증 요청이 동일한 서버로 전송되어야 합니다. 코드 유효성 검사가 다른 서버로 전송되면 인증이 거부됩니다.

사용자가 정의된 시간 제한 기간 내 SMS에 응답하지 않는 경우 해당 인증이 거부됩니다.

클라우드에서 Azure MFA를 사용 하는 단방향 SMS의 경우 (AD FS 어댑터 또는 네트워크 정책 서버 확장 포함) 시간 제한 설정을 구성할 수 없습니다. Azure AD는 180초 동안 확인 코드를 저장합니다.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Azure Multi-factor Authentication 서버에서 하드웨어 토큰을 사용할 수 있나요?

Azure Multi-Factor Authentication 서버을 사용 하는 경우 타사 OATH (Open Authentication) 시간 기반, TOTP (일회용 암호) 토큰을 가져온 다음 2 단계 인증에 사용할 수 있습니다.

비밀 키를 CSV 파일에 추가하고 Azure Multi-factor Authentication 서버에 가져올 수 있는 경우 OATH TOTP 토큰에 해당하는 ActiveIdentity 토큰을 사용할 수 있습니다. OATH 토큰은 클라이언트 시스템에서 사용자 입력을 수락할 수 있는 한 ADFS(Active Directory Federation Services), IIS(Internet Information Server) 폼 기반 인증 및 RADIUS(Remote Authentication Dial-In User Service)에서 사용할 수 있습니다.

다음 형식으로 타사 OATH TOTP 토큰을 가져올 수 있습니다.  

- 휴대용 대칭 키 컨테이너(PSKC)
- 파일에 일련 번호, Base 32 형식인 암호 키 및 시간 간격이 있는 경우 CSV

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Azure Multi-factor Authentication 서버를 사용하여 터미널 서비스 보안을 유지할 수 있나요?

예. 그러나 Windows Server 2012 R2 이상을 사용 하는 경우 원격 데스크톱 게이트웨이 (RD 게이트웨이)를 사용 하 여 터미널 서비스만 보호할 수 있습니다.

Windows Server 2012 R2의 보안 변경 때문에 Azure Multi-Factor Authentication 서버가 Windows Server 2012 및 이전 버전에서 LSA(로컬 보안 기관) 보안 패키지에 연결하는 방식이 변경되었습니다. Windows 2012 이전의 터미널 서비스 버전의 경우 [Windows 인증으로 애플리케이션 보호](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)를 수행할 수 있습니다. Windows Server 2012 r 2를 사용 하는 경우 RD 게이트웨이 필요 합니다.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>MFA 서버에서 발신자 ID를 구성했는데 사용자에게 익명 발신자의 Multi-Factor Authentication 전화가 계속 수신됩니다.

경우에 따라 Multi-Factor Authentication 호출이 공용 전화망을 통해 이루어진 경우 발신자 번호를 지원하지 않는 통신 회사를 통해 라우팅됩니다. 이 통신 사업자 동작 때문에 Multi-Factor Authentication 시스템에서 항상 전송 하더라도 호출자 ID는 보장 되지 않습니다.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>사용자에게 보안 정보를 등록하라는 메시지가 표시되는 이유는 무엇인가요?

사용자에게 보안 정보를 등록하라는 메시지가 표시되는 데는 여러 가지 이유가 있습니다.

- Azure AD의 관리자가 MFA를 사용할 수 있도록 사용자를 설정했지만 사용자의 계정에 대한 보안 정보가 아직 등록되어 있지 않습니다.
- 사용자가 Azure AD에서 셀프 서비스 암호 재설정을 사용하도록 설정되었습니다. 보안 정보를 통해 향후 암호를 재설정(분실한 경우)할 수 있습니다.
- 사용자가 MFA를 요구 하는 조건부 액세스 정책이 있고 이전에 MFA에 등록 되지 않은 응용 프로그램에 액세스 했습니다.
- 사용자가 Azure AD (Azure AD 조인 포함)를 사용 하 여 장치를 등록 하는 중입니다. 조직에서 장치 등록을 위해 MFA를 요구 하지만, 사용자는 이전에 MFA에 등록 하지 않았습니다.
- 사용자가 Windows 10에서 비즈니스용 Windows Hello (MFA 필요)를 생성 하 고 MFA에 대해 이전에 등록 하지 않았습니다.
- 조직에서 사용자에게 적용된 MFA 등록 정책을 만들고 사용하도록 설정했습니다.
- 사용자는 MFA에 대해 이전에 등록되었지만 관리자가 사용하지 않도록 설정한 인증 방법을 선택했습니다. 따라서 사용자는 MFA 등록 과정을 다시 거쳐서 새로운 기본 인증 방법을 선택해야 합니다.

## <a name="errors"></a>오류

* [모바일 앱 알림을 사용 하는 경우 "활성화 된 계정에 대 한 인증 요청이 아닙니다." 오류 메시지가 표시 되 면 사용자는 어떻게 해야 하나요?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [비 브라우저 응용 프로그램에 로그인 할 때 0x800434D4L 오류 메시지가 표시 되 면 사용자는 어떻게 해야 하나요?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>모바일 앱 알림을 사용 하는 경우 "활성화 된 계정에 대 한 인증 요청이 아닙니다." 오류 메시지가 표시 되 면 사용자는 어떻게 해야 하나요?

사용자에 게 다음 절차를 완료 하 여 Microsoft Authenticator에서 해당 계정을 제거한 다음 다시 추가 합니다.

1. [Azure Portal 프로필](https://account.activedirectory.windowsazure.com/profile/) 로 이동 하 여 조직 계정으로 로그인 합니다.
2. **추가 보안 확인**을 선택 합니다.
3. Microsoft Authenticator 앱에서 기존 계정을 제거 합니다.
4. **구성**을 클릭 한 다음 지침에 따라 Microsoft Authenticator를 다시 구성 합니다.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>사용자가 비브라우저 애플리케이션에 로그인할 때 0x800434D4L 오류 메시지가 표시되면 어떻게 해야 하나요?

*0x800434D4L* 오류는 2 단계 인증이 필요한 계정에서 작동 하지 않는 로컬 컴퓨터에 설치 된 비 브라우저 응용 프로그램에 로그인 하려고 할 때 발생 합니다.

이 오류를 해결하려면 관리 관련 작업용 사용자 계정과 비관리 관련 작업용 사용자 계정을 따로 두어야 합니다. 나중에 관리 계정과 비관리 계정 간의 사서함을 연결하면 비관리 계정을 사용하여 Outlook에 로그인할 수 있습니다. 이 해결책에 대한 자세한 내용은 [관리자에게 사용자의 사서함 내용을 열고 보는 기능을 제공하는](https://help.outlook.com/141/gg709759.aspx?sl=1) 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계

여기에서 질문에 대답할 수 없는 경우 다음 지원 옵션을 사용할 수 있습니다.

* [Microsoft 지원 기술 자료](https://support.microsoft.com)에서 일반적인 기술 문제에 대한 솔루션을 검색합니다.
* 커뮤니티에서 기술 질문 및 답변을 검색 하 여 찾아보거나 [Azure Active Directory Q&A](/answers/topics/azure-active-directory.html)에서 질문 합니다.
* [Azure Multi-Factor Authentication 서버 지원을](https://support.microsoft.com/oas/default.aspx?prid=14947)통해 Microsoft 전문가에 게 문의 하세요. 문의하는 경우 가능한 문제에 대한 많은 정보를 제공해주시면 도움이 됩니다. 오류를 발견한 페이지, 특정 오류 코드, 특정 세션 ID 및 오류를 발견한 사용자의 ID를 포함하는 정보를 제공해 주시면 됩니다.
* 레거시 PhoneFactor 고객이 질문 하거나 암호 재설정에 대 한 도움이 필요한 경우 [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) 전자 메일 주소를 사용 하 여 지원 사례를 여세요.