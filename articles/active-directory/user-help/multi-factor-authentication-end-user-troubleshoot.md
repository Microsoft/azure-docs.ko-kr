---
title: 계정 2단계 인증의 일반적인 문제 - Azure AD
description: 몇 가지 일반적인 2단계 인증 문제들과 회사 또는 학교 계정에 대한 솔루션입니다.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/20/2020
ms.author: curtand
ms.reviewer: kexia
metadata ms.custom: contperfq1
ms.openlocfilehash: 61ba06054e86d544ae4ab1edbdef8d86667ef3b2
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796581"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>2단계 인증 및 회사 또는 학교 계정에 대한 일반적인 문제

Azure Active Directory (Azure AD) 조직은 2 단계 인증 (2FV)을 켤 수 있습니다. 원하는 것 보다 더 자주 발생 하는 것 처럼 보이는 몇 가지 일반적인 2FV 문제가 있습니다. 가장 일반적인 문제에 대 한 수정 사항을 설명 하기 위해이 문서를 함께 제공 합니다.

2 개의 FV를 사용할 때 계정 로그인에는 다음 데이터의 조합이 필요 합니다.

- 사용자 이름
- 사용자 암호
- 모바일 장치 또는 전화

2 fv는 사용자가 알고 있는 것과 사용자가 _알고_ _있는_것을 필요로 하기 때문에 암호 보다 안전 합니다. 실제 전화가 있는 해커는 없습니다.

>[!Important]
>관리자의 경우, [Azure AD 문서](../index.yml)에서 Azure AD 환경을 설정하고 관리하는 방법에 대한 자세한 정보를 확인할 수 있습니다.

