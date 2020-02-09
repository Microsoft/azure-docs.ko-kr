---
title: Twitter 인증 구성
description: App Service 앱에 대한 id 공급자로 Twitter 인증을 구성 하는 방법에 대해 알아봅니다.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 04/19/2018
ms.custom: seodec18
ms.openlocfilehash: b5ec17c18cec8053f0732366c0cc5d0c5003e4de
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670810"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Twitter 로그인을 사용 하도록 App Service 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Twitter를 인증 공급자로 사용 하도록 Azure App Service를 구성 하는 방법을 보여 줍니다.

이 문서의 절차를 완료 하려면 확인 된 전자 메일 주소 및 전화 번호를 포함 하는 Twitter 계정이 필요 합니다. 새 Twitter 계정을 만들려면 [twitter.com]으로 이동합니다.

## <a name="register"></a>Twitter를 사용하여 애플리케이션 등록

1. [Azure 포털] 에 로그인 하 고 응용 프로그램으로 이동 합니다. **URL**을 복사합니다. Twitter 앱을 구성 하는 데 사용 합니다.
1. [Twitter 개발자] 웹 사이트로 이동 하 고 twitter 계정 자격 증명을 사용 하 여 로그인 한 다음 **새 앱 만들기**를 선택 합니다.
1. 새 앱에 대한 **이름** 및 **설명** 을 입력 합니다. 응용 프로그램의 **URL** 을 **웹 사이트** 필드에 붙여넣습니다. **콜백 url** 필드에 App Service 앱의 url을 입력 하 고 `/.auth/login/aad/callback`경로를 추가 합니다. 예: `https://contoso.azurewebsites.net/.auth/login/twitter/callback` HTTPS 체계를 사용 해야 합니다.
1. 페이지 맨 아래에서 조건을 읽고 동의 합니다. **Twitter 응용 프로그램 만들기**를 선택 합니다. 응용 프로그램 세부 정보가 표시 됩니다.
1. **설정** 탭을 선택 하 고 **이 응용 프로그램을 사용**하 여 Twitter에 로그인 한 후 **설정 업데이트**를 선택 합니다.
1. **Keys and Access Tokens** 탭을 선택합니다.

   다음 값을 적어둡니다.
   - 소비자 키 (API 키)
   - 소비자 비밀 (API 비밀)

   > [!NOTE]
   > 소비자 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요.

## <a name="secrets"></a>애플리케이션에 Twitter 정보 추가

1. [Azure 포털]에서 응용 프로그램으로 이동 합니다.
1. 설정 > **인증/권한 부여**를 선택 하 고 **App Service 인증이** **설정** 되어 있는지 확인 **합니다.**
1. **Twitter**를 선택 합니다.
1. 이전에 얻은 `API Key` 및 `API Secret` 값을 붙여넣습니다.
1. **확인**을 선택합니다.

   ![모바일 앱 Twitter 설정의 스크린샷][1]

   기본적으로 App Service는 인증을 제공 하지만 사이트 콘텐츠 및 Api에 대한 권한 있는 액세스를 제한 하지 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

1. (선택 사항) Twitter에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Twitter**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이 기능을 설정 하면 앱에서 모든 요청을 인증 해야 합니다. 또한 인증을 위해 모든 인증 되지 않은 요청을 Twitter로 리디렉션합니다.

   > [!CAUTION]
   > 이러한 방식으로 액세스를 제한 하는 것은 앱에 대한 모든 호출에 적용 됩니다 .이는 여러 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지가 있는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 앱이 수동으로 인증을 시작 하도록 **익명 요청 허용 (작업 없음)을 사용** 하는 것이 좋습니다. 자세한 내용은 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조 하세요.

1. **저장**을 선택합니다.

이제 앱에서 Twitter를 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter 개발자]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure 포털]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
