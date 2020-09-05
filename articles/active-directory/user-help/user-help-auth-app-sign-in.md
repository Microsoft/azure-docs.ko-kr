---
title: Microsoft Authenticator 앱을 사용하여 로그인 - Azure AD
description: Microsoft Authenticator 앱을 사용하여 2단계 인증 또는 휴대폰 로그인을 통해 회사 또는 학교 계정 또는 개인 Microsoft 및 Microsoft 이외의 계정에 로그인합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 19e441334395f600c3adaa867660345164537f96
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799454"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱을 사용하여 계정에 로그인

2단계 인증을 사용하는 경우 Microsoft Authenticator 앱으로 계정에 쉽게 로그인할 수 있습니다. 2단계 인증을 사용하면 특히 중요한 정보를 볼 때 보다 안전하게 계정에 액세스할 수 있습니다. 암호를 잊어버리거나 암호가 노출될 수 있으므로 2단계 인증은 다른 사람이 침입하기 어렵게 만들어서 계정을 보호하는 추가 보안 단계입니다.

Microsoft Authenticator 앱은 다음을 비롯한 여러 가지 방법으로 사용할 수 있습니다.

- 사용자 이름 및 암호로 로그인한 후 두 번째 확인 방법에 대한 프롬프트를 제공합니다.

- 암호를 요구하지 않고 모바일 디바이스에서 사용자 이름과 지문, 얼굴 또는 PIN을 사용하여 로그인합니다.

  >[!Important]
  >이 휴대폰 로그인 방법은 회사 또는 학교 및 개인 Microsoft 계정에서만 작동합니다. Microsoft 이외의 계정은 표준 2단계 인증 프로세스를 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

Microsoft Authenticator 앱을 사용하려면 다음을 수행해야 합니다.

 1. Microsoft Authenticator 앱을 다운로드하고 설치합니다. 이를 아직 수행하지 않은 경우 [앱 다운로드 및 설치](user-help-auth-app-download-install.md)를 참조하세요.

 2. Microsoft Authenticator 앱에 회사/학교, 개인 및 타사 계정을 추가합니다. 자세한 단계는 [회사 또는 학교 계정 추가](user-help-auth-app-add-work-school-account.md), [개인 계정 추가](user-help-auth-app-add-personal-ms-account.md) 및 [Microsoft 이외의 계정 추가](user-help-auth-app-add-non-ms-account.md)를 참조하세요.

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>회사 또는 학교 계정에 휴대폰 로그인 설정 및 사용

휴대폰 로그인은 2단계 인증의 형식입니다. 알고 있고 가지고 있는 것을 제공하여 여전히 ID를 확인해야 하지만 휴대폰 로그인을 통해 계정 암호 입력을 건너뛸 수 있으며 모바일 디바이스에서 모든 ID 검증을 수행합니다.

휴대폰 로그인을 설정하려면 2단계 인증을 설정해야 합니다. 계정에 대한 2단계 인증을 설정하는 방법에 대한 자세한 내용은 [회사 또는 학교 계정 추가](user-help-auth-app-add-work-school-account.md) 및 [개인 계정 추가](user-help-auth-app-add-personal-ms-account.md)를 참조하세요.

휴대폰 로그인은 iOS 디바이스 및 Android 6.0 이상을 실행하는 Android 디바이스에서만 사용할 수 있습니다.

### <a name="turn-on-phone-sign-in"></a>휴대폰 로그인 설정

Microsoft Authenticator 앱을 열고, 회사 또는 학교 계정으로 이동하고, 휴대폰 로그인을 설정합니다.

- **계정 타일을 탭**하면 계정의 전체 화면이 표시 됩니다. **휴대폰 로그인 활성화됨**이 표시되면 암호 없이 로그인하도록 완전히 설정된 것입니다. **휴대폰 로그인 활성화**가 표시되면 탭하여 휴대폰 로그인을 켭니다.
- **2 단계 확인을 위해 앱을 이미 사용 하**고 있는 경우 계정 타일을 탭 하 여 계정의 전체 화면 보기를 볼 수 있습니다. 그런 다음, **휴대폰 로그인 활성화**를 탭하여 휴대폰 로그인을 켭니다.
- 앱의 **계정** 화면에서 **회사 또는 학교 계정을 찾을 수 없는 경우** 앱에 계정을 아직 추가하지 않은 것입니다. 회사 또는 학교 [계정 추가 도움말](user-help-auth-app-add-work-school-account.md)의 단계에 따라 회사 또는 학교 계정을 추가 합니다.

휴대폰 로그인을 켠 후 Microsoft Authenticator 앱만 사용 하 여 로그인 할 수 있습니다. 방법은 다음과 같습니다.