이 콘텐츠는 조직에서 사용자에 게 제공 하는 계정인 회사 또는 학교 계정을 지원 하기 위한 것입니다 (예: dritan@contoso.com ). 개인 Microsoft 계정 (예:)에 대 한 2 단계 확인에 문제가 있는 경우 danielle@outlook.com [Microsoft 계정에 대해 2 단계 인증 설정 또는 해제](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조 하세요.

## <a name="i-dont-have-my-mobile-device-with-me"></a>내 모바일 디바이스가 없습니다.

문제가 발생합니다. 회사에서 모바일 장치를 사용 하 고 있으므로 이제 휴대폰을 사용 하 여 사용자가 누구 인지 확인할 수 없습니다. 이전에 사무실 전화를 통해 계정에 로그인 하는 대체 방법을 추가 했을 수도 있습니다. 그렇다면이 대체 방법을 지금 사용할 수 있습니다. 대체 인증 방법을 추가 하지 않은 경우 조직의 지원 센터에 문의 하 여 도움을 받을 수 있습니다.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>다른 인증 방법을 사용하여 회사 또는 학교 계정에 로그인

1. 계정에 로그인하고 **2단계 인증** 페이지에서 **다른 방법으로 로그인** 링크를 선택합니다.

    ![로그인 인증 방법 변경](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    **다른 방법으로 로그인** 링크가 표시되지 않으면 다른 인증 방법을 설정하지 않은 것입니다. 계정에 로그인하는 데 도움이 필요하면 관리자에게 문의해야 합니다.

2. 대체 인증 방법을 선택하고 2단계 인증 프로세스를 계속합니다.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>모바일 디바이스를 분실했거나 도난당한 경우

모바일 장치를 분실 했거나 도난당 한 경우 다음 작업 중 하나를 수행할 수 있습니다.

- 다른 방법을 사용 하 여 로그인 합니다.
- 조직의 지원 센터에 문의 하 여 설정을 지워야 합니다.

휴대폰을 분실 하거나 도난당 한 경우 조직의 지원 센터에 알려 주는 것이 좋습니다. 지원 센터에서 계정에 대 한 적절 한 업데이트를 수행할 수 있습니다. 설정이 지워진 후 다음 번에 로그인할 때 [2단계 인증 등록](multi-factor-authentication-end-user-first-time.md) 메시지가 표시됩니다.

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>내 모바일 장치에 전송 된 확인 코드를 수신 하 고 있지 않습니다.

확인 코드를 수신 하지 않는 것은 일반적인 문제입니다. 문제는 일반적으로 모바일 장치 및 해당 설정과 관련 되어 있습니다. 시도할 수 있는 몇 가지 작업은 다음과 같습니다.

사용 방법 | 지침 정보
--------- | ------------
모바일 디바이스 다시 시작 | 경우에 따라 디바이스에 새로 고침이 필요할 수도 있습니다. 장치를 다시 시작 하면 모든 백그라운드 프로세스 및 서비스가 종료 됩니다. 다시 시작 하면 장치의 핵심 구성 요소도 종료 됩니다. 장치를 다시 시작 하면 모든 서비스 또는 구성 요소가 새로 고쳐집니다.
보안 정보가 올바른지 확인합니다. | 보안 인증 방법 정보 특히 전화 번호가 정확한지 확인합니다. 잘못된 전화 번호를 입력한 경우, 모든 경고는 잘못 입력된 해당 전화 번호로 전달됩니다. 다행히도 해당 사용자는 경고를 사용하여 어떤 작업도 수행할 수 없으며 다만 이는 계정에 로그인하는 데에도 도움이 되지 않습니다. 정보가 올바른지 확인하려면 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서에서 해당 지침을 참조하세요.
알림이 설정되었는지 확인 | 모바일 장치의 알림이 켜져 있는지 확인 합니다. 다음 알림 모드가 허용 되는지 확인 합니다. <br/><br/> &bull; 전화 통화 <br/> &bull; 인증 앱 <br/> &bull; 텍스트 메시징 앱 <br/><br/> 이러한 모드에서 장치에 _표시_ 되는 경고를 생성 하는지 확인 합니다.
디바이스 신호 및 인터넷 연결 여부 확인 | 전화 통화 및 문자 메시지가 모바일 디바이스에 수신되는지 확인합니다. 친구에게 전화 통화 및 문자 메시지 전송을 부탁해 두 가지 모두 수신되는지 확인합니다. 전화 또는 텍스트를 받지 못한 경우 먼저 모바일 장치가 켜져 있는지 확인 합니다. 장치가 켜져 있지만 통화 또는 텍스트를 받지 못하는 경우 네트워크에 문제가 있을 수 있습니다. 공급자에 게 문의 해야 합니다. 신호와 관련된 문제들이 종종 발생할 경우, 모바일 디바이스에 [Microsoft Authenticator 앱](user-help-auth-app-download-install.md)을 설치한 후 사용하는 것이 좋습니다. 이 인증자 앱은 셀 신호 또는 인터넷 연결이 없어도 로그인용으로 임의 보안 코드를 생성할 수 있습니다.
방해 금지 해제 | 모바일 디바이스에서 **방해 금지** 기능을 사용하도록 설정하지 않았는지 확인합니다. 이 기능을 켜면 알림이 모바일 디바이스에서 경고하도록 허용되지 않습니다. 이 기능을 해제하는 방법에 관한 지침은 모바일 디바이스의 설명서를 참조하세요.
전화 번호 차단 해제 | 미국에서 Microsoft의 음성 통화는 +1(866) 539 4191, +1(855) 330 8653 및 +1(877) 668 6536에서 발신됩니다.
배터리 관련 설정 확인 | 이는 화면에서 약간 이상한 것 같습니다. 하지만 백그라운드에서 사용 되지 않는 앱의 남은 활성 상태를 중지 하도록 배터리 최적화를 설정한 경우 알림 시스템에 영향을 줄 가능성이 높습니다. 이 문제를 해결 하려면 인증 앱과 메시징 앱 모두에 대 한 배터리 최적화를 해제 하세요. 계정에 다시 로그인 해 보세요.
타사 보안 앱 사용 안 함 | 일부 전화 보안 앱은 성가신 알려지지 않은 호출자의 문자 메시지 및 전화 통화를 차단 합니다. 이러한 앱은 휴대폰에서 확인 코드를 수신 하지 못할 수 있습니다. 휴대폰에서 타사 보안 앱을 사용 하지 않도록 설정한 다음 다른 확인 코드를 보내도록 요청 하세요.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>두 번째 확인 정보를 입력 하 라는 메시지가 표시 되지 않습니다.

사용자 이름 및 암호를 사용 하 여 회사 또는 학교 계정에 로그인 합니다. 다음에는 추가 보안 확인 정보를 묻는 메시지가 표시 됩니다. 메시지가 표시 되지 않으면 장치를 아직 설정 하지 않은 것일 수 있습니다. 모바일 디바이스는 특정 추가 보안 확인 방법으로 작동하도록 설정해야 합니다.

모바일 장치가 켜져 있고 사용 가능한 지 확인 하려면 [2 단계 인증 방법 관리 설정](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조 하세요. 디바이스 또는 계정을 설정하지 않은 경우, [2단계 인증에 대한 계정 설정](multi-factor-authentication-end-user-first-time.md) 문서의 해당 단계를 수행하여 설정을 진행할 수 있습니다.

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>새 전화 번호를 추가 하려고 합니다.

새 전화 번호가 있는 경우 보안 확인 방법 세부 정보를 업데이트 해야 합니다. 그러면 확인 메시지가 올바른 위치로 이동할 수 있습니다. 확인 방법을 업데이트 하려면 [2 단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) 문서의 단계에 따라 **전화 번호 추가 또는 변경** 섹션을 참조 하세요.

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>새 모바일 장치를 추가 하 고 싶습니다.

새 모바일 장치를 사용 하는 경우 2 단계 인증을 사용 하도록 설정 해야 합니다. 이는 다단계 솔루션에 속합니다.

1. [2 단계 인증을 위한 내 계정 설정](multi-factor-authentication-end-user-first-time.md) 문서의 단계를 수행 하 여 사용자 계정으로 작업 하도록 장치를 설정 합니다.

1. **추가 보안 인증** 페이지에서 계정 및 장치 정보를 업데이트 합니다. 이전 장치를 삭제 하 고 새 장치를 추가 하 여 업데이트를 수행 합니다. 자세한 내용은 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조하세요.

선택적 단계:

- 모바일 장치에 Microsoft Authenticator 앱을 설치 하 고 설정 합니다. [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 수행 하 여을 설치 합니다.

- 신뢰할 수 있는 장치에 대 한 2 단계 인증 (2-FV)을 켭니다. [2 단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) 문서의 **신뢰할 수 있는 장치에 대 한 2 단계 확인 프롬프트 켜기** 섹션의 단계에 따라 2 개의 fv를 설정 합니다.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>여행 중 모바일 디바이스에 로그인하는 데 문제가 있는 경우

국제 행선지에 있는 동안 문자 메시지와 같은 모바일 디바이스 관련 확인 방법을 사용하는 것이 더 어려울 수 있습니다. 또한 모바일 디바이스에서 로밍 요금이 발생할 수도 있습니다. 이 경우, Wi-Fi 핫스팟에 연결하는 옵션과 함께 Microsoft Authenticator 앱을 사용하는 것이 좋습니다. 모바일 장치에서 Microsoft Authenticator 앱을 설정 하는 방법에 대 한 자세한 내용은 [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서를 참조 하세요.

## <a name="i-cant-get-my-app-passwords-to-work"></a>사용할 앱 암호를 수신할 수 없는 경우

2단계 인증을 지원하지 않는 이전 데스크톱 애플리케이션의 경우, 앱 암호가 사용자의 일반 암호를 대체합니다. 먼저 암호를 올바르게 입력했는지 확인합니다. 이 문제가 해결 되지 않으면 앱에 대 한 새 앱 암호를 만들어 보세요. 이렇게 하려면 [2 단계 인증을 위한 앱 암호 관리](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) 문서의 **My Apps 포털을 사용 하 여 앱 암호 만들기 및 삭제** 섹션의 단계를 수행 합니다.

## <a name="i-cant-turn-off-two-factor-verification"></a>2 단계 인증을 해제할 수 없습니다.

회사 또는 학교 계정(예: alain@contoso.com)에서 2단계 인증을 사용하는 경우, 조직에서는 이 추가된 보안 기능을 사용해야 한다고 판단했을 가능성이 무엇보다도 높습니다. 조직에서 이 기능을 사용해야 한다고 결정했으므로 개별적으로 해제할 수 있는 방법이 없습니다. 그러나 alain@outlook.com과 같은 개인 계정으로 2단계 인증을 사용하는 경우, 해당 기능을 설정 및 해제할 수 있습니다. 개인 계정에서 2단계 인증 제어 방법에 관한 지침은 [Microsoft 계정에 대해 2단계 인증 설정 또는 해제](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조하세요.

2단계 인증을 해제할 수 없는 경우, 조직 수준에서 적용된 보안 기본값 때문일 수도 있습니다. 보안 기본값에 관한 자세한 내용은 [보안 기본값이란?](../fundamentals/concept-fundamentals-security-defaults.md)을 참조하세요.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>문제에 대한 답변을 찾을 수 없는 경우

이러한 단계를 시도했지만 여전히 문제가 계속된다면 조직의 지원 센터에 문의하여 도움을 받으세요.

## <a name="related-articles"></a>관련 문서

- [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 앱 FAQ](user-help-auth-app-faq.md)