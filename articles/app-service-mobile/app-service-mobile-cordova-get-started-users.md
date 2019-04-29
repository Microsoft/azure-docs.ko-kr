---
title: Mobile Apps를 사용하여 Apache Cordova에 인증 추가 | Microsoft Docs
description: Azure App Service에서 Mobile Apps를 사용하여 Google, Facebook, Twitter, Microsoft를 비롯한 다양한 ID 공급자를 통해 Apache Cordova 앱의 사용자를 인증하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 23b5967782cf237ed5af2b802aabbbf9c2f781e7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114213"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Apache Cordova 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>요약
이 자습서에서는 지원되는 ID 공급자를 사용하여 Apache Cordova의 할 일 모음 빠른 시작 프로젝트에 인증을 추가합니다. 이 자습서는 [Mobile Apps 시작] 자습서를 기반으로 하며 이를 먼저 완료해야 합니다.

## <a name="register"></a>인증을 위해 앱 등록 및 App Service 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[유사한 단계를 보여 주는 비디오 보기](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

이제 백 엔드에 대한 익명 액세스가 비활성화되었는지 확인할 수 있습니다. Visual Studio에서

* [Mobile Apps 시작]자습서를 완료했을 때 생성된 프로젝트를 엽니다.
* **Google Android 에뮬레이터**에서 애플리케이션을 실행합니다.
* 앱이 시작된 후 예기치 않은 연결 오류가 표시되는지 확인합니다.

다음으로 앱을 업데이트하여 모바일 앱 백 엔드에서 리소스를 요청하기 전에 사용자를 인증하도록 합니다.

## <a name="add-authentication"></a>앱에 인증 추가
1. **Visual Studio**에서 프로젝트를 연 다음 편집을 위해 `www/index.html` 파일을 엽니다.
2. 헤드 섹션에서 `Content-Security-Policy` META 태그를 찾습니다.  허용된 원본 목록에 OAuth 호스트를 추가합니다.

   | 공급자 | SDK 공급자 이름 | OAuth 호스트 |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    다음은 Content-Security-Policy(Azure Active Directory용으로 구현됨) 예제입니다.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    `https://login.microsoftonline.com`을 위 표의 OAuth 호스트로 바꿉니다.  content-security-policy 메타 태그에 대한 자세한 내용은 [Content-Security-Policy 설명서]를 참조하세요.

    일부 인증 공급자에서는 적절한 모바일 디바이스에서 사용하는 경우 Content-Security-Policy 변경이 필요하지 않습니다.  예를 들어 Android 디바이스에서 Google 인증을 사용하는 경우 Content-Security-Policy를 변경하지 않아도 됩니다.

3. 편집을 위해 `www/js/index.js` 파일을 열고 `onDeviceReady()` 메서드를 찾아 클라이언트 생성 코드에서 다음 코드를 추가합니다.

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    이 코드는 테이블 참조를 만들고 UI를 새로 고치는 기존 코드를 바꿉니다.

    login() 메서드는 공급자를 사용하여 인증을 시작합니다. login() 메서드는 JavaScript 프라미스를 반환하는 비동기 함수입니다.  초기화의 나머지는 login() 메서드가 완료될 때까지 실행되지 않도록 프라미스 응답 안에 배치됩니다.

4. 방금 추가한 코드에서 `SDK_Provider_Name` 를 로그인 공급자 이름으로 바꿉니다. 예를 들어 Azure Active Directory의 경우 `client.login('aad')`를 사용합니다.
5. 프로젝트를 실행합니다.  프로젝트 초기화가 완료되면 애플리케이션에서 선택한 인증 공급자에 대한 OAuth 로그인 페이지를 표시합니다.

## <a name="next-steps"></a>다음 단계
* Azure App Service의 [인증 정보] 에 대해 자세히 알아봅니다.
* [푸시 알림] 을 Apache Cordova 앱에 추가하여 자습서를 계속합니다.

SDK 사용 방법을 알아봅니다.

* [Apache Cordova SDK]
* [ASP.NET 서버 SDK]
* [Node.js 서버 SDK]

<!-- URLs. -->
[Mobile Apps 시작]: app-service-mobile-cordova-get-started.md
[Content-Security-Policy 설명서]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[푸시 알림]: app-service-mobile-cordova-get-started-push.md
[인증 정보]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET 서버 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js 서버 SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
