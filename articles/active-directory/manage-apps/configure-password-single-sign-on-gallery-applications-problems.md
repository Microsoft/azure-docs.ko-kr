---
title: Azure AD 갤러리 앱에 대 한 암호 SSO 구성 문제
description: Azure AD 애플리케이션 갤러리에 이미 나열된 애플리케이션에 대해 암호 Single Sign-On을 구성할 때 발생하는 일반적인 문제 이해
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e933c82229415a71182096a6aca9a2e535934d89
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159049"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD 갤러리 애플리케이션에 대해 암호 Single Sign-On을 구성할 때 발생하는 문제

이 아티클은 Azure AD 갤러리 애플리케이션에서 **암호 Single Sign-On**을 구성할 때 발생하는 일반적인 문제를 이해하는 데 도움이 됩니다.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>자격 증명은 입력했지만 확장에서 제출하지 않음

이 문제는 일반적으로 애플리케이션 공급업체에서 최근에 로그인 페이지를 변경하여 필드를 추가했거나, 사용자 이름 및 암호 필드 감지에 사용되는 식별자를 변경했거나, 애플리케이션의 로그인 경험 방식을 수정한 경우에 발생합니다. 다행스럽게도 많은 경우에 Microsoft와 애플리케이션 공급업체가 협력하여 이 문제를 신속하게 해결할 수 있습니다.

Microsoft에는 통합이 중단되었을 때 자동으로 검색할 기술이 있지만 문제를 즉시 발견하지 못하거나 해결할 때까지 시간이 걸릴 수 있습니다. 이러한 통합이 올바르게 작동하지 않는 경우에는 가능한 신속하게 해결할 수 있도록 지원 사례를 엽니다.

**이 애플리케이션의 공급업체와 연락된 경우** Microsoft에서 애플리케이션을 Azure Active Directory와 기본적으로 통합하기 위해 작업할 수 있도록 알려주세요. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../azuread-dev/howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>자격 증명을 입력하고 제출했지만 페이지에 자격 증명이 잘못되었다고 표시됨

이 문제를 해결하려면 먼저 다음과 같은 작업을 사용합니다.