1. 회사 또는 학교 계정에 로그인합니다.

    사용자 이름을 입력하면 2자리 숫자를 보여주고 Microsoft Authenticator 앱을 통해 로그인하라는 **로그인 승인** 화면이 나타납니다. 이 로그인 방법을 사용하지 않으려면 **암호를 대신 사용**하도록 선택하고, 암호를 사용하여 로그인합니다.

    ![컴퓨터의 로그인 승인 상자](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. 디바이스에서 알림 또는 Microsoft Authenticator 앱을 연 다음, 컴퓨터의 **로그인 승인** 화면에 표시되는 숫자와 일치하는 숫자를 탭합니다.

    ![디바이스의 로그인 승인 상자](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. 로그인 시도를 인식한 경우 **승인**을 선택합니다. 그렇지 않은 경우 **거부**를 선택합니다.

4. 휴대폰의 PIN 또는 생체 인식 키를 사용하여 인증을 완료합니다.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>개인 Microsoft 계정에 휴대폰 로그인 설정 및 사용

Outlook.com, Xbox, Skype 등의 개인 Microsoft 계정에 휴대폰 로그인할 사용할 수 있습니다.

>[!NOTE]
>계정을 보호하기 위해 Microsoft Authenticator 앱에서 디바이스의 PIN 또는 생체 인식을 잠글 것을 요구합니다. 휴대폰을 잠금 해제 상태로 유지하면 휴대폰 로그인을 활성화하기 전에 보안 잠금을 설정하라고 앱에서 요구합니다.

### <a name="turn-on-phone-sign-in"></a>휴대폰 로그인 설정 

Microsoft Authenticator 앱을 열고, 회사 또는 학교 계정으로 이동하고, 휴대폰 로그인을 설정합니다.

- **계정 타일을 탭**하면 계정의 전체 화면이 표시 됩니다. **휴대폰 로그인 활성화됨**이 표시되면 암호 없이 로그인하도록 완전히 설정된 것입니다. **휴대폰 로그인 활성화**가 표시되면 탭하여 휴대폰 로그인을 켭니다.
- **2 단계 인증에 앱을 이미 사용 중인 경우**계정 타일을 탭 하 여 계정의 전체 화면 보기를 볼 수 있습니다. 그런 다음, **휴대폰 로그인 활성화**를 탭하여 휴대폰 로그인을 켭니다.
- 앱의 **계정** 화면에서 **계정을 찾을 수 없는 경우** 앱에 계정을 아직 추가하지 않은 것입니다. [개인 Microsoft 계정 추가](user-help-auth-app-add-personal-ms-account.md) 문서의 단계에 따라 개인 Microsoft 계정을 추가하세요.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>휴대폰 로그인을 사용하여 계정에 로그인

1. 개인 Microsoft 계정 로그인 페이지로 이동한 다음, 암호를 입력하는 대신 **Microsoft Authenticator 앱을 대신 사용** 링크를 선택합니다.

    Microsoft는 사용자의 휴대폰에 알림을 보냅니다.

2. 알림을 승인합니다.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>계정에 2단계 인증을 사용하여 로그인

표준 2 단계 인증 방법을 사용 하려면 로그인 하는 장치에 사용자 이름과 암호를 입력 한 다음 Microsoft Authenticator 앱이 알림을 받을지 또는 인증자 앱에서 확인 코드를 복사할지 여부를 선택 합니다. Android 장치에서 확인 코드는 **계정** 화면에서 찾을 수 있습니다. IOS 장치에서 이러한 확인 **코드는 계정 화면 또는** 계정 유형에 따라 계정의 전체 화면 보기에서 찾을 수 있습니다. Microsoft Authenticator 앱에 계정을 추가 하는 경우 계정에 대해 2 단계 인증을 설정 합니다.

>[!Note]
>Microsoft Authenticator 앱의 **계정** 화면에 회사 또는 학교 계정 또는 개인 계정이 표시되지 않는 경우 Microsoft Authenticator 앱에 계정을 추가하지 않은 것을 의미합니다. 계정을 추가하려면 [회사 또는 학교 계정 추가](user-help-auth-app-add-work-school-account.md) 또는 [개인 계정 추가](user-help-auth-app-add-personal-ms-account.md)를 참조하세요.

2단계 인증의 다양한 방법을 사용하여 회사 또는 학교 또는 개인 계정에 로그인하는 데 필요한 단계는 [2단계 인증 또는 보안 정보를 사용하여 로그인](user-help-sign-in.md)을 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

| 질문 | 해결 방법 |
| -------- | -------- |
| 휴대폰에 암호를 입력하는 것보다 안전하게 로그인할 수 있나요? | 오늘날 대부분의 사람들은 사용자 이름 및 암호를 사용하여 웹 사이트 또는 앱에 로그인합니다. 그러나 암호를 분실하거나, 도난당하거나, 해커가 추측할 가능성이 있습니다.<br><br>Microsoft Authenticator 앱을 설정하면 이 앱은 휴대폰의 PIN 또는 생체 인식 잠금으로 보호되는 계정을 잠금 해제하는 키를 휴대폰에 만듭니다. 이 키는 로그인하는 동안 신원을 증명하는 데 사용됩니다.<br><br>**중요**<br>데이터는 키를 로컬로 보호하는 용도로만 사용됩니다. 어떤 경우에도 전송되거나 클라우드에 저장되지 않습니다. |
| 휴대폰 로그인은 2단계 인증을 대체하나요? 기능을 꺼야 하나요? | 휴대폰 로그인은 2단계 모두 모바일 디바이스에서 발생하는 2단계 인증의 형식입니다. 계정에 대한 추가 보안을 제공하도록 설정한 2단계 인증을 유지해야 합니다. |
| 내 계정에 대해 2단계 인증을 계속 설정하는 경우에 두 개의 알림을 승인해야 하나요? | 아니요. 휴대폰을 사용하여 Microsoft 계정에 로그인해도 2단계 인증으로 집계되므로 두 번째 승인은 필요 없습니다. |
| 휴대폰을 분실하거나 휴대하지 않은 경우 계정에 액세스하려면 어떻게 해야 하나요? | 언제든지 로그인 페이지에서 [암호를 대신 사용]을 선택하여 암호를 사용하도록 전환할 수 있습니다. 그러나 2단계 인증을 사용하더라도 여전히 두 번째 방법을 사용하여 신분을 증명해야 합니다.<br><br>**중요**<br>계정에 두 가지 이상의 최신 인증 방법을 연결하는 것이 좋습니다.<br><br>[보안 설정](https://account.live.com/proofs/manage) 페이지에서 개인 계정의 인증 방법을 관리할 수 있습니다. 회사 또는 학교 계정이면 관리자가 보안 정보를 설정한 경우 조직의 [추가 보안 인증](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) 페이지 또는 **계정 보안 유지** 페이지로 이동하면 됩니다. 보안 정보에 대한 자세한 내용은 [보안 정보(미리 보기) 개요](./security-info-setup-signin.md)를 참조하세요.<br><br>인증 방법을 관리할 수 없으면 관리자에게 문의해야 합니다. |
| 이 기능을 중지하고 다시 암호를 사용하려면 어떻게 할까요? | 개인 계정의 경우 로그인하는 동안 **암호를 대신 사용** 링크를 선택합니다. 가장 최근에 사용한 방법이 저장되어 다음에 로그인할 때 기본값으로 제공됩니다. 다시 휴대폰 로그인을 사용하려면 로그인하는 동안 **앱을 대신 사용** 링크를 선택합니다.<br><br>회사 또는 학교 계정의 경우 Microsoft Authenticator 앱의 **설정** 페이지에서 디바이스 등록을 취소하거나 프로필의 **디바이스 및 작업** 영역에서 디바이스를 사용하지 않도록 설정해야 합니다. 프로필에서 디바이스를 사용하지 않도록 설정하는 자세한 내용은 [내 앱 포털에서 프로필 및 계정 정보 업데이트](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information)를 참조하세요. |
| 휴대폰 로그인에 회사 또는 학교 계정을 하나밖에 사용할 수 없는 이유는 무엇인가요? | 한 휴대폰은 하나의 회사 또는 학교 계정에 등록되어야 합니다. 다른 회사 또는 학교 계정에서 휴대폰 로그인을 사용하려면 **설정** 페이지에서 이 디바이스의 계정을 등록 취소해야 합니다. |
| 휴대폰을 사용하여 내 컴퓨터에 로그인할 수 있나요? | 컴퓨터에서 Windows 10의 Windows Hello를 사용하여 로그인하는 것이 좋습니다. Windows Hello는 얼굴, 지문 또는 PIN을 사용하여 로그인할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- 개인 Microsoft 계정에 대한 확인 코드를 가져오는 데 문제가 있는 경우 **Microsoft 계정 보안 정보 및 확인 코드** 문서에서 [확인 코드 문제 해결](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) 섹션을 참조하세요.

- 앱에 대한 일반적인 질문이 더 있는 경우 [Microsoft Authenticator FAQ](user-help-auth-app-faq.md)를 참조하세요.

- 2단계 인증에 대해 더 자세히 알아보려면 [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)을 참조하세요.

- 보안 정보에 대한 자세한 내용은 [보안 정보(미리 보기) 개요](./security-info-setup-signin.md)를 참조하세요.