---
title: 인증 방법-Azure Active Directory
description: MFA 및 SSPR 용 Azure AD에서 사용할 수 있는 인증 방법
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee0dd0cd83ab27dd728a7572b6fcd69c40bb1b00
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848751"
---
# <a name="what-are-authentication-methods"></a>인증 방법이란?

관리자는 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정 (SSPR)에 대 한 인증 방법을 선택 하는 것이 좋습니다. 사용자가 여러 인증 방법을 등록 하도록 요구 하는 것이 좋습니다. 사용자에 대 한 인증 방법을 사용할 수 없는 경우 다른 방법으로 인증 하도록 선택할 수 있습니다.

관리자는 SSPR 및 MFA 사용자에게 제공하는 인증 방법을 정책에서 정의할 수 있습니다. 일부 기능에는 일부 인증 방법이 제공되지 않을 수 있습니다. 정책을 구성 하는 방법에 대 한 자세한 내용은 [셀프 서비스 암호 재설정을 성공적으로 출시 하는 방법](howto-sspr-deployment.md) 및 [클라우드 기반 Azure Multi-Factor Authentication 계획](howto-mfa-getstarted.md) 문서를 참조 하세요.

관리자는 사용자가 액세스 권한이 없는 경우 필요한 최소 인증 방법 수보다 많이 선택할 수 있게 하는 것이 좋습니다.

|인증 방법|사용량|
| --- | --- |
| 암호 | MFA 및 SSPR |
| 보안 질문 | SSPR만 |
| 메일 주소 | SSPR만 |
| Microsoft Authenticator 앱 | MFA 및 SSPR |
| OATH 하드웨어 토큰 | MFA 및 SSPR용 공개 미리 보기 |
| SMS | MFA 및 SSPR |
| 음성 통화 | MFA 및 SSPR |
| 앱 암호 | 특정 경우 MFA만 |

