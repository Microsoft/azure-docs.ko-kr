---
title: Mobile Apps를 사용하여 Android에 인증 추가 | Microsoft Docs
description: Azure App Service 기능의 Mobile Apps를 사용하여 Google, Facebook, Twitter, Microsoft를 비롯한 다양한 ID 공급자를 통해 Android 앱의 사용자를 인증하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: 7b80c1148cf2716e71308d953ac445c4bb50cbc5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119776"
---
# <a name="add-authentication-to-your-android-app"></a>Android 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>요약
이 자습서에서는 지원되는 ID 공급자를 사용하여 Android의 할 일 모음 빠른 시작 프로젝트에 인증을 추가합니다. 이 자습서는 [Mobile Apps 시작] 자습서를 기반으로 하며 이를 먼저 완료해야 합니다.

## <a name="register"></a>인증을 위한 앱 등록 및 Azure App Service 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>허용되는 외부 리디렉션 URL에 앱 추가

보안 인증을 위해서는 앱에 대한 새로운 URL 체계를 정의해야 합니다. 이를 통해 인증 시스템은 인증 프로세스가 완료되면 앱으로 다시 리디렉션될 수 있습니다. 이 자습서에서는 전체적으로 URL 체계 _appname_을 사용합니다. 그러나 선택한 어떤 URL 체계도 사용 가능합니다. 이 체계는 모바일 애플리케이션에 고유해야 합니다. 서버 쪽에서 리디렉션을 사용하도록 설정하려면:

1. [Azure Portal]에서 해당 App Service를 선택합니다.

2. **인증/권한 부여** 메뉴 옵션을 클릭합니다.

3. **허용되는 외부 리디렉션 URL**에서 `appname://easyauth.callback`을 입력합니다.  이 문자열의 _appname_은 모바일 애플리케이션에 대한 URL 체계입니다.  이 체계는 프로토콜에 대한 일반 URL 사양을 따라야 합니다(문자 및 숫자만 사용하고 문자로 시작).  여러 위치에서 URL 체계에 따라 모바일 애플리케이션 코드를 조정해야 할 경우 선택한 문자열을 적어두어야 합니다.

4. **확인**을 클릭합니다.

5. **저장**을 클릭합니다.

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Android Studio에서 [Mobile Apps 시작] 자습서를 완료한 프로젝트를 엽니다. **실행** 메뉴에서 **앱 실행**을 클릭하여 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

     이 예외는 앱이 인증되지 않은 사용자로 백 엔드에 액세스하려고 시도하지만 *할 일 모음* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음으로 앱을 업데이트하여 Mobile Apps 백 엔드에서 리소스를 요청하기 전에 사용자를 인증하도록 합니다.

## <a name="add-authentication-to-the-app"></a>앱에 인증 추가
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>클라이언트에 인증 토큰 캐시
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>다음 단계
이 기본 인증 자습서를 완료했으므로 다음 자습서 중 하나를 계속하는 것을 고려해보세요.

* [Android 앱에 푸시 알림을 추가합니다](app-service-mobile-android-get-started-push.md).
  Azure Notification Hubs를 사용하는 Mobile Apps 백 엔드를 구성하여 푸시 알림을 보내는 방법을 알아봅니다.
* [Android 앱에 대해 오프라인 동기화를 사용합니다](app-service-mobile-android-get-started-offline-data.md).
  Mobile Apps 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Mobile Apps 시작]: app-service-mobile-android-get-started.md
[Azure Portal]: https://portal.azure.com/
