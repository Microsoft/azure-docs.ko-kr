---
title: 내 앱 포털에 대한 지원 - Azure Active Directory| Microsoft Docs
description: 내 앱 포털에서 로그인하고 일반적인 작업을 수행할 수 있도록 도움을 받습니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c5d1b08944417493d175de23c7738db58f1c7bd3
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798009"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>내 앱 포털 문제 해결

**내 앱** 포털에 로그인하거나 사용하는 데 문제가 있으면 기술 지원팀 또는 관리자에게 문의하기 전에 다음 문제 해결 팁을 사용해 보세요.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장을 설치하는 데 문제가 있습니다.

내 앱 보안 로그인 확장을 설치하는 데 문제가 있는 경우:

- 다음을 포함하여 지원되는 브라우저를 사용하고 있는지 확인합니다.

    - **Microsoft Edge.** Windows 10 Anniversary Edition 이상에서 실행됩니다.

    - **Google Chrome.** Windows 7 이상 및 Mac OS X 이상에서 실행됩니다.

    - **Mozilla Firefox 26.0 이상.** Windows XP SP2 이상 및 Mac OS X 10.6 이상에서 실행됩니다.

    - **Internet Explorer 11.** Windows 7 이상에서 실행됩니다(제한적 지원).

- 브라우저 확장 설정이 켜졌는지 확인합니다.

- 브라우저를 다시 시작하고 **내 앱** 포털에 다시 로그인해 보세요.

- 브라우저의 쿠키를 지운 다음 다시 시작하고 **내 앱** 포털에 다시 로그인합니다.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>**내 앱** 포털에 로그인할 수 없습니다.

**내 앱** 포털에 로그인하는 데 문제가 있는 경우 다음을 시도할 수 있습니다.

- 올바른 URL을 사용하고 있는지 확인합니다. https://myapps.microsoft.com/contoso.com 과 같이 조직의 https://myapps.microsoft.com 또는 사용자 지정된 페이지여야 합니다.

- 암호가 올바르고 만료되지 않았는지 확인합니다. 자세한 내용은 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md)을 참조하세요.

- 확인 정보가 최신이고 정확한지 확인합니다. 자세한 내용은 [Azure Multi-Factor Authentication은 무슨 의미입니까?](./multi-factor-authentication-end-user-first-time.md) 또는 [보안 정보 방법 및 정보 변경](./security-info-setup-auth-app.md)을 참조하세요.

- **인터넷 속성 > 보안 > 신뢰할 수 있는 사이트** 설정에 **내 앱** 포털 URL을 추가합니다.

- 브라우저 캐시를 지우고 다시 로그인해 보세요.

## <a name="my-password-isnt-working"></a>암호가 작동하지 않습니다.

암호를 잊어버렸거나, 조직으로부터 암호를 받지 못했거나, 계정에서 암호가 잠겨 있거나, 암호를 변경하려는 경우 [Azure AD 암호를 잊어버렸어요. 도와주세요!](active-directory-passwords-update-your-own-password.md)를 참조하세요.

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>암호를 다시 설정하고 싶습니다.

자신의 암호를 다시 설정할 수 있으려면 관리자가 먼저 조직에 대한 기능을 켠 다음, 필요한 확인 방법을 업데이트하고 확인해야 합니다. 확인 방법을 업데이트하는 방법에 대한 자세한 내용은 [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)을 참조하세요.

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>앱을 시작할 때 액세스 거부 메시지가 표시됩니다.

**My App** 포털에서 앱을 시작한 후에 **액세스 거부** 메시지가 표시되는 경우 다음을 시도할 수 있습니다.

- [내 앱 보안 로그인 확장](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)을 설치했는지 확인하고, [ 지원되는 브라우저](my-apps-portal-end-user-access.md#supported-browsers)를 사용하고 있는지 확인합니다.

- 앱에 대한 올바른 URL을 사용하고 있는지 확인하고, URL이 **인터넷 속성 > 보안 > 신뢰할 수 있는 사이트** 목록에 있는지 확인합니다.

- 암호가 올바르고 만료되지 않았는지 확인합니다. 자세한 내용은 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md)을 참조하세요.

- 확인 정보가 최신이고 정확한지 확인합니다. 자세한 내용은 [Azure Multi-Factor Authentication은 무슨 의미입니까?](./multi-factor-authentication-end-user-first-time.md) 또는 [보안 정보 방법 및 정보 변경](./security-info-setup-auth-app.md)을 참조하세요.

- 브라우저 캐시를 지우고 다시 로그인해 보세요.

이러한 작업을 시도한 후에도 여전히 앱에 액세스할 수 없는 경우에는 조직의 지원 센터에 문의해야 합니다.

## <a name="next-steps"></a>다음 단계

**내 앱** 포털에 로그인한 후에는 프로필과 계정 정보, 그룹 정보 및 액세스 검토 정보(권한이 있는 경우)를 업데이트할 수도 있습니다.

- [내 앱 포털에서 앱에 액세스하고 앱을 사용](my-apps-portal-end-user-access.md)합니다.

- [프로필 정보를 변경](my-apps-portal-end-user-update-profile.md)합니다.

- [그룹 관련 정보를 보고 업데이트](my-apps-portal-end-user-groups.md)합니다.

- [사용자 고유 액세스 검토를 수행](my-apps-portal-end-user-access-reviews.md)합니다.