![로그인 화면에서 사용하는 인증 방법](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| MFA 및 SSPR의 OATH 하드웨어 토큰은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

## <a name="password"></a>암호

Azure AD 암호는 인증 방법으로 간주됩니다. **비활성화할 수 없는** 하나의 메서드입니다.

## <a name="security-questions"></a>보안 질문

보안 질문은 **Azure AD SSPR(Self-service Password Reset)** 에서만 관리자가 아닌 계정에 제공됩니다.

보안 질문을 사용하는 경우 다른 방법과 함께 사용하는 것이 좋습니다. 어떤 사람들이 다른 사용자의 질문에 대한 답을 알고 있을 수도 있으므로 보안 질문은 다른 방법보다 덜 안전할 수 있습니다.

> [!NOTE]
> 보안 질문은 디렉터리에서 사용자 개체에 대해 비공개적으로 안전하게 저장되며 등록하는 동안 사용자만이 답변할 수 있습니다. 관리자는 사용자의 질문 또는 대답을 읽거나 수정할 수 없습니다.
>

### <a name="predefined-questions"></a>미리 정의된 질문

* 배우자/파트너를 처음 만난 도시는 어디인가요?
* 부모님이 처음 만난 도시는 어디인가요?
* 가장 가까운 형제 자매가 사는 도시는 어디인가요?
* 아버지가 출생하신 도시는 어디인가요?
* 첫 직장이 있는 도시는 어디인가요?
* 어머니가 출생하신 도시는 어디인가요?
* 2000년에 새해를 맞은 도시는 어디인가요?
* 고등학교에서 가장 좋아했던 선생님의 성은 무엇인가요?
* 지원했지만 다니지 않은 대학의 이름은 무엇인가요?
* 첫 번째 결혼 피로연을 열었던 장소의 이름은 무엇인가요?
* 아버지의 중간 이름은 무엇인가요?
* 가장 좋아하는 음식은 무엇인가요?
* 외할머니의 성함은 무엇인가요?
* 어머니의 중간 이름은 무엇인가요?
* 가장 손위인 형제 자매의 생년월일은 언제인가요? (예: 1985년 11월)
* 가장 손위인 형제 자매의 중간 이름은 무엇인가요?
* 친할아버지의 성함은 무엇인가요?
* 가장 손아래인 형제 자매의 중간 이름은 무엇인가요?
* 6학년 때 다닌 학교는 어디인가요?
* 어린 시절 베스트 프렌드의 성함은 무엇인가요?
* 첫 번째 배우자의 성함은 무엇인가요?
* 가장 좋아하는 초등학교 선생님의 성은 무엇인가요?
* 첫 번째 자동차 또는 오토바이의 제조업체와 모델은 무엇인가요?
* 처음 다닌 학교의 이름은 무엇인가요?
* 태어난 병원의 이름은 무엇인가요?
* 어린 시절 첫 번째 집의 거리 이름은 무엇인가요?
* 어린 시절 영웅의 이름은 무엇인가요?
* 좋아하는 봉제 인형의 이름은 무엇인가요?
* 첫 번째 애완동물의 이름은 무엇인가요?
* 어린 시절 별명은 무엇인가요?
* 고등학교 때 가장 좋아한 스포츠는 무엇인가요?
* 첫 번째 직업은 무엇인가요?
* 어린 시절 전화 번호의 마지막 4자리는 무엇인가요?
* 어린 시절 자라서 되고 싶었던 것은 무엇인가요?
* 지금까지 만난 가장 유명한 사람은 누구인가요?

미리 정의된 모든 보안 질문은 모두 사용자의 브라우저 로캘을 기반으로 Office 365 언어의 전체 집합으로 번역 및 지역화됩니다.

### <a name="custom-security-questions"></a>사용자 지정 보안 질문

사용자 지정 보안 질문은 지역화되지 않습니다. 모든 사용자 지정 질문은 사용자의 브라우저 로캘이 다른 경우에도 관리 사용자 인터페이스에 입력한 언어와 동일한 언어로 표시됩니다. 지역화된 질문이 필요한 경우 미리 정의된 질문을 사용해야 합니다.

사용자 지정 보안 질문은 최대 200자입니다.

### <a name="security-question-requirements"></a>보안 질문 요구 사항

* 최소 응답 문자 제한은 3자입니다.
* 최대 응답 문자 제한은 40자입니다.
* 사용자는 동일한 질문에 대해 한 번만 응답할 수 있습니다.
* 사용자는 둘 이상의 질문에 대해 동일한 응답을 제공할 수 없습니다.
* 유니코드 문자를 포함한 모든 문자 집합을 사용하여 질문과 응답을 정의할 수 있습니다.
* 정의된 질문 수는 등록에 필요한 질문 수보다 크거나 같아야 합니다.

## <a name="email-address"></a>메일 주소

이메일 주소는 **Azure AD SSPR(Self-service Password Reset)** 에서만 사용할 수 있습니다.

액세스하기 위해 사용자의 Azure AD 암호를 요청하지 않는 이메일 계정을 사용하는 것이 좋습니다.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 앱

Microsoft Authenticator 앱은 Microsoft 계정의 Azure AD 회사 또는 학교 계정에 추가 보안 수준을 제공합니다.

Microsoft Authenticator 앱은 [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) 및 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)에서 사용할 수 있습니다.

> [!NOTE]
> 사용자에게는 셀프 서비스 암호 재설정 등록을 위해 모바일 앱을 등록하는 옵션이 없습니다. 대신에 사용자는 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에서 또는 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)의 보안 정보 등록 미리 보기에서 자신의 모바일 앱을 등록할 수 있습니다.
>

### <a name="notification-through-mobile-app"></a>모바일 앱을 통한 알림

Microsoft Authenticator 앱을 사용하면 스마트폰 또는 태블릿에 알림을 표시하여 계정에 대한 무단 액세스를 방지하고 사기성 트랜잭션을 중지할 수 있습니다. 사용자가 알림을 확인하고 올바르면 확인을 선택합니다. 그렇지 않으면 거부를 선택할 수 있습니다.