- 사용자가 먼저 저장된 자격 증명을 사용해서 **애플리케이션 웹 사이트에 직접 로그인**을 시도하도록 합니다.

  * 로그인이 되면 사용자가 **애플리케이션 액세스 패널**의 **앱** 섹션에서 **애플리케이션 타일**에 있는 [자격 증명 업데이트](https://myapps.microsoft.com/) 단추를 클릭하여 작동하는 최신 사용자 이름 및 암호로 업데이트합니다.

  * 자신이나 다른 관리자가 이 사용자에게 자격 증명을 할당한 경우는 애플리케이션의 **사용자 및 그룹** 탭으로 이동하여 할당을 선택하고 **자격 증명 업데이트** 단추를 클릭하여 사용자 또는 그룹의 애플리케이션 할당을 찾습니다.

- 사용자가 직접 자격 증명을 할당한 경우는 사용자에게 **애플리케이션에서 암호가 만료되지 않았는지 확인하라고 한** 다음, 만료되었으면 애플리케이션에 직접 로그인하여 **만료된 암호를 업데이트**하도록 합니다.

  * 애플리케이션에서 암호가 업데이트된 후에 사용자에게 **애플리케이션 액세스 패널**의 **애플리케이션** 섹션에서 **애플리케이션 타일**에 있는 [자격 증명 업데이트](https://myapps.microsoft.com/) 단추를 클릭하여 문제가 없는 최신 사용자 이름 및 암호로 업데이트하라고 요청합니다.

  * 자신이나 다른 관리자가 이 사용자에게 자격 증명을 할당한 경우는 애플리케이션의 **사용자 및 그룹** 탭으로 이동하여 할당을 선택하고 **자격 증명 업데이트** 단추를 클릭하여 사용자 또는 그룹의 애플리케이션 할당을 찾습니다.

- 사용자가 아래 [액세스 패널 브라우저 확장을 설치하는 방법](#how-to-install-the-access-panel-browser-extension) 섹션의 단계에 따라 액세스 패널 브라우저 확장을 업데이트하도록 합니다.

- 액세스 패널 브라우저 확장이 실행 중이며 사용자의 브라우저에서 사용하도록 설정되어 있는지 확인합니다.

- **incognito, inPrivate 또는 개인 모드**일 때 사용자가 액세스 패널의 애플리케이션에 로그인하려고 하지 않았는지 확인합니다. 이러한 모드에서는 액세스 패널 확장이 지원되지 않습니다.

이전 방법이 통하지 않는 경우는 애플리케이션 쪽에서 변경이 일어나 일시적으로 애플리케이션과 Azure AD의 통합이 깨졌을 수 있습니다. 예를 들어, 애플리케이션 공급업체에서 수동 입력과 자동 입력에 대해 다르게 작동하는 스크립트를 페이지에 포함하는 바람에 자동화 통합이 깨지는 경우에 이 문제가 발생할 수 있습니다. 다행스럽게도 많은 경우에 Microsoft와 애플리케이션 공급업체가 협력하여 이 문제를 신속하게 해결할 수 있습니다.

Microsoft에는 애플리케이션 통합이 중단되었을 때 자동으로 검색할 기술이 있지만 문제를 즉시 발견하지 못하거나 해결할 때까지 시간이 걸릴 수 있습니다. 통합이 올바로 작동하지 않는 경우 최대한 신속하게 해결하도록 지원 사례를 열 수 있습니다. 

뿐만 아니라 **이 응용 프로그램의 공급 업체에 문의 하는 경우** **해당 응용** 프로그램을 사용 하 여 응용 프로그램을 기본적으로 Azure Active Directory와 통합 하는 데 사용할 수 있습니다. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../azuread-dev/howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Chrome 및 Firefox에서는 확장이 작동하지만 Internet Explorer에서는 작동하지 않음

이 문제에는 두 가지 주요 원인이 있습니다.

- Internet Explorer의 보안 설정에 따라, 웹 사이트가 **신뢰할 수 있는 영역**에 속해 있지 않으면 애플리케이션에서 스크립트 실행이 차단될 수 있습니다.

  *  이 문제를 해결하려면 사용자에게 **Internet Explorer 보안 설정**에서 **애플리케이션의 웹 사이트**를 **신뢰할 수 있는 사이트** 목록에 추가하라고 요청합니다. 사용자가 자세한 지침을 확인할 수 있도록 [사이트를 신뢰할 수 있는 사이트 목록에 추가하는 방법](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) 문서로 안내할 수도 있습니다.

- 드물게 Internet Explorer의 보안 유효성 검사 때문에 스크립트 실행 속도보다 페이지 로드 속도가 느려질 수 있습니다.

  * 이런 상황은 브라우저 버전, 컴퓨터 속도, 방문한 사이트에 따라 달라질 수 있습니다. 그런 경우는 특정 애플리케이션의 통합을 수정할 수 있도록 지원 팀에 문의해 주세요.

뿐만 아니라 **이 응용 프로그램의 공급 업체에 문의 하는 경우** **해당 응용** 프로그램을 사용 하 여 응용 프로그램을 기본적으로 Azure Active Directory와 통합 하는 데 사용할 수 있습니다. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../azuread-dev/howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>애플리케이션의 로그인 페이지가 최근에 변경되었거나 추가 필드가 필요한지 확인

애플리케이션의 로그인 페이지가 최근에 변경된 경우는 그 때문에 통합이 깨질 수도 있습니다. 이런 예로는 애플리케이션 공급업체에서 로그인 필드, chaptcha 또는 다단계 인증을 경험에 추가하는 경우가 있습니다. 다행스럽게도 많은 경우에 Microsoft와 애플리케이션 공급업체가 협력하여 이 문제를 신속하게 해결할 수 있습니다.

Microsoft에는 애플리케이션 통합이 중단되었을 때 자동으로 검색할 기술이 있지만 문제를 즉시 발견하지 못하거나 해결할 때까지 시간이 걸릴 수 있습니다. 통합이 올바로 작동하지 않는 경우 최대한 신속하게 해결하도록 지원 사례를 열 수 있습니다. 

뿐만 아니라 **이 응용 프로그램의 공급 업체에 문의 하는 경우** **해당 응용** 프로그램을 사용 하 여 응용 프로그램을 기본적으로 Azure Active Directory와 통합 하는 데 사용할 수 있습니다. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../azuread-dev/howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="how-to-install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장을 설치하는 방법

액세스 패널 브라우저 확장을 설치하려면 아래 단계를 수행합니다.

1.  지원되는 브라우저 중 하나에서 [액세스 패널](https://myapps.microsoft.com)을 열고 Azure AD에서 **사용자**로 로그인합니다.

2.  액세스 패널에서 **암호-SSO 애플리케이션**을 클릭합니다.

3.  소프트웨어를 설치하라는 프롬프트에서 **지금 설치**를 선택합니다.

4.  브라우저에 따라 다운로드 링크로 이동됩니다. 브라우저에 확장을 **추가**합니다.

5.  브라우저에서 요청하면 확장을 **사용** 또는 **허용**하도록 선택합니다.

6.  설치되면 브라우저 세션을 **다시 시작**합니다.

7.  액세스 패널에 로그인하고 암호 SSO 애플리케이션을 **시작**할 수 있는지 확인합니다.

아래와 같은 직접 링크에서 Chrome 및 Firefox에 대한 확장을 다운로드할 수 있습니다.

-   [Chrome 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>다음 단계
[애플리케이션 프록시를 사용하여 앱에 Single Sign-On 제공](application-proxy-configure-single-sign-on-with-kcd.md)

