---
title: MSAL.NET로 broker를 사용 하 여 Xamarin Android 앱 마이그레이션
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator 또는 Intune 회사 포털를 사용 하는 Xamarin Android 앱을 ADAL.NET에서 MSAL.NET로 마이그레이션하는 방법에 대해 알아봅니다.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47902b29da1dfe20fb54d633a0559e6a337fd771
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89183634"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>ADAL.NET에서 MSAL.NET로 broker를 사용 하는 Android 응용 프로그램 마이그레이션

현재 .NET 용 Azure Active Directory 인증 라이브러리 (ADAL.NET) 및 [authentication broker](brokered-auth.md)를 사용 하는 Xamarin Android 앱이 있는 경우 MSAL.NET ( [Microsoft authentication library for .net ](msal-overview.md) )로 마이그레이션해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Xamarin Android 앱은 이미 broker ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) 또는 [Intune 회사 포털](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal))와 통합 되었으며 MSAL.NET로 마이그레이션해야 하는 ADAL.NET 있습니다.

## <a name="step-1-enable-the-broker"></a>1 단계: broker 사용

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
ADAL.NET에서 broker 지원은 인증 별 컨텍스트를 기준으로 설정 됩니다.

Broker를 호출 하려면 `useBroker` 생성자에서을 *true* 로 설정 해야 했습니다 `PlatformParameters` .

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

Android 용 플랫폼별 페이지 렌더러 코드에서 `useBroker` 플래그를 true로 설정 합니다.

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

그런 다음 토큰 가져오기 호출에 매개 변수를 포함 합니다.

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
MSAL.NET에서 broker 지원은 PublicClientApplication 기준으로 설정 됩니다.

`WithBroker()`기본 매개 변수 (true로 설정 됨)를 사용 하 여 broker를 호출 합니다.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

그런 다음 AcquireToken 호출에서 다음을 호출 합니다.

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>2 단계: 작업 설정

ADAL.NET에서 [1 단계: Broker 사용](#step-1-enable-the-broker)에 표시 된 것 처럼 작업 (일반적으로 mainactivity)을 platformparameters의 일부로 전달 했습니다.

또한 MSAL.NET는 활동을 사용 하지만 broker가 없는 일반 Android 사용에는 필요 하지 않습니다. Broker를 사용 하려면 broker에서 응답을 보내고 받도록 작업을 설정 합니다.

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
활동은 Android 관련 플랫폼의 PlatformParameters에 전달 됩니다.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

MSAL.NET에서 다음 두 가지 작업을 수행 하 여 Android에 대 한 작업을 설정 합니다.

1. 에서 `MainActivity.cs` `App.RootViewController`  broker에 대 한 호출을 사용 하 여 작업을 확인 하려면를로 설정 합니다 `MainActivity` .

    올바르게 설정 되지 않은 경우 다음 오류가 발생할 수 있습니다. `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. AcquireTokenInteractive 호출에서를 사용 하 `.WithParentActivityOrWindow(App.RootViewController)` 고 사용할 활동에 대 한 참조를 전달 합니다. 이 예에서는 MainActivity를 사용 합니다.

**예:**

*App.cs*:

```CSharp
   public static object RootViewController { get; set; }
```

*MainActivity.cs*:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

AcquireToken 호출에서 다음을 수행 합니다.

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>다음 단계

Xamarin과 함께 MSAL.NET를 사용할 때의 Android 관련 고려 사항에 대 한 자세한 내용은 [구성 요구 사항 및 Xamarin Android with MSAL.NET 문제 해결 팁](msal-net-xamarin-android-considerations.md)을 참조 하세요.