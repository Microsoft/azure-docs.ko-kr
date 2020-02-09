---
title: Xamarin iOS 고려 사항 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL.NET (Microsoft Authentication Library for .NET)에서 Xamarin iOS를 사용 하는 경우의 특정 고려 사항에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7706a7f06cedde783e53a24ff385fa2376edcb93
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083539"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>MSAL.NET를 사용 하 여 Xamarin iOS 관련 고려 사항
Xamarin iOS에서 MSAL.NET 사용 시 고려해 야 할 몇 가지 고려 사항이 있습니다.

- [IOS 12 및 인증의 알려진 문제](#known-issues-with-ios-12-and-authentication)
- [`AppDelegate`에서 `OpenUrl` 함수를 재정의 하 고 구현 합니다.](#implement-openurl)
- [키 집합 그룹 사용](#enable-keychain-access)
- [토큰 캐시 공유 사용](#enable-token-cache-sharing-across-ios-applications)
- [키 집합 액세스 사용](#enable-keychain-access)

## <a name="implement-openurl"></a>OpenUrl 구현

먼저 `FormsApplicationDelegate` 파생 클래스의 `OpenUrl` 메서드를 재정의 하 고 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`를 호출 해야 합니다.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

또한 URL 체계를 정의 하 고, 앱에서 다른 앱을 호출 하 고, 리디렉션 URL에 특정 양식을 사용 하 고,이 리디렉션 URL을 등록 하는 데 필요한 권한을 요청 해야 합니다. [Azure Portal](https://portal.azure.com)

### <a name="enable-keychain-access"></a>키 집합 액세스 사용

키 집합 액세스를 사용 하도록 설정 하려면 응용 프로그램에 키 집합 액세스 그룹이 있어야 합니다.
아래와 같이 응용 프로그램을 만들 때 `WithIosKeychainSecurityGroup()` api를 사용 하 여 키 집합 액세스 그룹을 설정할 수 있습니다.

캐시와 Single Sign-On를 활용 하려면 모든 응용 프로그램에서 키 집합 액세스 그룹을 동일한 값으로 설정 해야 합니다.

MSAL v4. x를 사용 하는 예제는 다음과 같습니다.
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

이러한 변경은 아래 액세스 그룹 또는 사용자 고유의 액세스 그룹을 사용 하 여 `Entitlements.plist` 파일에서 키 집합 액세스를 사용 하도록 설정 하는 것 *외에* 도 사용 됩니다.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

`WithIosKeychainSecurityGroup()` api를 사용 하는 경우 xcode를 사용 하 여 응용 프로그램을 빌드할 때 동일한 작업을 수행 하기 때문에 MSAL이 응용 프로그램의 *팀 ID* (AppIdentifierPrefix)의 끝에 보안 그룹을 자동으로 추가 합니다. 자세한 내용은 [iOS 자격 설명서](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)를 참조 하세요. 따라서 자격에는 `Entitlements.plist`의 키 집합 액세스 그룹 앞에 `$(AppIdentifierPrefix)`를 포함 해야 합니다.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>IOS 응용 프로그램에서 토큰 캐시 공유 사용

MSAL 2.x에서 여러 응용 프로그램에 걸쳐 토큰 캐시를 유지 하는 데 사용할 키 집합 액세스 그룹을 지정할 수 있습니다. 이 설정을 사용 하면 [ADAL.NET](https://aka.ms/adal-net), MSAL.NET 응용 프로그램 및 ADAL을 사용 하 여 개발한 네이티브 iOS 응용 프로그램을 포함 하 여 동일한 키 집합 액세스 그룹을 포함 하는 여러 응용 프로그램 간에 토큰 캐시를 공유할 수 있습니다 [. Objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 또는 [msal. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)

토큰 캐시를 공유 하면 동일한 키 집합 액세스 그룹을 사용 하는 모든 응용 프로그램 간에 Single Sign-On 수 있습니다.

이 캐시 공유를 사용 하도록 설정 하려면 ' WithIosKeychainSecurityGroup () ' 메서드를 사용 하 여 위의 예제와 같이 동일한 캐시를 공유 하는 모든 응용 프로그램에서 키 집합 액세스 그룹을 동일한 값으로 설정 해야 합니다.

이전에는 `WithIosKeychainSecurityGroup()` api를 사용할 때마다 MSAL에서 $ (AppIdentifierPrefix)를 추가 했습니다. AppIdentifierPrefix 또는 "팀 ID"는 동일한 게시자가 만든 응용 프로그램만 키 집합 액세스를 공유할 수 있도록 하는 데 사용 되기 때문입니다.

> [!NOTE]
> **`KeychainSecurityGroup` 속성은 사용 되지 않습니다.**
> 
> 이전에는 MSAL 2.x에서 개발자가 `KeychainSecurityGroup` 속성을 사용할 때 TeamId 접두사를 포함 해야 했습니다.
>
>  MSAL 2.7. x에서 새 `iOSKeychainSecurityGroup` 속성을 사용 하는 경우 MSAL은 런타임 중에 TeamId 접두사를 확인 합니다. 이 속성을 사용 하는 경우 값은 TeamId 접두사를 포함 하지 않아야 합니다.
>  이전 `KeychainSecurityGroup` 속성은 이제 사용 되지 않으므로 TeamId를 제공 하지 않아도 되는 새 `iOSKeychainSecurityGroup` 속성을 사용 합니다.

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator 사용

응용 프로그램에서 Microsoft Authenticator (broker)를 사용 하 여 다음을 사용할 수 있습니다.

- SSO (Single Sign On). 사용자는 각 응용 프로그램에 로그인 할 필요가 없습니다.
- 장치 id입니다. 장치 인증서에 액세스 하 여 작업 공간에 연결 된 장치에서 생성 되었습니다. 테넌트 관리자가 장치와 관련 된 조건부 액세스를 사용 하도록 설정 하면 응용 프로그램이 준비 됩니다.
- 응용 프로그램 id 확인. 응용 프로그램은 broker를 호출할 때 리디렉션 url을 전달 하 고 broker는이를 확인 합니다.

Broker를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Xamarin iOS 및 Android 응용 프로그램에서 Microsoft Authenticator 또는 Microsoft Intune 회사 포털 사용](msal-net-use-brokers-with-xamarin-apps.md)을 참조 하세요.

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Xamarin iOS 관련 속성을 보여 주는 샘플

자세한 내용은 다음 샘플의 readme.md 파일에 있는 [IOS 관련 고려 사항](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) 단락에서 제공 됩니다.

샘플 | 플랫폼 | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | 간단한 Xamarin Forms 앱은 MSAL을 사용 하 여 Azure AD v2.0 엔드포인트을 통해 MSA 및 Azure AD를 인증 하 고 결과 토큰을 사용 하 여 Microsoft Graph에 액세스 하는 방법을 보여주는 합니다.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12 및 인증의 알려진 문제
Microsoft는 iOS12와 일부 인증 유형 간의 비 호환성에 대 한 정보를 제공 하기 위해 [보안 권고](https://github.com/aspnet/AspNetCore/issues/4647) 를 출시 했습니다. 비호환은 소셜, WSFed 및 OIDC 로그인을 중단 합니다. 또한이 권고는 개발자가 응용 프로그램에 ASP.NET에 의해 추가 된 현재 보안 제한을 제거 하 여 iOS12와 호환 되도록 하는 데 도움이 되는 지침을 제공 합니다.  

Xamarin iOS에서 MSAL.NET 응용 프로그램을 개발할 때 iOS 12에서 웹 사이트에 로그인 하려고 하면 무한 루프가 표시 될 수 있습니다 (이 [ADAL 문제와](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)비슷함). 

또한이 [WebKit 문제](https://bugs.webkit.org/show_bug.cgi?id=188165)에 설명 된 대로 IOS 12 Safari를 사용 하 여 ASP.NET Core oidc 인증에서 중단 되는 것을 볼 수 있습니다.
