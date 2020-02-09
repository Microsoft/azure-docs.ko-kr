---
title: 셀프 서비스 암호 재설정 심층 살펴보기-Azure Active Directory
description: SSPR(셀프 서비스 암호 재설정)은 어떻게 작동하나요?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b19c80378aa40a7f791a3eb61130b013217ddee
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848581"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>작동 방식: Azure AD SSPR(셀프 서비스 암호 재설정)

SSPR(셀프 서비스 암호 재설정)은 어떻게 작동하나요? 인터페이스에서 이 옵션은 무엇인가요? 계속하여 Azure AD(Azure Active Directory) SSPR에 대해 자세히 알아보겠습니다.

## <a name="how-does-the-password-reset-portal-work"></a>암호 재설정 포털의 작동 원리

사용자가 암호 재설정 포털로 이동하면 다음 항목을 확인하기 위해 워크플로가 시작됩니다.

   * 페이지를 지역화하려면 어떻게 해야 하나요?
   * 사용자 계정이 유효한가요?
   * 사용자는 어떤 조직에 속해 있나요?
   * 사용자의 암호는 어디서 관리하나요?
   * 사용자가 기능을 사용하도록 허가되었나요?

다음 단계에 따라 암호 재설정 페이지의 논리에 대해 자세히 알아봅니다.

1. 사용자가 **계정에 액세스할 수 없습니까?** 링크를 선택하거나 [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)로 직접 이동합니다.
   * 환경은 브라우저 로캘에 따라 적절한 언어로 렌더링됩니다. 암호 재설정 환경은 Office 365에서 지원하는 것과 동일한 언어로 지역화됩니다.
   * 다른 번역 언어로 암호 재설정 포털을 보려면 암호 재설정 URL 마지막에 "?mkt="을 첨부합니다. [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) 예에서는 스페인어로 번역됩니다.
