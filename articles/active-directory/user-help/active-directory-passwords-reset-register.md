---
title: 인증 정보를 사용한 본인 암호 재설정 - Azure AD
description: Azure AD 셀프 서비스 암호 재설정을 위해 인증 수단 정보를 등록하면 관리자 도움 없이도 본인의 암호를 다시 설정할 수 있습니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e460f3fd383286b1e61a979b87a9dfa09f272313
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799029"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>자신의 암호를 재설정하기 위한 본인 확인 방법 정보 등록

회사 또는 학교 암호가 생각 나지 않거나, 조직으로부터 암호를 받은 적이 없거나, 계정이 잠긴 경우 보안 정보 및 모바일 디바이스를 사용하여 회사 또는 학교 암호를 재설정할 수 있습니다.

관리자는 사용자가 정보를 등록하고 암호를 재설정할 수 있도록 이 기능을 켜야 합니다. **암호를 잊어버렸습니다** 옵션이 표시되지 않는 경우 조직의 관리자가 이 기능을 켜지 않은 것입니다. 이 기능이 필요하다고 생각하시는 경우 지원 센터에 도움을 요청하세요.

>[!Important]
>이 문서는 셀프 서비스 암호 재설정에 가입하려는 사용자를 위한 것입니다. 즉 alain@contoso.com , 관리자의 도움 없이 자신의 회사 또는 학교 암호 (예:)를 다시 설정할 수 있습니다. 직원 또는 다른 사용자에 대해 셀프 서비스 암호 재설정을 켜는 방법을 원하는 관리자는 [Azure AD 셀프 서비스 암호 재설정 배포 및 기타 문서](../authentication/howto-sspr-deployment.md)를 참조하세요.

## <a name="set-up-your-password-reset-verification-method"></a>암호 재설정 확인 방법 설정

1. 디바이스에서 웹 브라우저를 열고 [암호 정보 페이지](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)로 이동합니다.

2. 관리자가 조직을 설정한 방식에 따라 다음 옵션 중 하나 이상을 보안 인증 방법으로 설정하는 데 사용할 수 있습니다. 여러 옵션을 사용할 수 있다면 방법 중 하나를 사용할 수 없게 되는 경우를 대비하여 둘 이상의 옵션을 보안 확인 방법으로 사용하는 것이 좋습니다.

    - **인증 앱.** Microsoft Authenticator 앱 또는 다른 인증자 앱을 보안 확인 방법으로 사용하도록 선택합니다. 앱 설정 방법에 대한 자세한 내용은[확인 방법으로 Microsoft Authenticator 앱 설정](security-info-setup-auth-app.md)을 참조하세요.

    - **문자 메시지.** 본인의 모바일 디바이스에 직접 문자 메시지를 보내려면 선택합니다. 문자 메시지 설정에 대한 자세한 내용은 [확인 방법으로 문자 메시지 설정](security-info-setup-text-msg.md)을 참조하세요.

    - **전화 통화.** 등록된 전화 번호로 전화를 받으려면 선택합니다. 전화 통화 설정에 대한 자세한 내용은 [확인 방법으로 전화 번호 설정](security-info-setup-phone-number.md)을 참조하세요.

    - **보안 키.** Microsoft 호환 보안 키를 사용하려면 선택합니다. 자세한 내용은 [확인 방법으로 보안 키 설정](security-info-setup-security-key.md)을 참조하세요.

    - **이메일 주소.** 잊었거나 분실한 암호를 요청하지 않고 사용할 수 있는 대체 이메일 주소를 선택합니다. 보안 확인 방법이 아닌 암호 재설정에만 적용됩니다. 이메일 주소 설정에 대한 자세한 내용은 [확인 방법으로 이메일 주소 설정](security-info-setup-email.md)을 참조하세요.

    - **보안 질문.** 관리자가 정한 미리 정의된 보안 질문을 설정하고 답하도록 선택합니다. 보안 확인 방법이 아닌 암호 재설정에만 적용됩니다. 보안 질문에 대한 자세한 내용은 [확인 방법으로 보안 질문 설정](security-info-setup-questions.md)을 참조하세요.

3. 메서드를 선택 및 설정한 후 **마침**을 선택하여 프로세스를 완료합니다.

    > [!Note]
    > 전화 번호 또는 이메일 주소에 추가된 정보는 조직의 글로벌 디렉터리와 공유되지 않습니다. 이 정보는 본인과 관리자만 볼 수 있습니다. 보안 질문에 대한 대답만 볼 수 있습니다.

## <a name="common-problems-and-their-solutions"></a>일반적인 문제 및 해결 방법

 다음은 일반적인 오류 사례 및 해결 방법입니다.

| 오류 메시지 |  가능한 솔루션 |
| --- | --- | --- |
| 관리자에게 문의하세요.<br>Microsoft에서 사용자 계정 암호가 관리되지 않는 것이 감지되었습니다. 결과적으로 자동으로 암호를 재설정할 수 없습니다.<br>IT 직원에게 추가 지원을 요청하세요.| 사용자 ID를 입력한 후 이 오류 메시지가 표시되었다면 해당 조직에서 내부적으로 암호를 관리하며 사용자가 **계정에 액세스할 수 없음** 링크에서 암호를 재설정할 수 없다는 뜻입니다. 이 경우 암호를 재설정하려면 해당 조직의 기술 지원팀이나 관리자에게 문의해야 합니다. |
| 암호 재설정을 위해 계정을 사용할 수 없습니다.<br>죄송하지만 IT 직원이 계정을 이 서비스와 함께 사용하도록 설정하지 않습니다.<br>원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | 사용자 ID를 입력한 후 이 오류 메시지가 표시되었다면 조직에서 암호 재설정 기능을 켜지 않았거나 해당 사용자가 이 기능을 사용하도록 허용되지 않았음을 의미합니다. 이런 상황에서 암호를 재설정하려면 **관리자 문의** 링크를 선택해야 합니다. 링크를 클릭하면 조직의 기술 지원팀 또는 관리자에게 이메일을 보내 암호 재설정이 필요함을 알립니다. |
| 계정을 확인하지 못했습니다.<br>원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | 사용자 ID를 입력한 후 이 오류 메시지가 표시되었다면 해당 조직에서 암호 재설정을 켰고 사용자가 이 기능을 사용할 수 있으나 서비스에 등록되지 않았음을 의미합니다. 이 상황에서 암호를 재설정하려면 해당 조직의 기술 지원팀이나 관리자에게 문의해야 합니다. 디바이스로 돌아와 암호 재설정에 등록하는 내용은 이 문서의 위 프로세스를 참조하세요. |

## <a name="next-steps"></a>다음 단계

- [셀프 서비스 암호 재설정을 사용하여 암호 변경](active-directory-passwords-update-your-own-password.md)

- [보안 정보 페이지](https://mysignins.microsoft.com/security-info)

- [암호 재설정 포털](https://passwordreset.microsoftonline.com/)

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)