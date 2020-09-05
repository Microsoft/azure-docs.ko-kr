---
title: 셀프 서비스 암호 재설정 심층 살펴보기-Azure Active Directory
description: SSPR(셀프 서비스 암호 재설정)은 어떻게 작동하나요?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce07575fe95ffbd4fd906bcde7d76d89e50d48b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716319"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>작동 방식: Azure AD SSPR(셀프 서비스 암호 재설정)

Azure AD(Azure Active Directory) SSPR(셀프 서비스 암호 재설정)을 사용하면 관리자 또는 지원 센터에서 개입하지 않고도 사용자가 암호를 변경하거나 다시 설정할 수 있습니다. 사용자 계정이 잠겨 있거나 암호를 잊어버린 경우 프롬프트에 따라 자신을 차단 해제하여 작업을 다시 수행할 수 있습니다. 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다.

> [!IMPORTANT]
> 이 개념 문서에서는 셀프 서비스 암호 재설정이 작동 하는 방법을 관리자에 게 설명 합니다. 이미 셀프 서비스 암호 재설정에 등록 된 최종 사용자가 계정으로 다시 전환 해야 하는 경우으로 이동 [https://aka.ms/sspr](https://aka.ms/sspr) 합니다.
>
> IT 팀이 자신의 암호를 재설정 하는 기능을 사용하도록 설정하지 않은 경우, 추가 지원이 필요 하면 기술 지원팀에 문의하세요.

## <a name="how-does-the-password-reset-portal-work"></a>암호 재설정 포털의 작동 원리

사용자는 [SSPR 포털](https://aka.ms/sspr)을 사용 하 여 암호를 다시 설정 하거나 변경할 수 있습니다. 먼저 원하는 인증 방법을 등록 해야 합니다. 사용자가 SSPR 포털에 액세스 하는 경우 Azure 플랫폼은 다음 요소를 고려 합니다.

* 페이지를 지역화하려면 어떻게 해야 하나요?
* 사용자 계정이 유효한가요?
* 사용자는 어떤 조직에 속해 있나요?
* 사용자의 암호는 어디에서 관리 되나요?
* 사용자가 기능을 사용하도록 허가되었나요?

사용자가 응용 프로그램 또는 페이지에서 **계정에 액세스할 수 없음** 링크를 선택 하거나 직접로 이동 하는 경우 [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) SSPR 포털에서 사용 되는 언어는 다음 옵션을 기반으로 합니다.

* 기본적으로 브라우저 로캘은 적절 한 언어로 SSPR을 표시 하는 데 사용 됩니다. 암호 재설정 환경은 [Office 365에서 지 원하는](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)것과 동일한 언어로 지역화 됩니다.
* 특정 지역화 된 언어로 SSPR에 연결 하려면 `?mkt=` 필요한 로캘과 함께 암호 재설정 URL의 끝에를 추가 합니다.
    * 예를 들어 스페인어 *es* 로캘을 지정 하려면를 사용 `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) 합니다.

SSPR 포털이 필수 언어로 표시 되 면 사용자에 게 사용자 ID를 입력 하 고 captcha를 전달 하 라는 메시지가 표시 됩니다. 이제 Azure AD는 사용자가 다음 검사를 수행 하 여 SSPR를 사용할 수 있는지 확인 합니다.

* 사용자가 SSPR 사용 하도록 설정 되어 있고 Azure AD 라이선스가 할당 되었는지 확인 합니다.
  * 사용자가 SSPR에 대해 사용 하도록 설정 되어 있지 않거나 라이선스가 할당 되지 않은 경우 관리자에 게 암호를 재설정 하도록 요청 하 라는 메시지가 표시 됩니다.
* 관리자 정책에 따라 사용자의 계정에 올바른 인증 방법이 정의되어 있는지 확인합니다.
  * 정책에 하나의 메서드만 필요한 경우 관리자 정책에서 사용 하도록 설정 된 하나 이상의 인증 방법에 대해 사용자에 게 적절 한 데이터가 정의 되어 있는지 확인 합니다.
    * 인증 방법이 구성 되지 않은 경우 사용자는 자신의 암호를 재설정 하기 위해 관리자에 게 연락 하는 것이 좋습니다.
  * 정책에 두 가지 방법이 필요한 경우 관리자 정책에서 사용 하도록 설정 된 하나 이상의 인증 방법에 대해 정의 된 적절 한 데이터가 사용자에 게 있는지 확인 합니다.
    * 인증 방법이 구성 되지 않은 경우 사용자는 자신의 암호를 재설정 하기 위해 관리자에 게 연락 하는 것이 좋습니다.
  * Azure 관리자 역할이 사용자에게 할당된 경우에는 강력한 2-게이트 암호 정책이 적용됩니다. 자세한 내용은 [관리자 정책 재설정 정책 차이점](concept-sspr-policy.md#administrator-reset-policy-differences)을 참조 하세요.
* Azure AD 테 넌 트가 페더레이션된, 통과 인증 또는 암호 해시 동기화를 사용 하는 경우와 같이 사용자의 암호가 온-프레미스에서 관리 되는지 확인 합니다.
  * SSPR 쓰기 저장이 구성 되어 있고 사용자의 암호가 온-프레미스에서 관리 되는 경우 사용자는 인증을 진행 하 고 암호를 다시 설정할 수 있습니다.
  * SSPR 쓰기 저장이 배포 되지 않고 사용자의 암호가 온-프레미스에서 관리 되는 경우 사용자에 게 관리자에 게 문의 하 여 암호를 다시 설정 하 라는 메시지가 표시 됩니다.

위의 모든 검사가 성공적으로 완료 되 면 사용자는 암호를 다시 설정 하거나 변경 하는 프로세스를 안내 합니다.

SSPR을 시작 하려면 다음 자습서를 완료 하세요.

> [!div class="nextstepaction"]
> [자습서: 셀프 서비스 암호 재설정 사용 (SSPR)](tutorial-enable-sspr.md)

## <a name="registration-options"></a>등록 옵션

사용자가 SSPR를 사용 하 여 암호를 다시 설정 하거나 변경 하려면 먼저 해당 사용자를 등록 하 고 사용할 인증 방법을 등록 해야 합니다. 이전 섹션에서 설명한 것 처럼 SSPR에 대해 사용자를 등록 하 고 적절 한 라이선스를 적용 해야 합니다.

### <a name="require-users-to-register-when-they-sign-in"></a>사용자가 로그인 시 등록하도록 요구하시겠습니까?

Azure AD를 사용 하 여 응용 프로그램에 로그인 하는 경우 사용자가 SSPR 등록을 완료 하도록 요구 하는 옵션을 사용 하도록 설정할 수 있습니다. 이 워크플로에는 다음 애플리케이션이 포함됩니다.

* Office 365
* Azure portal
* 액세스 패널
* 페더레이션된 애플리케이션
* Azure AD를 사용하여 애플리케이션 사용자 지정

등록이 필요 하지 않은 경우 로그인 중에 사용자에 게 메시지가 표시 되지 않지만 수동으로 등록할 수 있습니다. 사용자는 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 액세스 패널의 **프로필** 탭에서 **암호 재설정에 등록** 링크를 방문 하거나 선택할 수 있습니다.

![Azure Portal의 SSPR에 대 한 등록 옵션][Registration]

> [!NOTE]
> 사용자는 **취소** 를 선택 하거나 창을 닫으면 SSPR 등록 포털을 해제할 수 있습니다. 그러나 등록을 완료할 때까지 로그인 할 때마다 등록 하 라는 메시지가 표시 됩니다.
>
> SSPR에 등록 하는이 인터럽트는 이미 로그인 되어 있는 경우 사용자의 연결을 중단 하지 않습니다.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>사용자가 인증 정보를 다시 확인하도록 요청받을 때까지의 기간(일) 설정

암호를 재설정 하거나 변경 해야 하는 경우 인증 방법이 올바른지 확인 하려면 일정 기간 후 사용자에 게 정보 등록 정보를 확인 하도록 요구할 수 있습니다. 이 옵션은 **로그인 할 때 사용자가 등록 해야** 함 옵션을 사용 하도록 설정한 경우에만 사용할 수 있습니다.

사용자가 등록 된 메서드를 확인 하 라는 메시지를 표시 하는 유효한 값은 *0* 일에서 *730* 일 사이입니다. 이 값을 *0* 으로 설정 하면 사용자가 인증 정보를 확인 하는 것을 묻지 않습니다.

## <a name="authentication-methods"></a>인증 방법

사용자가 SSPR에 대해 사용 하도록 설정 된 경우 인증 방법을 하나 이상 등록 해야 합니다. 두 개 이상의 인증 방법을 선택 하는 것이 좋습니다. 사용자가 필요할 때 한 방법으로 액세스할 수 없는 경우에는 유연성이 높아집니다. 자세한 내용은 [인증 방법 이란?](concept-authentication-methods.md)을 참조 하세요.

SSPR에는 다음과 같은 인증 방법을 사용할 수 있습니다.

* 모바일 앱 알림
* 모바일 앱 코드
* 메일
* 휴대폰
* 사무실 전화
* 본인 확인 질문

사용자는 관리자가 사용 하도록 설정한 인증 방법을 등록 한 경우에만 암호를 다시 설정할 수 있습니다.

> [!WARNING]
> 계정 할당 Azure *관리자* 역할은 [관리자 정책 다시 설정 차이점](concept-sspr-policy.md#administrator-reset-policy-differences)섹션에 정의 된 대로 메서드를 사용 하는 데 필요 합니다.

![Azure Portal의 인증 방법 선택][Authentication]

### <a name="number-of-authentication-methods-required"></a>필수 인증 방법의 수

사용자가 암호를 재설정 하거나 잠금 해제 하기 위해 제공 해야 하는 사용 가능한 인증 방법의 수를 구성할 수 있습니다. 이 값은 *1* 또는 *2*로 설정할 수 있습니다.

사용자는 여러 인증 방법을 등록 하 고 등록할 수 있습니다. 두 개 이상의 인증 방법을 등록 하는 것이 좋습니다. 필요한 경우 한 가지 방법으로 액세스할 수 없는 경우에도 더 유연 하 게 사용할 수 있습니다.

사용자가 SSPR를 사용 하려고 할 때 등록 된 최소한의 필수 메서드를 갖고 있지 않은 경우 관리자가 암호를 재설정 하도록 요청 하는 오류 페이지가 표시 됩니다. SSPR에 대 한 기존 사용자를 등록 하 고이 기능을 사용할 수 없는 경우에는 필요한 방법 수를 1에서 2로 늘릴 수 있습니다. 자세한 내용은 다음 섹션을 참조 하 여 [인증 방법을 변경](#change-authentication-methods)하십시오.

#### <a name="mobile-app-and-sspr"></a>모바일 앱 및 SSPR

모바일 앱을 암호 재설정을 위한 방법으로 사용 하는 경우 (예: Microsoft Authenticator 앱) 다음과 같은 고려 사항이 적용 됩니다.

* 관리자가 하나의 방법으로 암호를 재설정하도록 요구하는 경우 사용 가능한 유일한 옵션은 인증 코드입니다.
* 관리자가 암호를 재설정 하는 데 두 가지 방법을 사용 해야 하는 경우 사용자는 다른 활성화 된 방법 외에도 알림 **또는** 확인 코드를 사용할 수 있습니다.

| 재설정에 필요한 방법 수 | 하나 | 2 |
| :---: | :---: | :---: |
| 모바일 앱 기능 사용 가능 | 코드 | 코드 또는 알림 |

에서 셀프 서비스 암호 재설정을 등록 하는 경우 사용자에 게 모바일 앱을 등록 하는 옵션이 없습니다 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . 사용자는에서 모바일 앱을 등록 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 하거나의 결합 된 보안 정보 등록에서 등록할 수 있습니다 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> 메서드만 필요한 경우 인증자 앱을 유일한 인증 방법으로 선택할 수 없습니다. 마찬가지로 두 가지 메서드를 필요로 하는 경우 인증자 앱과 하나의 추가 메서드만 선택할 수 있습니다.
>
> 인증자 앱을 메서드로 포함 하는 SSPR 정책을 구성 하는 경우 한 가지 방법이 필요할 때 하나 이상의 추가 메서드를 선택 하 고 두 개의 메서드를 구성 해야 하는 경우 두 개 이상의 추가 메서드를 선택 해야 합니다.
>
> 이 요구 사항은 현재 SSPR 등록 환경에 인증자 앱을 등록 하는 옵션이 포함 되어 있지 않기 때문입니다. 인증자 앱을 등록 하는 옵션은 결합 된 새로운 [등록 환경](./concept-registration-mfa-sspr-combined.md)에 포함 되어 있습니다.
>
> 인증자 앱만 사용 하는 정책 (한 방법에 필요한 경우) 또는 인증자 앱과 하나의 추가 방법 (두 가지 방법이 필요한 경우)을 허용 하면 새로운 결합 된 등록 환경을 사용 하도록 구성 될 때까지 사용자가 SSPR 등록을 차단 하는 것으로 이어질 수 있습니다.

### <a name="change-authentication-methods"></a>인증 방법 변경

등록된 재설정 또는 잠금 해제에 필요한 인증 방법이 하나만 있는 정책으로 시작한 후에 이를 두 가지 방법으로 변경하면 어떻게 되나요?

| 등록 방법 수 | 필요한 방법 수 | 결과 |
| :---: | :---: | :---: |
| 1개 이상 | 1 | 다시 설정 또는 잠금 해제 **가능** |
| 1 | 2 | 다시 설정 또는 잠금 해제 **불가능** |
| 2 이상 | 2 | 다시 설정 또는 잠금 해제 **가능** |

사용 가능한 인증 방법을 변경 하면 사용자에 게 문제가 발생할 수도 있습니다. 사용자가 사용할 수 있는 인증 방법 유형을 변경하면 사용 가능한 최소 데이터 양이 없는 경우 사용자가 실수로 SSPR을 사용할 수 없게 될 수 있습니다.

다음 예제 시나리오를 살펴보십시오.

1. 원래 정책은 필요한 두 가지 인증 방법으로 구성됩니다. 사무실 전화 번호와 보안 질문만 사용합니다.
1. 관리자는 더 이상 보안 질문을 사용하지 않도록 정책을 변경하지만 휴대폰 및 보조 메일은 사용하도록 허용합니다.
1. 휴대폰 또는 대체 전자 메일 필드를 입력 하지 않은 사용자는 이제 암호를 재설정할 수 없습니다.

## <a name="notifications"></a>공지

SSPR를 사용 하 여 암호 이벤트의 인식을 향상 시키기 위해 사용자 및 id 관리자에 대 한 알림을 구성할 수 있습니다.

### <a name="notify-users-on-password-resets"></a>사용자에게 암호 재설정에 대해 알림

이 옵션을 **예**로 설정 하면 암호를 다시 설정 하는 사용자가 암호를 변경 했음을 알리는 전자 메일을 받습니다. 전자 메일은 SSPR 포털을 통해 Azure AD에 저장 된 기본 및 대체 전자 메일 주소로 전송 됩니다. 누구도 재설정 이벤트의 알림을 받지 않습니다.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>다른 관리자가 암호를 재설정하면 모든 관리자에게 알림

이 옵션을 **예**로 설정 하면 다른 모든 azure 관리자는 azure AD에 저장 된 기본 전자 메일 주소로 전자 메일을 받습니다. 전자 메일은 사용자에게 다른 관리자가 SSPR을 사용하여 암호를 변경했음을 알려줍니다.

다음 예제 시나리오를 살펴보십시오.

* 환경에 4명의 관리자가 있습니다.
* 관리자 *는* SSPR를 사용 하 여 암호를 다시 설정 합니다.
* 관리자 *B*, *C*및 *D* 는 암호 재설정을 알리는 전자 메일을 받습니다.

## <a name="on-premises-integration"></a>온-프레미스 통합

하이브리드 환경이 있는 경우 Azure AD에서 온-프레미스 디렉터리로 암호 변경 이벤트를 다시 쓰도록 Azure AD Connect를 구성할 수 있습니다.

![비밀 번호 쓰기 저장이 사용 하도록 설정 되어 있고 작동 하는지 확인][Writeback]

Azure AD는 현재 하이브리드 연결을 확인 하 고 Azure Portal에 다음 메시지 중 하나를 제공 합니다.

* 온-프레미스 쓰기 저장 클라이언트가 실행 중입니다.
* Azure AD는 온라인 상태이며 온-프레미스 쓰기 저장 클라이언트에 연결되어 있습니다. 그러나 설치된 Azure AD Connect 버전이 만료된 것 같습니다. 최신 연결 기능을 사용하고 중요한 버그가 수정되도록 [Azure AD Connect 업그레이드](../hybrid/how-to-upgrade-previous-version.md)를 수행하는 것이 좋습니다.
* 그러나 설치 된 Azure AD Connect 버전이 최신 버전이 아니기 때문에 온-프레미스 쓰기 저장 클라이언트 상태를 확인할 수 없습니다. 연결 상태를 확인할 수 있도록 [Azure AD Connect를 업그레이드](../hybrid/how-to-upgrade-previous-version.md)합니다.
* 현재 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없습니다. [Azure AD Connect 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)을 통해 연결을 복원합니다.
* 비밀번호 쓰기 저장이 올바르게 구성되지 않았으므로 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없습니다. [비밀번호 쓰기 저장 구성](./tutorial-enable-sspr-writeback.md)으로 연결을 복원합니다.
* 현재 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없습니다. 일시적인 문제로 인한 것일 수 있습니다. 문제가 지속되면 [Azure AD Connect 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)을 통해 연결을 복원합니다.

SSPR 쓰기 저장을 시작하려면 다음 자습서를 완료하세요.

> [!div class="nextstepaction"]
> [자습서: 셀프 서비스 암호 재설정(SSPR) 쓰기 저장 사용](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>온-프레미스 디렉터리에 대한 비밀번호 쓰기 저장

Azure Portal를 사용 하 여 비밀 번호 쓰기 저장을 사용 하도록 설정할 수 있습니다. Azure AD Connect를 다시 구성할 필요 없이 일시적으로 비밀 번호 쓰기 저장을 사용 하지 않도록 설정할 수도 있습니다.

* 옵션을 **예**로 설정 하면 쓰기 저장을 사용할 수 있습니다. 페더레이션된, 통과 인증 또는 암호 해시 동기화 된 사용자는 암호를 다시 설정할 수 있습니다.
* 옵션을 **아니요**로 설정 하면 쓰기 저장을 사용할 수 없습니다. 페더레이션된, 통과 인증 또는 암호 해시 동기화 된 사용자는 암호를 재설정할 수 없습니다.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>사용자가 해당 암호를 재설정하지 않고 계정의 잠금을 해제할 수 있음

기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다. 유연성을 제공 하기 위해 사용자가 암호를 재설정 하지 않고도 온-프레미스 계정의 잠금을 해제할 수 있도록 선택할 수 있습니다. 이러한 두 작업을 구분 하려면이 설정을 사용 합니다.

* **예**로 설정 된 경우 사용자에 게 암호를 재설정 하 고 계정의 잠금을 해제 하거나 암호를 다시 설정 하지 않고 계정의 잠금을 해제할 수 있는 옵션이 제공 됩니다.
* **아니요**로 설정 하면 사용자는 결합 된 암호 재설정 및 계정 잠금 해제 작업만 수행할 수 있습니다.

### <a name="on-premises-active-directory-password-filters"></a>온-프레미스 Active Directory 암호 필터

SSPR은 Active Directory에서 관리자가 시작한 암호 재설정에 해당 하는 기능을 수행 합니다. 타사 암호 필터를 사용 하 여 사용자 지정 암호 규칙을 적용 하 고 Azure AD 셀프 서비스 암호 재설정 중에이 암호 필터를 확인 해야 하는 경우 타사 암호 필터 솔루션이 관리자 암호 재설정 시나리오에 적용 되도록 구성 되어 있는지 확인 합니다. [Active Directory Domain Services에 대 한 AZURE AD 암호 보호](concept-password-ban-bad-on-premises.md) 는 기본적으로 지원 됩니다.

## <a name="password-reset-for-b2b-users"></a>B2B 사용자에 대한 암호 재설정

암호 재설정 및 변경은 모든 B2B(기업 간 전자 상거래) 구성에서 완전히 지원됩니다. B2B 사용자 암호 재설정은 다음 세 가지 경우에 지원됩니다.

* **기존 AZURE ad 테 넌 트를 사용 하는 파트너 조직의 사용자**: 파트너를 사용 하는 조직에 기존 azure ad 테 넌 트가 있는 경우 해당 테 넌 트에서 사용 하도록 설정 된 모든 암호 재설정 정책을 적용 합니다. 암호 재설정이 작동하려면 파트너 조직에서 Azure AD SSPR을 사용하도록 설정되어 있는지 확인해야 합니다. Office 365 고객에 게는 추가 요금이 부과 되지 않습니다.
* 셀프 서비스 등록을 **통해 등록** 하는 사용자: 파트너가 파트너에 게 제공 하는 [셀프 서비스 등록](../users-groups-roles/directory-self-service-signup.md) 기능을 사용 하 여 테 넌 트에 등록 하는 경우 등록 한 전자 메일을 사용 하 여 암호를 다시 설정할 수 있습니다.
* **B2B 사용자**: 새 [Azure AD B2B 기능](../external-identities/what-is-b2b.md) 을 사용 하 여 만든 모든 새 b2b 사용자는 초대 프로세스 중에 등록 한 전자 메일을 사용 하 여 암호를 재설정할 수도 있습니다.

이 시나리오를 테스트하려면 이러한 파트너 사용자 중 하나로 https://passwordreset.microsoftonline.com으로 이동합니다. 대체 전자 메일 또는 인증 전자 메일이 정의되어 있으면 암호 재설정이 예상대로 작동합니다.

> [!NOTE]
> Azure AD 테 넌 트에 대 한 게스트 액세스 권한이 부여 된 Microsoft 계정 (예: Hotmail.com, Outlook.com 또는 기타 개인 전자 메일 주소)은 Azure AD SSPR를 사용할 수 없습니다. [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에 있는 정보를 사용하여 암호를 다시 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

SSPR을 시작 하려면 다음 자습서를 완료 하세요.

> [!div class="nextstepaction"]
> [자습서: 셀프 서비스 암호 재설정 사용 (SSPR)](tutorial-enable-sspr.md)

다음 문서에서는 Azure AD를 통한 암호 재설정에 대한 추가 정보를 제공합니다.

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "사용 가능한 Azure AD 인증 방법 및 필요한 수량"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Azure Portal에서 SSPR 등록 옵션 구성"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Azure Portal의 SSPR에 대 한 온-프레미스 통합"