2. 사용자 ID를 입력하고 captcha를 전달합니다.
3. Azure AD에서 다음 항목을 확인하여 사용자가 이 기능을 사용할 수 있는지 확인합니다.
   * 사용자가 이 기능을 사용할 수 있고 Azure AD 라이선스가 할당되어 있는지 확인합니다.
     * 사용자가 이 기능을 사용할 수 없거나 라이선스가 할당되어 있지 않은 경우 관리자에게 문의하여 암호를 다시 설정하도록 사용자에게 요구합니다.
   * 관리자 정책에 따라 사용자의 계정에 올바른 인증 방법이 정의되어 있는지 확인합니다.
     * 정책에 하나의 방법만 필요한 경우 관리자 정책에서 사용하도록 설정된 하나 이상의 인증 방법에 대해 정의된 적절한 데이터가 사용자에게 있는지 확인합니다.
       * 인증 방법이 구성되어 있지 않으면 관리자에게 문의하여 암호를 다시 설정하도록 사용자에게 요구합니다.
     * 정책에 두 방법만 필요한 경우 관리자 정책에서 사용하도록 설정된 두 인증 방법에 대해 정의된 적절한 데이터가 사용자에게 있는지 확인합니다.
       * 인증 방법이 구성되어 있지 않으면 관리자에게 문의하여 암호를 다시 설정하도록 사용자에게 요구합니다.
     * Azure 관리자 역할이 사용자에게 할당된 경우에는 강력한 2-게이트 암호 정책이 적용됩니다. 이 정책에 대한 자세한 내용은 [관리자 재설정 정책의 차이점](concept-sspr-policy.md#administrator-reset-policy-differences) 섹션에서 찾을 수 있습니다.
   * 사용자의 암호가 온-프레미스에서 관리되는지(페더레이션, 통과 인증 또는 암호 해시 동기화) 여부를 확인합니다.
     * 쓰기 저장이 배포되어 있고 사용자의 암호가 온-프레미스에서 관리되는 경우 사용자가 인증을 진행하고 암호를 다시 설정할 수 있습니다.
     * 쓰기 저장이 배포되어 있지 않고 사용자의 암호가 온-프레미스에서 관리되는 경우 관리자에게 문의하여 암호를 다시 설정하도록 사용자에게 요구합니다.
4. 사용자가 성공적으로 암호를 다시 설정할 수 있다고 확인되는 경우 사용자를 재설정 프로세스로 안내합니다.

## <a name="authentication-methods"></a>인증 방법

SSPR을 사용하는 경우 인증 방법으로 다음 옵션 중 하나 이상을 선택해야 합니다. 이러한 옵션을 "게이트"라고 하는 경우도 있습니다. 사용자가 필요할 때 한 인증 방법에 액세스하지 못하는 상황에서 더 유연하게 사용할 수 있게 **두 개 이상의 인증 방법을 선택**하는 것이 좋습니다. 아래에 나열 된 방법에 대 한 추가 정보는 [인증 방법 이란?](concept-authentication-methods.md)문서에서 찾을 수 있습니다.

* 모바일 앱 알림
* 모바일 앱 코드
* 이메일
* 휴대폰
* 사무실 전화
* 보안 질문

사용자는 관리자가 사용하도록 설정한 인증 방법으로 데이터를 표시하는 경우에만 자신의 암호를 재설정할 수 있습니다.

> [!IMPORTANT]
> 2019 년 3 월부터, 무료/평가판 Azure AD 테넌트의 MFA 및 SSPR 사용자가 전화 통화 옵션을 사용할 수 없습니다. SMS 메시지는 이러한 변경의 영향을 받지 않습니다. 전화 통화는 유료 Azure AD 테넌트의 사용자가 계속 사용할 수 있습니다. 이 변경 내용은 무료/평가판 Azure AD 테넌트에만 영향을 줍니다.

> [!WARNING]
> Azure 관리자 역할이 할당된 계정은 [관리자 재설정 정책의 차이점](concept-sspr-policy.md#administrator-reset-policy-differences) 섹션에 정의된 메서드를 사용해야 합니다.

![Azure Portal의 인증 방법 선택][Authentication]

### <a name="number-of-authentication-methods-required"></a>필수 인증 방법의 수

이 옵션은 사용자가 자신의 암호를 다시 설정하거나 잠금을 해제하기 위해 통과해야 하는 사용 가능한 인증 방법 또는 게이트의 최소 수를 결정합니다. 이 수는 1 또는 2로 설정할 수 있습니다.

관리자가 해당 인증 방법을 사용하도록 설정한 경우 사용자는 더 많은 인증 방법을 제공하도록 선택할 수 있습니다.

사용자에 대해 최소한의 필요한 인증 방법이 등록되지 않은 경우 관리자가 사용자의 암호를 다시 설정하도록 지시하는 오류 페이지가 표시됩니다.

#### <a name="mobile-app-and-sspr"></a>모바일 앱 및 SSPR

Microsoft Authenticator 앱과 같은 모바일 앱을 암호 재설정 방법으로 사용할 경우 다음 주의 사항에 유의해야 합니다.

* 관리자가 하나의 방법으로 암호를 재설정하도록 요구하는 경우 사용 가능한 유일한 옵션은 인증 코드입니다.
* 관리자가 두 가지 방법으로 암호를 재설정하도록 요구하는 경우, 사용자는 다른 활성화된 방법과 함께 **알림** 또는 **확인 코드**를 사용하여 재설정할 수 있습니다.

| 재설정에 필요한 방법 수 | 1 | 2 |
| :---: | :---: | :---: |
| 모바일 앱 기능 사용 가능 | 코드 | 코드 또는 알림 |

사용자에게는 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)에서 셀프 서비스 암호 재설정 등록을 위해 모바일 앱을 등록할 수 있는 옵션이 없습니다. 사용자는 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에서 또는 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)의 새로운 보안 정보 등록 미리 보기에서 자신의 모바일 앱을 등록할 수 있습니다.

> [!WARNING]
> 사용자가 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)에서 새 환경에 액세스할 수 있으려면 [셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication에 대한 등록 수렴(공개 미리 보기)](concept-registration-mfa-sspr-converged.md)를 사용하도록 설정해야 합니다.