> [!WARNING]
> 재설정에 한 가지 방법만 필요하고 셀프 서비스 암호 재설정을 사용하는 경우 **최고 수준의 보안을 유지하기 위해** 사용자에게 유일한 옵션으로 확인 코드만 제공됩니다.
>
> 두 방법이 필수인 경우 다른 활성화된 방법과 함께 **알림** 또는 **확인** 코드를 사용하여 재설정할 수 있습니다.
>

모바일 앱을 통한 알림 및 모바일 앱의 확인 코드 둘 다 사용하도록 설정한 경우 알림을 사용하여 Microsoft Authenticator 앱을 등록하려는 사용자는 알림 및 코드 모두를 사용하여 해당 ID를 확인할 수 있습니다.

> [!NOTE]
> 조직에서 근무 하는 직원이 있거나 중국으로 여행 하는 경우 **Android 장치** 에서 **모바일 앱 방법을 통한 알림이** 해당 국가에서 작동 하지 않습니다. 이러한 사용자에 대해 다른 방법을 사용할 수 있습니다.

### <a name="verification-code-from-mobile-app"></a>모바일 앱의 확인 코드

Microsoft Authenticator 앱 또는 타사 앱을 소프트웨어 토큰으로 사용하여 OATH 확인 코드를 생성할 수 있습니다. 사용자 이름 및 암호를 입력한 후 앱에서 제공한 코드를 로그인 화면에 입력합니다. 확인 코드는 두 번째 인증 형식을 제공합니다.

> [!WARNING]
> 재설정에 한 가지 방법만 필요하고 셀프 서비스 암호 재설정을 사용하는 경우 **최고 수준의 보안을 유지하기 위해** 사용자에게 유일한 옵션으로 확인 코드만 제공됩니다.
>

사용자는 언제 든 지 사용 하도록 구성 된 Microsoft Authenticator 앱과 같은 최대 5 개의 OATH 하드웨어 토큰 또는 인증자 응용 프로그램을 조합 하 여 사용할 수 있습니다.

## <a name="oath-hardware-tokens-public-preview"></a>OATH 하드웨어 토큰(공개 미리 보기)

OATH는 OTP(일회성 암호) 코드 생성 방법을 지정하는 공개 표준입니다. Azure AD는 30초 또는 60초 중 하나로 OATH-TOTP SHA-1 토큰 사용을 지원합니다. 고객은 자신이 선택한 공급업체에서 이러한 토큰을 확보할 수 있습니다. 비밀 키는 모든 토큰과 호환 되지 않을 수 있는 128 자로 제한 됩니다. 비밀 키를 Base32로 인코딩해야 합니다.

