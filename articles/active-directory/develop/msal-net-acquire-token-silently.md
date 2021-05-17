---
title: 캐시에서 토큰 획득(MSAL.NET)
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET(MSAL.NET)을 사용하여 토큰 캐시에서 자동으로 액세스 토큰을 획득하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 94e4a4d5b80e246ce822e977deafe5c41c9ff4ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064779"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>MSAL.NET를 사용하여 토큰 캐시에서 토큰 가져오기

Microsoft Authentication Library for .NET(MSAL.NET)을 사용하여 액세스 토큰을 가져오면 토큰이 캐시됩니다. 애플리케이션에 토큰이 필요한 경우 먼저 `AcquireTokenSilent` 메서드를 호출하여 허용되는 토큰이 캐시에 있는지 확인해야 합니다. 대부분의 경우 캐시의 토큰에 따라 더 많은 범위가 있는 다른 토큰을 획득하게 됩니다. 또한 토큰 캐시에 새로 고침 토큰도 포함되어 있으므로 만료 기간이 가까워질 때 토큰을 새로 고칠 수 있습니다.

권장되는 패턴은 `AcquireTokenSilent` 메서드를 먼저 호출하는 것입니다.  `AcquireTokenSilent`가 실패하면 다른 메서드를 사용하여 토큰을 획득합니다.

다음 예제에서 애플리케이션은 먼저 토큰 캐시에서 토큰을 가져오려고 시도합니다.  `MsalUiRequiredException` 예외가 throw되면 애플리케이션에서 토큰을 대화형으로 가져옵니다. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