> [!IMPORTANT]
> 1 게이트 정책을 구성할 때 인증자 앱을 유일한 인증 방법으로 선택할 수 없습니다. 마찬가지로, authenticator 앱과 2 게이트 정책을 구성할 때 하나의 추가 메서드만 선택할 수 있습니다.
> 그러면 인증자 앱을 메서드로 포함 하는 SSPR 정책을 구성 하는 경우 1 게이트 정책을 구성할 때 최소한 추가 방법을 선택 해야 하며, 2 게이트 정책을 구성할 때 적어도 두 개 이상의 추가 방법을 선택 해야 합니다.
> 이 요구 사항에 대 한 이유는 현재 SSPR 등록 환경에 인증자 앱을 등록 하는 옵션이 포함 되어 있지 않기 때문입니다. Authenticator 앱을 등록 하는 옵션이 [셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication (공개 미리 보기)에 대 한 새 수렴 형 등록과](concept-registration-mfa-sspr-converged.md)함께 포함 되어 있습니다.
> Authenticator 앱만 사용 하는 정책 (1-게이트 정책의 경우) 또는 인증자 앱과 하나의 추가 방법 (2 게이트 정책의 경우)만 허용 하면 새를 사용 하도록 구성 될 때까지 사용자가 SSPR에 대 한 등록을 차단 하 게 됩니다. 등록 환경.

### <a name="change-authentication-methods"></a>인증 방법 변경

등록된 재설정 또는 잠금 해제에 필요한 인증 방법이 하나만 있는 정책으로 시작한 후에 이를 두 가지 방법으로 변경하면 어떻게 되나요?

| 등록 방법 수 | 필요한 방법 수 | 결과 |
| :---: | :---: | :---: |
| 1개 이상 | 1 | 다시 설정 또는 잠금 해제 **가능** |
| 1 | 2 | 다시 설정 또는 잠금 해제 **불가능** |
| 2 이상 | 2 | 다시 설정 또는 잠금 해제 **가능** |

사용자가 사용할 수 있는 인증 방법 유형을 변경하면 사용 가능한 최소 데이터 양이 없는 경우 사용자가 실수로 SSPR을 사용할 수 없게 될 수 있습니다.

예제:
1. 원래 정책은 필요한 두 가지 인증 방법으로 구성됩니다. 사무실 전화 번호와 보안 질문만 사용합니다. 
2. 관리자는 더 이상 보안 질문을 사용하지 않도록 정책을 변경하지만 휴대폰 및 보조 메일은 사용하도록 허용합니다.
3. 입력한 휴대폰 또는 대체 이메일 필드가 없는 사용자는 암호를 다시 설정할 수 없습니다.

## <a name="registration"></a>등록

### <a name="require-users-to-register-when-they-sign-in"></a>사용자가 로그인 시 등록하도록 요구하시겠습니까?

이 옵션을 사용하려면 사용자가 Azure AD를 사용하는 애플리케이션에 로그인한 경우 암호 재설정 등록을 완료해야 합니다. 이 워크플로에는 다음 애플리케이션이 포함됩니다.

* PowerApps
* Azure Portal
* 액세스 패널
* 페더레이션된 애플리케이션
* Azure AD를 사용하여 애플리케이션 사용자 지정

등록 요구를 사용하지 않도록 설정된 경우 사용자가 수동으로 등록할 수 있습니다. [액세스 패널]의 **프로필** 탭 아래에서 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)을 방문하거나 **암호 재설정에 등록** 링크를 선택할 수 있습니다.

> [!NOTE]
> 사용자가 **취소**를 선택하거나 창을 닫으면 암호 재설정 등록 포털을 해제할 수 있습니다. 그러나 이 경우에는 등록을 완료할 때까지 로그인할 때마다 등록하도록 요구하는 메시지가 표시됩니다.
>
> 이미 로그인되어 있으면 이 중단으로 인해 사용자의 연결이 끊어지지 않습니다.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>사용자가 인증 정보를 다시 확인하도록 요청받을 때까지의 기간(일) 설정

이 옵션은 인증 정보 설정 및 재확인 사이의 기간을 결정하고, **사용자가 로그인 시 등록하도록 요구하시겠습니까?** 옵션을 사용하도록 설정한 경우에만 사용할 수 있습니다.

유효한 값은 0-730일이며, 여기서 "0"은 사용자가 해당 인증 정보를 다시 확인하도록 요청받지 않습니다.

## <a name="notifications"></a>공지

### <a name="notify-users-on-password-resets"></a>사용자에게 암호 재설정에 대해 알림