![MFA 서버 OATH 토큰 블레이드에 OATH 토큰 업로드](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

OATH 하드웨어 토큰은 공개 미리 보기의 일부로 지원됩니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

토큰이 확보되면 아래 예제와 같이 UPN, 일련 번호, 비밀 키, 시간 간격, 제조업체 및 모델이 포함된 CSV(쉼표로 구분된 값) 파일 형식으로 업로드해야 합니다.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> 위와 같이 CSV 파일에 머리글 행을 포함해야 합니다.

CSV 파일로 올바르게 형식이 지정되면 관리자는 Azure Portal에 로그인하여 **Azure Active Directory**, **MFA 서버**, **OATH 토큰**으로 이동하여 CSV 파일을 업로드할 수 있습니다.

CSV 파일의 크기에 따라 처리하는 데 몇 분 정도가 소요될 수 있습니다. 현재 상태를 가져오려면 **새로 고침** 단추를 클릭합니다. 파일에 오류가 있는 경우 오류가 나열된 CSV 파일을 다운로드하여 해결할 수 있습니다.

오류가 모두 처리되면 관리자는 토큰이 활성화되도록 **활성화**를 클릭하고 토큰에 표시된 OTP를 입력하여 각 키를 활성화할 수 있습니다.

사용자는 언제 든 지 사용 하도록 구성 된 Microsoft Authenticator 앱과 같은 최대 5 개의 OATH 하드웨어 토큰 또는 인증자 응용 프로그램을 조합 하 여 사용할 수 있습니다.

## <a name="phone-options"></a>전화 옵션

### <a name="mobile-phone"></a>휴대폰

휴대폰 사용자에게는 두 옵션이 제공됩니다.

사용자가 자신의 휴대폰 번호를 디렉터리에는 표시하지 않는 대신 암호 재설정에는 사용하도록 하려면 관리자가 디렉터리에 해당 휴대폰 번호를 채우지 않아야 합니다. 사용자가 [암호 재설정 등록 포털](https://aka.ms/ssprsetup)을 통해 **인증 전화** 특성을 채워야 합니다. 관리자는 사용자의 프로필에서 이 정보를 볼 수 있지만 다른 곳에 게시되지는 않습니다.

올바르게 작동하려면 전화 번호가 *+국가코드 전화번호* 형식으로 저장되어야 합니다(예: +1 4255551234).

> [!NOTE]
> 국가 번호와 전화 번호 사이에 공백이 필요합니다.
>
> 암호 재설정은 전화 번호 확장을 지원하지 않습니다. +1 4255551234X12345 형식에서도 전화를 걸지 전에 확장이 제거됩니다.

Microsoft는 동일한 번호를 통한 일관적인 SMS 또는 음성 기반 Multi-Factor Authentication 즉시 이행을 보장하지 않습니다. 사용자를 위해, SMS 이행성을 향상하기 위한 조정 작업을 수시로 진행하고 있는 Microsoft는 언제든지 짧은 코드를 추가하거나 제거할 수 있습니다. Microsoft는 미국 및 캐나다 외에도 국가/지역에 대 한 간단한 코드를 지원 하지 않습니다.

#### <a name="text-message"></a>문자 메시지

확인 코드가 담긴 SMS가 휴대폰 번호로 전송됩니다. 계속하려면 로그인 인터페이스에 제공한 확인 코드를 입력합니다.

#### <a name="phone-call"></a>전화 통화

제공한 전화 번호에 자동으로 음성 전화를 겁니다. 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다.

> [!IMPORTANT]
> 2019 년 3 월부터, 무료/평가판 Azure AD 테넌트의 MFA 및 SSPR 사용자가 전화 통화 옵션을 사용할 수 없습니다. SMS 메시지는 이러한 변경의 영향을 받지 않습니다. 전화 통화는 유료 Azure AD 테넌트의 사용자가 계속 사용할 수 있습니다. 이 변경 내용은 무료/평가판 Azure AD 테넌트에만 영향을 줍니다.

### <a name="office-phone"></a>사무실 전화

제공한 전화 번호에 자동으로 음성 전화를 겁니다. 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다.

올바르게 작동하려면 전화 번호가 *+국가코드 전화번호* 형식으로 저장되어야 합니다(예: +1 4255551234).

사무실 전화 특성은 관리자가 관리합니다.

> [!IMPORTANT]
> 2019 년 3 월부터, 무료/평가판 Azure AD 테넌트의 MFA 및 SSPR 사용자가 전화 통화 옵션을 사용할 수 없습니다. SMS 메시지는 이러한 변경의 영향을 받지 않습니다. 전화 통화는 유료 Azure AD 테넌트의 사용자가 계속 사용할 수 있습니다. 이 변경 내용은 무료/평가판 Azure AD 테넌트에만 영향을 줍니다.

> [!NOTE]
> 국가 번호와 전화 번호 사이에 공백이 필요합니다.
>
> 암호 재설정은 전화 번호 확장을 지원하지 않습니다. +1 4255551234X12345 형식에서도 전화를 걸지 전에 확장이 제거됩니다.

### <a name="troubleshooting-phone-options"></a>전화 옵션 문제 해결

전화 번호를 사용 하는 인증 방법과 관련 된 일반적인 문제:

* 단일 장치에서 차단 된 호출자 ID
   * 장치 문제 해결
* 잘못 된 전화 번호, 잘못 된 국가 코드, 집 전화번호 및 회사 전화 번호
   * 사용자 개체 및 구성 된 인증 방법 문제를 해결 합니다. 올바른 전화 번호가 등록 되어 있는지 확인 합니다.
* 잘못 된 PIN 입력
   * 사용자가 Azure MFA 서버에 등록 된 올바른 PIN을 사용 했는지 확인 합니다.
* 음성 메일로 전달 된 통화
   * 사용자가 전화를 켜고 해당 지역에서 해당 서비스를 사용할 수 있는지 확인 하거나 대체 방법을 사용 합니다.
* 사용자가 차단됨
   * 관리자가 Azure Portal에서 사용자의 차단을 해제 하도록 합니다.
* SMS가 장치에서 구독 하지 않음
   * 사용자가 장치에서 메서드를 변경 하거나 SMS를 활성화 하도록 합니다.
* 잘못 된 통신 공급자 (전화 입력 없음, 누락 된 DTMF 톤 문제, 여러 장치에서 차단 된 호출자 ID 또는 여러 장치에서 차단 된 SMS)
   * Microsoft는 여러 통신 공급자를 사용 하 여 인증을 위해 전화 통화와 SMS 메시지를 라우팅합니다. 위의 문제가 발생 한 경우 사용자가 5 분 이내에 5 번 이상 메서드를 사용 하 고 Microsoft 지원에 문의할 때 해당 사용자의 정보를 사용할 수 있도록 합니다.

## <a name="app-passwords"></a>앱 암호

특정 비 브라우저 앱은 다단계 인증을 지원하지 않으며, 다단계 인증에 대해 활성화되어 있는 사용자가 비 브라우저 앱을 사용하려 하면 인증할 수 없습니다. 앱 암호는 사용자가 인증을 계속할 수 있게 합니다.

사용자별 MFA를 통하지 않고 조건부 액세스 정책을 통해 Multi-Factor Authentication을 적용하는 경우 앱 암호를 만들 수 없습니다. 조건부 액세스 정책을 사용하여 액세스를 제어하는 애플리케이션은 앱 암호가 필요하지 않습니다.

조직이 Azure AD를 사용하여 SSO에 대해 페더레이션되고 Azure MFA를 사용할 계획인 경우 다음 사항에 유의하세요.

* 이 앱 암호는 Azure AD에서 확인되므로 페더레이션을 바이패스합니다. 앱 암호를 설정할 때에만 페더레이션이 사용됩니다. 페더레이션된(SSO) 사용자의 경우 암호는 조직 ID에 저장됩니다. 사용자가 회사를 떠나는 경우 해당 정보는 DirSync를 사용하는 조직 ID에 유입되어야 합니다. 계정 사용 안 함/삭제 설정은 동기화에 최대 3시간이 걸리며 Azure AD에서 앱 암호의 사용 안 함/삭제가 지연됩니다.
* 앱 암호를 사용할 경우 온-프레미스 클라이언트 Access Control 설정은 적용되지 않습니다.
* 온-프레미스 인증 로깅/감사 기능은 앱 암호에 사용할 수 없습니다
* 특정 고급 아키텍처 디자인은 클라이언트와 2단계 인증을 사용하는 경우 인증 위치에 따라 조직의 사용자 이름과 암호 및 앱 암호를 조합하여 사용할 필요가 있습니다. 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 조직의 사용자 이름과 암호를 사용합니다. Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호를 사용합니다.
* 기본적으로 사용자가 앱 암호를 만들 수 없습니다. 사용자가 앱 암호를 만들 수 있도록 해야 할 경우 서비스 설정에서 **사용자가 브라우저에 기반하지 않는 애플리케이션에 로그인하기 위해 앱 암호를 만들 수 있음** 옵션을 선택합니다.

## <a name="next-steps"></a>다음 단계

[조직에 대해 셀프 서비스 암호 재설정을 사용하도록 설정](quickstart-sspr.md)

[조직에 Azure Multi-Factor Authentication을 사용하도록 설정](howto-mfa-getstarted.md)

[테넌트에서 결합 된 등록 사용](howto-registration-mfa-sspr-combined.md)

[최종 사용자 인증 방법 구성 설명서](https://aka.ms/securityinfoguide)
