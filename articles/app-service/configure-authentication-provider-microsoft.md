---
title: Microsoft 계정 인증 구성 - Azure App Service
description: App Services 애플리케이션에 대해 Microsoft 계정 인증을 구성하는 방법을 알아봅니다.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e3da856efd7d44f15f9de27c9e38375d40dc211d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850960"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Microsoft 계정 로그인을 사용하도록 App Service 애플리케이션을 구성하는 방법
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Microsoft 계정을 인증 공급자로 사용하도록 Azure App Service를 구성하는 방법을 보여 줍니다. 

## <a name="register-microsoft-account"> </a>Microsoft 계정을 사용하여 앱 등록
1. [Azure Portal]에 로그온한 다음, 애플리케이션으로 이동합니다. **URL**을 복사하여 나중에 Microsoft 계정으로 응용 프로그램을 구성하는 데 사용합니다.
2. Microsoft 계정 개발자 센터의 [내 애플리케이션] 페이지로 이동하고 필요한 경우 Microsoft 계정으로 로그온합니다.
3. **앱 추가**를 클릭한 다음, 애플리케이션 이름을 입력하고 **만들기**를 클릭합니다.
4. 나중에 필요하므로 **애플리케이션 ID**를 기록해 둡니다. 
5. "플랫폼" 아래에서 **플랫폼 추가** 를 클릭하고 "웹"을 선택합니다.
6. "리디렉션 URI" 아래에서 애플리케이션에 대한 엔드포인트를 입력한 다음, **저장**을 클릭합니다. 
   
   > [!NOTE]
   > 리디렉션 URI는 경로 */.auth/login/microsoftaccount/callback*이 추가된 애플리케이션의 URL입니다. 예: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`   
   > HTTPS 체계를 사용 중인지 확인합니다.
   
7. "애플리케이션 암호" 아래에서 **새 암호 생성**을 클릭합니다. 표시되는 값을 기록해 둡니다. 이 페이지를 떠나면 다시 표시되지 않습니다.

    > [!IMPORTANT]
    > 암호는 중요한 보안 자격 증명입니다. 다른 사람과 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
    
8. 페이지 맨 아래에 있는 **저장**

## <a name="secrets"></a>App Service 애플리케이션에 Microsoft 계정 정보 추가
1. [Azure Portal]로 돌아가서 애플리케이션으로 이동하여 **설정** > **인증/권한 부여**를 클릭합니다.
2. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **켭니다**.
3. **Microsoft 계정**을 클릭합니다. 이전에 가져온 애플리케이션 ID 및 암호 값을 붙여넣고 필요에 따라 애플리케이션에 필요한 범위를 설정합니다. 그런 후 **OK**를 클릭합니다.
   
    ![][1]
   
    기본적으로 App Service는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.
4. (선택 사항) Microsoft 계정에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Microsoft 계정**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Microsoft 계정에 리디렉션되어야 합니다.
5. **저장**을 클릭합니다.

이제 앱에서 Microsoft 계정을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[내 애플리케이션]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