이 옵션을 **예**로 설정하면 암호를 다시 설정하는 사용자가 암호가 변경되었음을 알려주는 전자 메일을 받게 됩니다. 이 전자 메일은 SSPR 포털을 통해 Azure AD의 파일에 있는 기본 및 대체 이메일 주소로 보냅니다. 누구도 재설정 이벤트의 알림을 받지 않습니다.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>다른 관리자가 암호를 재설정하면 모든 관리자에게 알림

이 옵션을 **예**로 설정하면 '모든 관리자'가 Azure AD의 파일에 있는 기본 이메일 주소로 전자 메일을 받습니다. 전자 메일은 사용자에게 다른 관리자가 SSPR을 사용하여 암호를 변경했음을 알려줍니다.

예제: 환경에 4명의 관리자가 있습니다. A 관리자는 SSPR을 사용하여 암호를 다시 설정합니다. B, C 및 D 관리자는 암호 재설정을 알려주는 이메일을 받습니다.

## <a name="on-premises-integration"></a>온-프레미스 통합

Azure AD Connect를 설치, 구성 및 사용 설정하는 경우 온-프레미스 통합을 위해 다음과 같은 추가 옵션이 있습니다. 이 옵션이 회색으로 표시되면 쓰기 저장이 제대로 구성되지 않은 것입니다. 자세한 내용은 [비밀번호 쓰기 저장 구성](howto-sspr-writeback.md)을 참조하세요.

![비밀 번호 쓰기 저장이 사용 하도록 설정 되어 있고 작동 하는지 확인][Writeback]

이 페이지에서는 현재 구성에 따라 다음 메시지 중 하나가 표시되는 온-프레미스 쓰기 저장 클라이언트의 빠른 상태를 제공합니다.

* 온-프레미스 쓰기 저장 클라이언트가 실행 중입니다.
* Azure AD는 온라인 상태이며 온-프레미스 쓰기 저장 클라이언트에 연결되어 있습니다. 그러나 설치된 Azure AD Connect 버전이 만료된 것 같습니다. 최신 연결 기능을 사용하고 중요한 버그가 수정되도록 [Azure AD Connect 업그레이드](../hybrid/how-to-upgrade-previous-version.md)를 수행하는 것이 좋습니다.
* Azure AD Connect의 설치 버전이 오래된 버전이므로 온-프레미스 쓰기 저장 클라이언트 상태를 확인할 수 없습니다. 연결 상태를 확인할 수 있도록 [Azure AD Connect를 업그레이드](../hybrid/how-to-upgrade-previous-version.md)합니다.
* 현재 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없습니다. [Azure AD Connect 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)을 통해 연결을 복원합니다.
* 비밀번호 쓰기 저장이 올바르게 구성되지 않았으므로 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없습니다. [비밀번호 쓰기 저장 구성](howto-sspr-writeback.md)으로 연결을 복원합니다.
* 현재 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없습니다. 일시적인 문제로 인한 것일 수 있습니다. 문제가 지속되면 [Azure AD Connect 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)을 통해 연결을 복원합니다.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>온-프레미스 디렉터리에 대한 비밀번호 쓰기 저장

이 컨트롤은 이 디렉터리에 대해 비밀번호 쓰기 저장이 사용되는지 여부를 결정합니다. 쓰기 저장이 설정되면 온-프레미스 쓰기 저장 서비스의 상태를 나타냅니다. 이 컨트롤은 Azure AD Connect를 다시 구성하지 않고 비밀번호 쓰기 저장을 일시적으로 사용하지 않도록 설정하려는 경우에 유용합니다.

* 스위치를 **예**로 설정하면 쓰기 저장이 사용하도록 설정되며, 페더레이션되고, 통과 인증 또는 암호 해시 동기화된 사용자는 암호를 다시 설정할 수 있습니다.
* 스위치를 **아니요**로 설정하면 쓰기 저장을 사용하지 않도록 설정되며, 페더레이션되고, 통과 인증 또는 암호 해시 동기화된 사용자는 암호를 다시 설정할 수 없습니다.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>사용자가 해당 암호를 재설정하지 않고 계정의 잠금을 해제할 수 있음

이 컨트롤은 암호 재설정 포털을 방문하는 사용자에게 암호를 다시 설정하지 않고 온-프레미스 Active Directory 계정의 잠금을 해제할 수 있는 옵션을 제공하는지 여부를 지정합니다. 기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다. 이 설정을 사용하여 다음 두 가지 작업을 구분합니다.

