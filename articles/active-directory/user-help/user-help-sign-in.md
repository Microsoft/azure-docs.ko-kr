---
title: ID 인증 정보를 사용하여 로그인 - Azure AD
description: 보안 정보의 다양한 신원 확인 방법을 사용하여 로그인하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 42ced6632ebfa56af8fe13a02f531b9835e13df3
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799386"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>2단계 인증 또는 보안 정보를 사용하여 로그인

2단계 인증 또는 보안 정보를 설정한 후에는 지정된 확인 방법을 사용하여 계정에 로그인할 수 있습니다.

> [!Note]
> 2단계 인증 방법을 계속 사용하는 경우 [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md) 문서의 지침에 따라 확인 방법을 설정해야 합니다.
>
> 관리자가 보안 정보 환경을 켠 경우 이러한 단계별 문서를 사용하여 확인 방법을 설정해야 합니다.<ul><li>[인증 앱을 사용하도록 보안 정보 설정](security-info-setup-auth-app.md)</li><li>[문자 메시지를 사용하도록 보안 정보 설정](security-info-setup-text-msg.md)</li><li>[전화 통화를 사용하도록 보안 정보 설정](security-info-setup-phone-number.md)</li><li>[보안 키로 사용할 보안 정보 설정](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>모바일 디바이스에서 인증자 앱 알림을 사용하여 로그인

1. 사용자 이름 및 암호를 사용하여 계정에 로그인합니다.

2. 모바일 디바이스로 보낸 승인 알림에서 **승인**을 선택합니다.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>모바일 디바이스에서 인증자 앱 코드를 사용하여 로그인

1. 사용자 이름 및 암호를 사용하여 계정에 로그인합니다.

2. 인증자 앱을 열고 계정에 대해 임의로 생성된 코드를 **코드 입력** 상자에 입력합니다.

## <a name="sign-in-using-your-phone-number"></a>전화 번호를 사용하여 로그인

1. 사용자 이름 및 암호를 사용하여 계정에 로그인합니다.

2. 전화를 받고 지침을 따릅니다.

## <a name="sign-in-using-a-text-message"></a>문자 메시지를 사용하여 로그인

1. 사용자 이름 및 암호를 사용하여 계정에 로그인합니다.

2. 문자 메시지를 열고 문자 메시지의 코드를 **코드 입력** 상자에 입력합니다.

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>잠금 화면에서 보안 키를 사용하여 로그인

1. 보안 키를 등록한 후에는 Windows 10 잠금 화면에서 보안 키 이미지를 선택합니다.

2. 보안 키를 디바이스의 USB 포트에 삽입하고 보안 키 PIN을 사용하여 Windows에 로그인합니다.

    ![Windows 10 잠금 화면에서 보안 키 로그인](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>보안 키와 Microsoft Edge 브라우저를 사용하여 로그인

1. 보안 키를 등록한 후에는 Microsoft Edge 브라우저를 엽니다.

2. 로그인하라는 메시지가 표시되면 보안 키를 디바이스의 USB 포트에 삽입하고 보안 키 PIN을 사용하여 Windows에 로그인합니다.

    ![Microsoft Edge 브라우저를 사용하여 보안 키 로그인](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Microsoft Authenticator 앱을 사용하여 로그인하는 방법에 대한 자세한 [Microsoft Authenticator 앱을 사용하여 계정에 로그인](user-help-auth-app-sign-in.md) 문서를 참조하세요.

## <a name="sign-in-using-another-verification-method"></a>또 다른 인증 방법을 사용하여 로그인

어떤 이유로 기본 로그인 방법을 사용할 수 없는 경우 이전에 설정한 다른 확인 방법을 사용할 수 있습니다.

1. 계정에 정상적으로 로그인한 다음, **2단계 인증** 페이지에서 **다른 방법으로 로그인** 링크를 선택합니다.

    ![로그인 인증 방법 변경](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >**다른 방법으로 로그인** 링크가 보이지 않으면 다른 확인 방법을 설정하지 않았다는 뜻이며, 관리자에게 계정 로그인을 도와 달라고 문의해야 합니다. 관리자의 도움을 받아 로그인한 후에는 확인 방법을 추가합니다. 인증 방법 추가에 대한 자세한 내용은 [2단계 인증을 위한 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조하세요.
    >
    >**다른 방법으로 로그인** 링크가 표시되지만 여전히 다른 인증 방법이 보이지 않는 경우 관리자에게 계정 로그인을 도와 달라고 문의해야 합니다.

2. 대체 인증 방법을 선택하고 2단계 인증 프로세스를 계속합니다.

3. 계정에 다시 로그인한 후 인증 방법을 업데이트할 수 있습니다(필요한 경우). 방법 추가 또는 변경에 대한 자세한 내용은 [2단계 인증을 위한 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [보안 정보(미리 보기) 개요](./security-info-setup-signin.md) 문서에서 보안 정보에 대해 자세히 알아봅니다.

- [2단계 인증 개요](./multi-factor-authentication-end-user-first-time.md) 문서에서 2단계 인증에 대해 알아봅니다.

- 암호를 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.