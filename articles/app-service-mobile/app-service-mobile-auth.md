---
title: Azure App Service에서 모바일 앱에 대한 인증 및 권한 부여 | Microsoft Docs
description: Azure App Service, 특히 모바일 앱에 대한 인증/권한 부여 기능에 대한 개념적 참조 및 개요입니다.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859127"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Azure App Service에서 모바일 앱에 대한 인증 및 권한 부여

이 문서에서는 App Service 백 엔드를 사용하여 네이티브 모바일 앱을 개발할 때 인증 및 권한 부여가 작동하는 방법에 대해 설명합니다. App Service에서 통합된 인증 및 권한 부여를 제공하므로 모바일 앱은 App Service의 코드를 변경하지 않고 사용자를 로그인할 수 있습니다. 애플리케이션을 보호하고 사용자 단위당 데이터로 작업하는 쉬운 방법을 제공합니다. 

이 문서는 모바일 앱 개발에 중점을 둡니다. 모바일 앱에 대한 App Service 인증 및 권한 부여를 빠르게 시작하려면 [iOS 앱에 인증 추가][iOS](또는 [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] 또는 [Cordova]) 자습서 중 하나를 참조하세요. 

App Service에서 인증 및 권한 부여가 작동하는 방법에 대한 내용은 [Azure App Service의 인증 및 권한 부여](../app-service/overview-authentication-authorization.md)를 참조하세요.

## <a name="authentication-with-provider-sdk"></a>공급자 SDK를 사용하는 인증

App Service에서 모든 항목이 구성되면 App Service를 사용하여 로그인하도록 모바일 클라이언트를 수정할 수 있습니다. 두 가지 접근 방법은 다음과 같습니다.

* 지정된 ID 공급자가 게시한 SDK를 사용하여 ID를 설정한 다음 App Service에 액세스를 얻습니다.
* Mobile Apps 클라이언트 SDK가 사용자에서 로그인할 수 있도록 한 줄의 코드를 사용합니다.

> [!TIP]
> 대부분의 애플리케이션은 공급자 SDK를 사용하여 사용자가 로그인할 때 더 일관된 환경을 가져오고, 토큰 새로 고침 지원을 사용하고, 공급자가 지정한 다른 이점을 가져와야 합니다.
> 
> 

공급자 SDK를 사용하면 사용자는 앱에서 실행 중인 운영 체제와 더 밀접하게 통합되는 환경에 로그인할 수 있습니다. 또한 이 방법을 사용하면 클라이언트에 공급자 토큰과 일부 사용자 정보가 제공되어 Graph API를 사용하고 사용자 환경을 사용자 지정하는 작업을 더 쉽게 수행할 수 있습니다. 클라이언트의 코드가 사용자에게 로그인하고 이 코드에서 공급자 토큰에 액세스할 수 있기 때문에 경우에 따라 블로그 및 포럼에서 이를 "클라이언트 흐름" 또는 "클라이언트 제어 흐름"이라고도 합니다.

공급자 토큰을 가져오면 유효성 검사에 대한 App Service에 전송되어야 합니다. App Service는 토큰 유효성을 검사한 후, 클라이언트에 반환되는 새 App Service 토큰을 만듭니다. Mobile Apps 클라이언트 SDK에는 이 교환을 관리하고 애플리케이션 백 엔드에 대한 모든 요청에 토큰을 자동으로 연결하는 도우미 메서드가 있습니다. 개발자는 공급자 토큰에 대한 참조를 유지할 수도 있습니다.

인증 흐름에 대한 자세한 내용은 [App Service 인증 흐름](../app-service/overview-authentication-authorization.md#authentication-flow)을 참조하세요. 

## <a name="authentication-without-provider-sdk"></a>공급자 SDK를 사용하지 않는 인증

공급자 SDK를 설정하지 않으려는 경우 Azure App Service의 Mobile Apps 기능을 로그인하도록 허용할 수 있습니다. Mobile Apps 클라이언트 SDK는 선택한 공급자에게 웹 보기를 열고 사용자를 로그인합니다. 서버에서 사용자에게 로그인하는 프로세스를 관리하고 클라이언트 SDK에서 공급자 토큰을 받지 않기 때문에 경우에 따라 블로그 및 포럼에서 "서버 흐름" 또는 "서버 제어 흐름"이라고도 합니다.

이 흐름을 시작하는 코드는 각 플랫폼에 대한 인증 자습서에 포함됩니다. 흐름의 끝에서 클라이언트 SDK에는 App Service 토큰이 있고 토큰은 애플리케이션 백 엔드에 대한 모든 요청에 자동으로 연결됩니다.

인증 흐름에 대한 자세한 내용은 [App Service 인증 흐름](../app-service/overview-authentication-authorization.md#authentication-flow)을 참조하세요. 
## <a name="more-resources"></a>추가 리소스

다음 자습서에서는 [서버 제어 흐름](../app-service/overview-authentication-authorization.md#authentication-flow)을 사용하여 모바일 클라이언트에 인증을 추가하는 방법을 보여 줍니다.

* [iOS 앱에 인증 추가][iOS]
* [Android 앱에 인증 추가][Android]
* [Windows 앱에 인증 추가][Windows]
* [Xamarin.iOS 앱에 인증 추가][Xamarin.iOS]
* [Xamarin.Android 앱에 인증 추가][Xamarin.Android]
* [ 앱에 인증 추가][Xamarin.Forms]
* [Cordova 앱에 인증 추가][Cordova]

Azure Active Directory에 대한 [클라이언트 제어 흐름](../app-service/overview-authentication-authorization.md#authentication-flow)을 사용하려는 경우 다음 리소스를 사용하세요.

* [iOS용 Active Directory 인증 라이브러리 사용][ADAL-iOS]
* [Android용 Active Directory 인증 라이브러리 사용][ADAL-Android]
* [Windows 및 Xamarin용 Active Directory 인증 라이브러리 사용][ADAL-dotnet]

Facebook에 대한 [클라이언트 제어 흐름](../app-service/overview-authentication-authorization.md#authentication-flow)을 사용하려는 경우 다음 리소스를 사용하세요.

* [iOS용 Facebook SDK 사용](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Twitter에 대한 [클라이언트 제어 흐름](../app-service/overview-authentication-authorization.md#authentication-flow)을 사용하려는 경우 다음 리소스를 사용하세요.

* [iOS용 Twitter 패브릭 사용](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Google에 대한 [클라이언트 제어 흐름](../app-service/overview-authentication-authorization.md#authentication-flow)을 사용하려는 경우 다음 리소스를 사용하세요.

* [iOS용 Google 로그인 SDK 사용](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