* **예**로 설정하면 암호를 다시 설정하고 계정의 잠금을 해제하거나, 암호를 다시 설정하지 않고 계정의 잠금을 해제할 수 있는 옵션이 사용자에게 제공됩니다.
* **아니요**로 설정하면 사용자는 결합된 암호 재설정 및 계정 잠금 해제 작업만 수행할 수 있습니다.

### <a name="on-premises-active-directory-password-filters"></a>온-프레미스 Active Directory 암호 필터

Azure AD 셀프 서비스 암호 재설정은 Active Directory에서 관리자가 시작하는 암호 재설정과 동일한 작업을 수행합니다. 타사 암호 필터를 사용하여 사용자 지정 암호 규칙을 적용하고 Azure AD 셀프 서비스 암호 재설정 중에 이 암호 필터를 확인하도록 하는 경우에는 관리자 암호 재설정 시나리오에서 타사 암호 필터 솔루션을 적용하도록 구성해야 합니다. [Windows Server Active Directory에 대한 Azure AD 암호 보호](concept-password-ban-bad-on-premises.md)는 기본적으로 지원됩니다.

## <a name="password-reset-for-b2b-users"></a>B2B 사용자에 대한 암호 재설정

암호 재설정 및 변경은 모든 B2B(기업 간 전자 상거래) 구성에서 완전히 지원됩니다. B2B 사용자 암호 재설정은 다음 세 가지 경우에 지원됩니다.

* **기존 Azure AD 테넌트가 있는 파트너 조직의 사용자** - 제휴한 조직에 기존 Azure AD 테넌트가 있는 경우 "해당 테넌트에서 사용되는 모든 암호 재설정 정책을 따릅니다". 암호 재설정이 작동하려면 파트너 조직에서 Azure AD SSPR을 사용하도록 설정되어 있는지 확인해야 합니다. Office 365 고객에게 추가 요금이 청구되지 않으며 [암호 관리 시작](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) 가이드의 단계에 따라 이 기능을 사용하도록 설정할 수 있습니다.
* 셀프 서비스 등록을 통해 **로그인한 사용자** - 제휴한 조직에서 [셀프 서비스 등록](../users-groups-roles/directory-self-service-signup.md) 기능을 사용하여 테넌트에 가입한 경우 등록한 전자 메일을 통해 암호를 다시 설정하도록 합니다.
* **B2B 사용자** - 새 [Azure AD B2B 기능](../active-directory-b2b-what-is-azure-ad-b2b.md)을 사용하여 만든 모든 새 B2B 사용자는 초대 프로세스 중에 등록한 전자 메일을 통해 암호를 다시 설정할 수 있습니다.

이 시나리오를 테스트하려면 이러한 파트너 사용자 중 하나로 https://passwordreset.microsoftonline.com 으로 이동합니다. 대체 전자 메일 또는 인증 전자 메일이 정의되어 있으면 암호 재설정이 예상대로 작동합니다.

> [!NOTE]
> Hotmail.com, Outlook.com 또는 기타 개인 이메일 주소와 같이 Azure AD 테넌트에 게스트 액세스 권한이 부여된 Microsoft 계정은 Azure AD SSPR을 사용할 수 없습니다. [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에 있는 정보를 사용하여 암호를 다시 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 Azure AD를 통한 암호 재설정에 대한 추가 정보를 제공합니다.

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](howto-sspr-deployment.md)
* [암호 재설정 또는 변경](../user-help/active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](../user-help/active-directory-passwords-reset-register.md)
* [라이선스 관련 질문이 있습니까?](concept-sspr-licensing.md)
* [SSPR에서 사용하는 데이터는 무엇이며, 사용자에 대해 어떤 데이터를 채워야 합니까?](howto-sspr-authenticationdata.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](concept-sspr-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](howto-sspr-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](howto-sspr-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](concept-sspr-howitworks.md)
* [뭐가 손상 된 것으로 생각 합니다. SSPR 문제 해결을 어떻게 할까요??](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "사용 가능한 Azure AD 인증 방법 및 필요한 수량"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "온-프레미스 통합 비밀번호 쓰기 저장 구성 및 문제 해결 정보"
