---
title: Azure Multi-Factor Authentication 구성 - Azure Active Directory
description: Azure Portal에서 Azure Multi-Factor Authentication에 대 한 설정을 구성 하는 방법을 알아봅니다.
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
ms.custom: contperfq4
ms.openlocfilehash: 1bc3f7887c9d257f5971b867ff9b7b1dd970fa87
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179406"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure Multi-Factor Authentication 구성 설정

Azure Multi-Factor Authentication에 대 한 최종 사용자 환경을 사용자 지정 하려면 계정 잠금 임계값, 사기 행위 경고 및 알림과 같은 설정에 대 한 옵션을 구성할 수 있습니다. 일부 설정은 Azure Active Directory (Azure AD)에 대 한 Azure Portal에 직접 있으며 일부 설정은 별도의 Azure Multi-Factor Authentication 포털에 있습니다.

Azure Portal에서 사용할 수 있는 Azure Multi-Factor Authentication 설정은 다음과 같습니다.

| 기능 | Description |
| ------- | ----------- |
| [계정 잠금](#account-lockout) | 한 행에 거부 된 인증 시도가 너무 많으면 Azure Multi-Factor Authentication를 사용 하지 못하도록 일시적으로 계정을 잠급니다. 이 기능은 인증을 위해 PIN을 입력하는 사용자에게만 적용됩니다. (MFA 서버) |
| [사용자 차단/차단 해제](#block-and-unblock-users) | 특정 사용자가 Azure Multi-Factor Authentication 요청을 받을 수 없도록 차단 합니다. 차단된 사용자에 대한 모든 인증 시도가 자동으로 거부됩니다. 사용자는 차단 된 시간부터 90 일 동안 차단 된 상태로 유지 되거나 수동으로 차단 해제 됩니다. |
| [사기 행위 경고](#fraud-alert) | 사용자가 사기성 확인 요청을 보고할 수 있도록 하는 설정을 구성 합니다. |
| [알림](#notifications) | MFA 서버의 이벤트 알림이 가능하도록 설정합니다. |
| [OATH 토큰](concept-authentication-methods.md#oath-tokens) | 클라우드 기반 Azure MFA 환경에 사용되어 사용자의 OATH 토큰을 관리합니다. |
| [전화 통화 설정](#phone-call-settings) | 클라우드 및 온-프레미스 환경의 인사말 및 전화 통화 관련 설정을 구성합니다. |
| 공급자 | 계정과 연관이 있을 수 있는 기존 인증 공급자를 표시합니다. 2018년 9월 1일부로 새 인증 공급자를 생성할 수 없습니다. |

![Azure Portal - Azure AD Multi-Factor Authentication 설정](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>계정 잠금

공격 중에 반복 되는 MFA 시도를 방지 하기 위해 계정 잠금 설정을 사용 하 여 일정 기간 동안 계정이 잠길 때까지 허용 되는 실패 횟수를 지정할 수 있습니다. 계정 잠금 설정은 MFA 프롬프트에 대해 pin 코드를 입력 하는 경우에만 적용 됩니다.

다음 설정을 사용할 수 있습니다.

* 계정 잠금을 트리거하기 위한 MFA 거부 수
* 계정 잠금 카운터가 다시 설정 될 때 까지의 시간 (분)
* 계정이 자동으로 차단 해제 될 때까지 분

계정 잠금 설정을 구성 하려면 다음 설정을 완료 합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**  >  **보안**  >  **MFA**  >  **계정 잠금**으로 이동 합니다.
1. 사용자 환경에 필요한 값을 입력 하 고 **저장**을 선택 합니다.

    ![Azure Portal 계정 잠금 설정의 스크린샷](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>사용자 차단 및 차단 해제

사용자의 장치를 분실 하거나 도난당 한 경우에는 연결 된 계정에 대 한 인증 시도를 차단할 수 있습니다. 차단된 사용자에 대한 모든 인증 시도가 자동으로 거부됩니다. 사용자는 차단된 시간 이후 90일 동안 차단된 상태로 유지됩니다.

### <a name="block-a-user"></a>사용자 차단

사용자를 차단 하려면 다음 단계를 완료 합니다.

1. **Azure Active Directory** > **보안** > **MFA** > **사용자 차단/차단 해제**로 이동합니다.
1. **추가**를 선택하여 사용자를 차단합니다.
1. **복제 그룹**을 선택 하 고 *Azure 기본값*을 선택 합니다.

    차단 된 사용자의 사용자 이름을로 입력 `username\@domain.com` 하 고 *이유* 필드에 주석을 입력 합니다.
1. 준비가 되 면 **확인** 을 선택 하 여 사용자를 차단 합니다.

### <a name="unblock-a-user"></a>사용자 차단 해제

사용자를 차단 해제 하려면 다음 단계를 완료 합니다.

1. **Azure Active Directory** > **보안** > **MFA** > **사용자 차단/차단 해제**로 이동합니다.
1. 원하는 사용자 옆의 *작업* 열에서 **차단 해제**를 선택 합니다.
1. *차단 해제 이유* 필드에서 주석을 입력합니다.
1. 준비가 되 면 **확인** 을 선택 하 여 사용자를 차단 해제 합니다.

## <a name="fraud-alert"></a>사기 행위 경고

사기 행위 경고 기능을 사용 하면 사용자가 자신의 리소스에 액세스 하려는 사기성 시도를 보고할 수 있습니다. 알 수 없고 의심 스러운 MFA 프롬프트가 수신 되 면 사용자는 Microsoft Authenticator 앱을 사용 하거나 휴대폰을 통해 사기 행위를 보고할 수 있습니다.

다음 사기 행위 경고 구성 옵션을 사용할 수 있습니다.

* **사기 행위를 보고 하는 사용자를 자동으로 차단**: 사용자가 사기 행위를 보고 하는 경우 해당 계정은 90 일 동안 또는 관리자가 계정을 차단 해제할 때까지 차단 됩니다. 관리자는 로그인 보고서를 사용하여 로그인을 검토하고 향후 사기를 예방하기 위해 적절한 조치를 취할 수 있습니다. 그런 다음 관리자는 사용자의 계정을 [차단 해제](#unblock-a-user)할 수 있습니다.
* **초기 인사말 중 사기 행위를 보고 하는 코드**: 사용자가 multi-factor authentication을 수행 하는 전화 통화를 받으면 일반적으로를 눌러 **#** 로그인을 확인 합니다. 사기 행위를 보고하려면 사용자가 **#** 를 누르기 전에 코드를 입력합니다. 이 코드의 기본값은 **0**이지만, 사용자가 지정할 수 있습니다.

   > [!NOTE]
   > Microsoft의 기본 음성 인사말은 사용자가 사기 행위 경고를 제출하기 위해 **0#** 키를 누르도록 지시합니다. **0**이 아닌 코드를 사용하려는 경우 사용자에게 적절한 지침과 함께 고유의 사용자 지정 음성 인사말을 기록하고 업로드해야 합니다.

사기 행위 경고를 사용 하도록 설정 하 고 구성 하려면 다음 단계를 완료 합니다.

1. **Azure Active Directory** > **보안** > **MFA** > **사기 행위 경고**로 이동합니다.
1. *사기 행위 경고 제출 허용* 설정을 **켜기**로 설정합니다.
1. 사기 행위를 보고 하는 사용자 또는 *초기 인사말 중에 사기 행위를 보고 하는 코드를* 원하는 대로 *자동으로 차단* 을 구성 합니다.
1. 준비되면 **저장**을 선택합니다.

### <a name="view-fraud-reports"></a>사기 행위 보고 보기

**Azure Active Directory** > **로그인** > **인증 세부 정보**를 선택합니다. 사기 행위 보고서는 이제 표준 Azure AD 로그인 보고서의 일부이며 MFA가 거부되고 사기 행위 코드가 입력됨에 따라 **"결과 세부 정보"** 로 표시됩니다.
 
## <a name="notifications"></a>공지

사용자가 사기 행위 경고를 보고할 때 전자 메일 알림을 구성할 수 있습니다. 이러한 알림은 일반적으로 사용자의 계정 자격 증명이 손상 될 수 있으므로 id 관리자에 게 전송 됩니다. 다음 예에서는 사기 행위 경고 알림 전자 메일의 모양을 보여 줍니다.

![사기 행위 경고 알림 전자 메일 예제](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

사기 행위 경고 알림을 구성 하려면 다음 설정을 완료 합니다.

1. **Azure Active Directory**  >  **보안**  >  **Multi-Factor Authentication**  >  **알림**으로 이동 합니다.
1. 다음 상자에 추가할 전자 메일 주소를 입력 합니다.
1. 기존 전자 메일 주소를 제거 하려면 원하는 메일 주소 옆의 **...** 옵션을 선택한 다음 **삭제**를 선택 합니다.
1. 준비되면 **저장**을 선택합니다.

## <a name="oath-tokens"></a>OATH 토큰

Azure AD는 30초 또는 60초마다 코드를 새로 고치는 OATH-TOTP SHA-1 토큰의 사용을 지원합니다. 고객은 자신이 선택한 공급업체에서 이러한 토큰을 구매할 수 있습니다.

OATH TOTP 하드웨어 토큰은 일반적으로 토큰에서 사전 프로그래밍된 비밀 키 또는 시드를 제공합니다. 이러한 키는 다음 단계에 설명된 대로 Azure AD에 입력해야 합니다. 비밀 키는 128자로 제한되며 일부 토큰과는 호환되지 않을 수 있습니다. 비밀 키에는 *a-z* 또는 *A-Z*와 *1-7*까지의 숫자만 포함할 수 있으며, *Base32*로 인코딩해야 합니다.

다시 시드될 수 있는 프로그래밍 가능한 OATH TOTP 하드웨어 토큰은 소프트웨어 토큰 설정 흐름에서 Azure AD를 사용하여 설정할 수도 있습니다.

OATH 하드웨어 토큰은 공개 미리 보기의 일부로 지원됩니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

![OATH 토큰을 MFA OATH 토큰 블레이드에 업로드](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

토큰이 확보되면 아래 예제와 같이 UPN, 일련 번호, 비밀 키, 시간 간격, 제조업체 및 모델이 포함된 CSV(쉼표로 구분된 값) 파일 형식으로 업로드해야 합니다.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> CSV 파일에 머리글 행을 포함해야 합니다.

CSV 파일로 올바르게 형식이 지정되면 관리자는 Azure Portal에 로그인하여 **Azure Active Directory > 보안 > MFA > OATH 토큰**으로 이동하고 CSV 파일을 업로드할 수 있습니다.

CSV 파일의 크기에 따라 처리하는 데 몇 분 정도가 소요될 수 있습니다. 현재 상태를 가져오려면 **새로 고침** 단추를 선택합니다. 파일에 오류가 있는 경우 오류가 나열된 CSV 파일을 다운로드하여 해결할 수 있습니다. 다운로드한 CSV 파일의 필드 이름은 업로드된 버전과 다릅니다.

오류가 모두 처리되면 관리자는 토큰에 대해 **활성화**를 선택하고 토큰에 표시된 OTP를 입력하여 각 키를 활성화할 수 있습니다.

사용자는 언제든지 사용할 수 있도록 구성된 Microsoft Authenticator 앱과 같은 인증자 애플리케이션 또는 최대 5개의 OATH 하드웨어 토큰을 조합할 수 있습니다.

## <a name="phone-call-settings"></a>전화 통화 설정

사용자가 MFA 프롬프트에 대 한 전화 통화를 수신 하는 경우 해당 사용자의 경험 (예: 발신자 ID 또는 음성 인사말)을 구성할 수 있습니다.

미국에서 MFA 호출자 ID를 구성 하지 않은 경우 Microsoft의 음성 통화는 다음 번호에서 제공 됩니다. 스팸 필터를 사용 하는 경우 다음 숫자를 제외 해야 합니다.

* *+ 1 (866) 539 4191*
* *+ 1 (855) 330 8653*
* *+ 1 (877) 668 6536*

> [!NOTE]
> Azure Multi-Factor Authentication 호출이 공용 전화 네트워크를 통해 전달 되는 경우에는 호출자 ID를 지원 하지 않는 통신 회사를 통해 라우팅되는 경우도 있습니다. 이로 인해 Azure Multi-Factor Authentication에서 항상 전송 하더라도 호출자 ID는 보장 되지 않습니다.

사용자 고유의 호출자 ID 번호를 구성 하려면 다음 단계를 완료 합니다.

1. **Azure Active Directory** > **보안** > **MFA** > **전화 통화 설정**으로 이동합니다.
1. 사용자가 휴대폰에서 볼 수 있도록 **MFA 호출자 ID 번호** 를 설정 합니다. 미국 기반 번호만 허용됩니다.
1. 준비되면 **저장**을 선택합니다.

### <a name="custom-voice-messages"></a>사용자 지정 음성 메시지

사용자 지정 음성 메시지 기능을 사용 하 여 Azure Multi-Factor Authentication에 대 한 사용자 고유의 녹음/녹화 또는 인사말을 사용할 수 있습니다. 이러한 메시지는 또는 기본 Microsoft 녹음/바꾸기와 함께 사용할 수 있습니다.

이 작업을 시작하기 전에 다음 제한 사항을 고려하세요.

* 지원 되는 파일 형식은 *.wav* 및 *. mp3*입니다.
* 파일 크기는 1MB로 제한됩니다.
* 인증 메시지 길이는 20초 미만이어야 합니다. 메시지가 20초보다 길면 인증이 실패할 수 있습니다. 메시지가 완료되고 인증이 시간 초과되기 전에 사용자가 응답하지 않을 수 있습니다.

### <a name="custom-message-language-behavior"></a>사용자 지정 메시지 언어 동작

사용자 지정 음성 메시지를 사용자에 게 재생 하는 경우 메시지의 언어는 다음과 같은 요인에 따라 달라 집니다.

* 현재 사용자의 언어.
  * 사용자 브라우저에서 검색된 언어.
  * 다른 인증 시나리오는 다르게 동작할 수 있습니다.
* 사용 가능한 사용자 지정 메시지의 언어.
  * 이 언어는 사용자 지정 메시지가 추가될 때 관리자가 선택합니다.

예를 들어, 독일어가 포함된 사용자 지정 메시지가 하나만 있는 경우 다음과 같습니다.

* 독일어로 인증하는 사용자는 사용자 지정 독일어 메시지를 듣게 됩니다.
* 영어로 인증하는 사용자는 표준 영어 메시지를 듣게 됩니다.

### <a name="custom-voice-message-defaults"></a>사용자 지정 음성 메시지 기본값

다음 샘플 스크립트를 사용 하 여 사용자 지정 메시지를 만들 수 있습니다. 사용자 지정 메시지를 구성 하지 않은 경우에는 이러한 구문이 기본값입니다.

| 메시지 이름 | 스크립트 |
| --- | --- |
| 인증 성공 | 로그인이 확인 되었습니다. 안녕히 계세요. |
| 확장 프롬프트 | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 계속하려면 우물정자를 누르세요. |
| 사기 행위 확인 | 사기 행위 경고를 제출했습니다. 차단된 계정을 해제하려면 회사의 IT 지원 센터에 문의하세요. |
| 사기 인사말(표준) | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 확인을 마치려면 우물정자를 누르세요. 이 확인을 시작하지 않은 경우 누군가가 계정에 액세스하려고 할 수 있습니다. 사기 행위 경고를 제출하려면 0 우물정자를 누릅니다. 이렇게 하면 회사의 IT 팀에 알리고 추가 인증 시도를 차단합니다. |
| 사기 행위가 보고됨    사기 행위 경고를 제출했습니다. | 차단된 계정을 해제하려면 회사의 IT 지원 센터에 문의하세요. |
| 활성화 | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 확인을 마치려면 우물정자를 누르세요. |
| 거부된 인증 다시 시도 | 확인이 거부되었습니다. |
| 다시 시도(표준) | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 확인을 마치려면 우물정자를 누르세요. |
| 인사말(표준) | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 확인을 마치려면 우물정자를 누르세요. |
| 인사말(PIN) | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 확인을 완료하려면 PIN을 입력하고 우물정자를 입력하세요. |
| 사기 인사말(PIN) | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다.  확인을 완료하려면 PIN을 입력하고 우물정자를 입력하세요. 이 확인을 시작하지 않은 경우 누군가가 계정에 액세스하려고 할 수 있습니다. 사기 행위 경고를 제출하려면 0 우물정자를 누릅니다. 이렇게 하면 회사의 IT 팀에 알리고 추가 인증 시도를 차단합니다. |
| 다시 시도(PIN) | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 확인을 완료하려면 PIN을 입력하고 우물정자를 입력하세요. |
| 숫자 뒤에 확장 프롬프트 | 이 확장으로 이미 전환된 경우 계속하려면 우물정자를 누르세요. |
| 인증이 거부됨 | 죄송합니다. 지금은 로그인할 수 없습니다. 나중에 다시 시도하세요. |
| 활성화 인사말(표준) | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 확인을 마치려면 우물정자를 누르세요. |
| 활성화 다시 시도(표준) | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 확인을 마치려면 우물정자를 누르세요. |
| 활성화 인사말(PIN) | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 확인을 완료하려면 PIN을 입력하고 우물정자를 입력하세요. |
| 숫자 앞에 확장 프롬프트 | Microsoft의 로그인 확인 시스템을 사용해 주셔서 감사합니다. 이 호출을 확장으로 전송하세요. |

### <a name="set-up-a-custom-message"></a>사용자 지정 메시지 설정

사용자 고유의 사용자 지정 메시지를 사용 하려면 다음 단계를 완료 합니다.

1. **Azure Active Directory** > **보안** > **MFA** > **전화 통화 설정**으로 이동합니다.
1. **인사말 추가**를 선택합니다.
1. 인사말 **유형** (예: *인사말 (표준)* 또는  *인증 성공*)을 선택 합니다.
1. [사용자 지정 메시지 언어 동작](#custom-message-language-behavior)의 이전 섹션에 따라 **언어**를 선택 합니다.
1. 업로드할 *. mp3* 또는 *.wav* 사운드 파일을 찾아 선택 합니다.
1. 준비가 되 면 **추가**를 선택 하 고 **저장**을 선택 합니다.

## <a name="mfa-service-settings"></a>MFA 서비스 설정

Azure Multi-Factor Authentication에 대한 앱 암호, 신뢰할 수 있는 IP, 확인 옵션 및 Multi-Factor Authentication 저장에 대한 설정을 서비스 설정에서 찾을 수 있습니다. 이는 레거시 포털이 며, 일반 Azure AD 포털의 일부가 아닙니다.

서비스 설정은 Azure Portal에서 **Azure Active Directory** > **보안** > **MFA** > **시작** > **구성** > **추가 클라우드 기반 MFA 설정**으로 이동하여 액세스할 수 있습니다. 추가 *서비스 설정* 옵션을 사용 하 여 새 창이 나 탭이 열립니다.

## <a name="trusted-ips"></a>신뢰할 수 있는 IP

Azure Multi-Factor Authentication의 _신뢰할 수_ 있는 ip 기능은 정의 된 IP 주소 범위에서 로그인 하는 사용자에 대 한 multi-factor Authentication 프롬프트를 무시 합니다. 사용자가 이러한 위치 중 하나에 있을 때 Azure Multi-Factor Authentication 프롬프트가 없는 경우 온-프레미스 환경에 대 한 신뢰할 수 있는 IP 범위를로 설정할 수 있습니다.

> [!NOTE]
> 신뢰할 수 있는 ip는 MFA 서버를 사용 하는 경우에만 개인 IP 범위를 포함할 수 있습니다. 클라우드 기반 Azure Multi-Factor Authentication의 경우 공용 IP 주소 범위만 사용할 수 있습니다.
>
> IPv6 범위는 [명명 된 위치 (미리 보기)](../conditional-access/location-condition.md#preview-features) 인터페이스 에서만 지원 됩니다.

조직에서 온-프레미스 애플리케이션 참고에 MFA를 제공하도록 NPS 확장을 배포하면 원본 IP 주소는 인증 시도가 통과하는 NPS 서버를 항상 나타냅니다.

| Azure AD 테 넌 트 유형 | 신뢰할 수 있는 IP 기능 옵션 | |:---|:---| 2 단계 | 관리 | **특정 범위의 ip 주소**: 관리자는 회사 인트라넷에서 로그인 하는 사용자에 대해 multi-factor authentication을 무시할 수 있는 ip 주소 범위를 지정 합니다. 최대 50의 신뢰할 수 있는 IP 범위를 구성할 수 있습니다. | | 페더레이션된 | **모든 페더레이션된 사용자**: 조직 내에서 로그인 하는 모든 페더레이션된 사용자는 multi-factor authentication을 무시할 수 있습니다. 사용자는 AD FS(Active Directory Federation Services)에서 발급된 클레임을 사용하여 인증을 바이패스합니다.<br/>**특정 범위의 ip 주소**: 관리자는 회사 인트라넷에서 로그인 하는 사용자에 대해 multi-factor authentication을 무시할 수 있는 ip 주소 범위를 지정 합니다. |

신뢰할 수 있는 IP 바이패스는 회사 인트라넷 내부 에서만 작동 합니다. **모든 페더레이션된 사용자** 옵션을 선택 하 고 사용자가 회사 인트라넷 외부에서 로그인 하는 경우 사용자는 multi-factor authentication을 사용 하 여 인증 해야 합니다. 이 프로세스는 사용자가 AD FS 클레임을 제시하는 경우에도 동일합니다.

### <a name="end-user-experience-inside-of-corpnet"></a>회사 네트워크 내부의 최종 사용자 환경

신뢰할 수 있는 Ip 기능이 사용 하지 않도록 설정 된 경우 브라우저 흐름에 multi-factor authentication이 필요 합니다. 이전 리치 클라이언트 애플리케이션에는 앱 암호가 필요합니다.

신뢰할 수 있는 Ip를 사용 하는 경우 브라우저 흐름에는 multi-factor authentication이 필요 하지 않습니다. 앱 암호는 사용자가 앱 암호를 만들지 않은 경우 오래 된 리치 클라이언트 응용 프로그램에 필요 하지 않습니다. 앱 암호를 사용한 후에는 암호가 여전히 필요합니다.

### <a name="end-user-experience-outside-corpnet"></a>회사 네트워크 외부 최종 사용자 환경

신뢰할 수 있는 IP가 정의 되었는지 여부에 관계 없이 브라우저 흐름에는 multi-factor authentication이 필요 합니다. 이전 리치 클라이언트 애플리케이션에는 앱 암호가 필요합니다.

### <a name="enable-named-locations-by-using-conditional-access"></a>조건부 액세스를 사용하여 명명된 위치를 사용하도록 설정

다음 단계를 사용 하 여 명명 된 위치를 정의 하는 조건부 액세스 규칙을 사용할 수 있습니다.

1. Azure Portal에서 **Azure Active Directory**를 검색 하 고 선택한 다음 **보안**  >  **조건부 액세스**  >  **명명 된 위치**로 이동 합니다.
1. **새 위치**를 선택합니다.
1. 위치에 대한 이름을 입력합니다.
1. **신뢰할 수 있는 위치로 표시**를 선택합니다.
1. 사용자 환경에 대 한 CIDR 표기법으로 IP 범위를 입력 합니다 (예: *40.77.182.32/27*).
1. **만들기**를 선택합니다.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>조건부 액세스를 사용하여 신뢰할 수 있는 IP 기능을 사용하도록 설정

조건부 액세스 정책을 사용 하 여 신뢰할 수 있는 Ip를 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure Active Directory**를 검색 하 고 선택한 다음 **보안**  >   **조건부 액세스**  >  **명명 된 위치**로 이동 합니다.
1. **MFA에서 신뢰할 수 있는 IP 구성**을 선택합니다.
1. **서비스 설정** 페이지의 **신뢰할 수 있는 IP** 아래에 있는 다음 두 가지 옵션 중에서 선택합니다.

   * **내 인트라넷에서 발생하는 페더레이션된 사용자의 요청**: 이 옵션을 선택하려면 확인란을 선택합니다. 회사 네트워크에서 로그인 하는 모든 페더레이션된 사용자는 AD FS에서 발급 한 클레임을 사용 하 여 multi-factor authentication을 바이패스 합니다. AD FS에 적절한 트래픽에 인트라넷 클레임을 추가하는 규칙이 있는지 확인합니다. 규칙이 없는 경우 AD FS에서 다음 규칙을 만듭니다.

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **공용 IP 중 특정 범위의 요청**: 이 옵션을 선택하려면 CIDR 표기법을 사용하여 텍스트 상자에 IP 주소를 입력합니다.
      * xxx.xxx.xxx.1에서 xxx.xxx.xxx.254 범위에 있는 IP 주소의 경우 **xxx.xxx.xxx.0/24** 같은 표기법을 사용합니다.
      * 단일 IP 주소의 경우 **xxx.xxx.xxx.xxx/32** 같은 표기법을 사용합니다.
      * 최대 50개의 IP 주소 범위를 입력합니다. 이러한 IP 주소에서 로그인 하는 사용자는 multi-factor authentication을 바이패스 합니다.

1. **저장**을 선택합니다.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>서비스 설정을 사용하여 신뢰할 수 있는 IP 기능을 사용하도록 설정

조건부 액세스 정책을 사용 하 여 신뢰할 수 있는 Ip를 사용 하지 않으려는 경우 다음 단계를 사용 하 여 Azure Multi-Factor Authentication에 대 한 *서비스 설정을* 구성할 수 있습니다.

1. Azure Portal에서 **Azure Active Directory**를 검색 하 고 선택한 다음 **사용자**를 선택 합니다.
1. **Multi-Factor Authentication**을 선택합니다.
1. Multi-Factor Authentication 섹션 아래에서 **서비스 설정**을 선택합니다.
1. **서비스 설정** 페이지의 **신뢰할 수 있는 IP** 아래에 있는 다음 두 가지 옵션 중 하나(또는 둘 다)를 선택합니다.

   * **내 인트라넷의 페더레이션된 사용자의 요청**: 이 옵션을 선택하려면 확인란을 선택합니다. 회사 네트워크에서 로그인 하는 모든 페더레이션된 사용자는 AD FS에서 발급 한 클레임을 사용 하 여 multi-factor authentication을 바이패스 합니다. AD FS에 적절한 트래픽에 인트라넷 클레임을 추가하는 규칙이 있는지 확인합니다. 규칙이 없는 경우 AD FS에서 다음 규칙을 만듭니다.

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **지정된 IP 주소 서브넷 범위의 요청**: 이 옵션을 선택하려면 CIDR 표기법을 사용하여 텍스트 상자에 IP 주소를 입력합니다.
      * xxx.xxx.xxx.1에서 xxx.xxx.xxx.254 범위에 있는 IP 주소의 경우 **xxx.xxx.xxx.0/24** 같은 표기법을 사용합니다.
      * 단일 IP 주소의 경우 **xxx.xxx.xxx.xxx/32** 같은 표기법을 사용합니다.
      * 최대 50개의 IP 주소 범위를 입력합니다. 이러한 IP 주소에서 로그인 하는 사용자는 multi-factor authentication을 바이패스 합니다.

1. **저장**을 선택합니다.

## <a name="verification-methods"></a>확인 방법

서비스 설정 포털에서 사용자에 게 제공 되는 확인 방법을 선택할 수 있습니다. 사용자가 Azure Multi-Factor Authentication에 자신의 계정을 등록한 경우, 사용하도록 설정한 옵션에서 원하는 인증 방법을 선택합니다. 사용자 등록 프로세스에 대 한 지침은 [multi-factor authentication에 대 한 내 계정 설정](../user-help/multi-factor-authentication-end-user-first-time.md)에서 제공 됩니다.

다음과 같은 확인 방법을 사용할 수 있습니다.

| 메서드 | Description |
|:--- |:--- |
| 휴대폰에 전화 걸기 |자동 음성 전화를 겁니다. 사용자가 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다. 이 전화 번호는 온-프레미스 Active Directory와 동기화되지 않습니다. |
| 휴대폰에 문자 메시지 전송 |확인 코드를 포함하는 문자 메시지를 보냅니다. 로그인 인터페이스에 이 확인 코드를 입력하라는 메시지가 표시됩니다. 이 프로세스를 단방향 SMS라고 합니다. 양방향 SMS는 사용자가 특정 코드를 다시 문자로 보내야 함을 의미합니다. 양방향 SMS는 2018년 11월 14일 이후 사용되지 않으며 지원되지 않습니다. 관리자는 이전에 양방향 SMS를 사용한 사용자에게 다른 방법을 사용하도록 설정해야 합니다.|
| 모바일 앱을 통한 알림 |휴대폰이나 등록된 디바이스로 푸시 알림을 보냅니다. 사용자는 알림을 보고 **확인**을 선택하여 인증을 완료합니다. [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다. |
| 모바일 앱 또는 하드웨어 토큰의 확인 코드 |Microsoft Authenticator 앱은 30초마다 새로운 OATH 확인 코드를 생성합니다. 사용자는 로그인 인터페이스에 확인 코드를 입력합니다. [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다. |

자세한 내용은 [AZURE AD에서 사용할 수 있는 인증 및 확인 방법](concept-authentication-methods.md) 을 참조 하세요.

### <a name="enable-and-disable-verification-methods"></a>인증 방법 사용 및 사용 안 함

확인 방법을 사용 하거나 사용 하지 않도록 설정 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure Active Directory**를 검색 하 고 선택한 다음 **사용자**를 선택 합니다.
1. **Multi-Factor Authentication**을 선택합니다.
1. Multi-Factor Authentication 섹션 아래에서 **서비스 설정**을 선택합니다.
1. **서비스 설정** 페이지의 **인증 옵션** 아래에서 사용자에게 제공하는 방법을 선택/선택 취소합니다.
1. **저장**을 클릭합니다.

## <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication 저장

_Multi-Factor Authentication_ 사용 기능을 사용 하면 사용자가 Multi-Factor Authentication를 사용 하 여 장치에 성공적으로 로그인 한 후 지정 된 일 수 동안 후속 확인을 무시할 수 있습니다. 사용 편의성을 개선 하 고 사용자가 동일한 장치에서 MFA를 수행 해야 하는 횟수를 최소화 하려면 90 일 이상 기간을 선택 합니다.

> [!IMPORTANT]
> 계정 또는 디바이스가 손상된 경우 신뢰할 수 있는 디바이스의 Multi-Factor Authentication을 저장해두는 것이 보안에 도움이 될 수 있습니다. 회사 계정이 손상되거나 신뢰할 수 있는 디바이스를 분실하거나 도난당한 경우 [MFA 세션을 취소](howto-mfa-userdevicesettings.md)해야 합니다.
>
> 복원 작업은 모든 장치에서 신뢰할 수 있는 상태를 해지 하므로 사용자는 multi-factor authentication을 다시 수행 해야 합니다. [Multi-factor Authentication에 대 한 설정 관리](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)에 명시 된 대로 사용자가 자신의 장치에서 Multi-Factor Authentication를 복원 하도록 지시할 수도 있습니다.

### <a name="how-the-feature-works"></a>기능의 작동 원리

Multi-Factor Authentication 저장 기능은 사용자가 로그인 시 **X일 동안 다시 묻지 않음** 상자를 선택한 경우 브라우저에서 영구 쿠키를 설정합니다. 그러면 쿠키가 만료될 때까지 동일한 브라우저에서 사용자에게 Multi-Factor Authentication 메시지가 다시 표시되지 않습니다. 사용자가 동일한 디바이스에서 다른 브라우저를 열거나 쿠키를 지우는 경우 다시 확인하라는 메시지가 표시됩니다.

비 브라우저 애플리케이션에는 앱이 최신 인증을 지원하는지 여부와 상관없이 **X일 동안 다시 묻지 않음** 옵션이 표시되지 않습니다. 이러한 앱에서는 1시간마다 새로운 액세스 토큰을 제공하는 _새로 고침 토큰_을 사용합니다. 새로 고침 토큰의 유효성이 확인 되 면 Azure AD는 지정 된 일 수 내에 마지막 multi-factor authentication이 발생 했는지 확인 합니다.

이 기능을 사용하면 일반적으로 매번 메시지를 표시하는 웹앱의 인증 횟수가 감소합니다. 이 기능은 더 낮은 기간이 구성 된 경우 일반적으로 90 일 마다 메시지를 표시 하는 최신 인증 클라이언트에 대 한 인증 수를 늘릴 수 있습니다. 조건부 액세스 정책과 결합될 경우 인증 수가 증가할 수도 있습니다.

> [!IMPORTANT]
> **Multi-Factor Authentication 기억** 기능은 사용자가 Azure Multi-Factor Authentication 서버 또는 타사 multi-factor authentication 솔루션을 통해 AD FS에 대해 multi-factor authentication을 수행 하는 경우 AD FS **로그인 유지** 기능과 호환 되지 않습니다.
>
> 사용자가 AD FS에서 **로그인 유지**를 선택하고 자신의 디바이스를 Multi-Factor Authentication에 대해 신뢰할 수 있는 것으로 표시하는 경우 해당 사용자는 **Multi-Factor Authentication 저장** 일 수가 만료된 후 자동으로 인증되지 않습니다. Azure AD는 새 multi-factor authentication을 요청 하지만 AD FS는 다시 multi-factor authentication을 수행 하는 대신 원래 Multi-Factor Authentication 클레임 및 날짜와 함께 토큰을 반환 합니다. **이 반응으로 Azure AD 및 AD FS 간의 확인 루프가 시작됩니다.**
>
> **Multi-Factor Authentication 저장** 기능은 B2B 사용자와 호환되지 않으며, 초대된 테넌트에 로그인할 때 B2B 사용자에게 표시되지 않습니다.
>

### <a name="enable-remember-multi-factor-authentication"></a>Multi-Factor Authentication 저장 사용

사용자가 MFA 상태 및 바이패스 프롬프트를 기억할 수 있는 옵션을 사용 하도록 설정 하 고 구성 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure Active Directory**를 검색 하 고 선택한 다음 **사용자**를 선택 합니다.
1. **Multi-Factor Authentication**을 선택합니다.
1. Multi-Factor Authentication 섹션 아래에서 **서비스 설정**을 선택합니다.
1. **서비스 설정** 페이지의 **multi-factor authentication 기억을**선택 하 고 **사용자가 신뢰 하는 장치에서 Multi-factor authentication을 기억할 수 있음** 옵션을 선택 합니다.
1. 신뢰할 수 있는 장치에서 multi-factor authentication을 바이패스 하도록 허용 하는 일 수를 설정 합니다. 최적의 사용자 환경을 위해 *90* 일 이상으로 기간을 연장 합니다.
1. **저장**을 선택합니다.

### <a name="mark-a-device-as-trusted"></a>디바이스를 신뢰할 수 있음으로 표시

Multi-Factor Authentication 보관 기능을 사용 하도록 설정 하면 사용자가 로그인 할 때 **다시 묻지 않음**옵션을 선택 하 여 장치를 신뢰할 수 있는 것으로 표시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Multi-Factor Authentication에서 사용할 수 있는 방법에 대 한 자세한 내용은 [Azure Active Directory에서 사용할 수 있는 인증 및 확인 방법](concept-authentication-methods.md) 을 참조 하세요.
