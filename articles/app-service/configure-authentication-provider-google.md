---
title: Google 인증 구성
description: App Service 앱에 대한 id 공급자로 Google 인증을 구성 하는 방법에 대해 알아봅니다.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670822"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Google 로그인을 사용 하도록 App Service 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Google을 인증 공급자로 사용하도록 Azure App Service를 구성하는 방법을 보여 줍니다.

이 항목의 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)으로 이동하세요.

## <a name="register"></a>Google을 사용하여 애플리케이션 등록

1. [서버 쪽 앱에 대한 Google 로그인](https://developers.google.com/identity/sign-in/web/server-side-flow) 의 google 설명서에 따라 클라이언트 ID 및 클라이언트 암호를 만듭니다. 코드를 변경할 필요가 없습니다. 다음 정보를 사용 하면 됩니다.
    - **권한 있는 JavaScript 원본**에 대해 *\<앱 이름 >* 에서 앱 이름과 함께 `https://<app-name>.azurewebsites.net`를 사용 합니다.
    - **권한 있는 리디렉션 URI**의 경우 `https://<app-name>.azurewebsites.net/.auth/login/google/callback`를 사용 합니다.
1. 앱 ID 및 앱 암호 값을 복사 합니다.

    > [!IMPORTANT]
    > 앱 암호는 중요 한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.

## <a name="secrets"></a>애플리케이션에 Google 정보 추가

1. [Azure 포털]에서 App Service 앱으로 이동 합니다.
1. 설정 > **인증/권한 부여**를 선택 하 고 **App Service 인증이** **설정** 되어 있는지 확인 **합니다.**
1. **Google**을 선택한 다음 이전에 가져온 앱 ID 및 앱 암호 값을 붙여넣습니다. 응용 프로그램에 필요한 모든 범위를 사용 하도록 설정 합니다.
1. **확인**을 선택합니다.

   App Service는 인증을 제공 하지만 사이트 콘텐츠 및 Api에 대한 권한 있는 액세스를 제한 하지 않습니다. 자세한 내용은 [사용자 권한 부여 또는 거부](app-service-authentication-how-to.md#authorize-or-deny-users)를 참조 하세요.

1. 필드 Google에서 인증 된 사용자만 사이트에 액세스 하도록 제한 하려면 **google**에 **요청이 인증 되지 않은 경우 수행할 작업** 을 설정 합니다. 이 기능을 설정 하면 앱에서 모든 요청을 인증 해야 합니다. 또한 인증을 위해 인증 되지 않은 모든 요청을 Google으로 리디렉션합니다.

    > [!CAUTION]
    > 이러한 방식으로 액세스를 제한 하는 것은 앱에 대한 모든 호출에 적용 됩니다 .이는 여러 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지가 있는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 앱이 수동으로 인증을 시작 하도록 **익명 요청 허용 (작업 없음)을 사용** 하는 것이 좋습니다. 자세한 내용은 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조 하세요.

1. **저장**을 선택합니다.

이제 앱에서 Google을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 포털]: https://portal.azure.com/

