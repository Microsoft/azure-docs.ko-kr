---
title: 여러 리소스에 대 한 동의 얻기 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: 사용자가 .NET 용 Microsoft Authentication Library (MSAL.NET)를 사용 하 여 여러 리소스에 대 한 사전 동의를 얻는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6333d935e1a902ba173017f8149c098f44398955
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165875"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>사용자가 MSAL.NET를 사용 하 여 여러 리소스에 대 한 동의를 가져옵니다.
Microsoft id 플랫폼 끝점에서는 한 번에 여러 리소스에 대 한 토큰을 가져올 수 없습니다. Microsoft Authentication Library for .NET (MSAL.NET)을 사용 하는 경우 획득 토큰 메서드의 범위 매개 변수는 단일 리소스에 대 한 범위만 포함 해야 합니다. 그러나 작성기 메서드를 사용 하 여 추가 범위를 지정 하 여 사전에 여러 리소스에 동의할 수 있습니다 `.WithExtraScopeToConsent` .

> [!NOTE]
> 여러 리소스에 대 한 동의를 얻는 것은 Microsoft id 플랫폼에는 적용 되지만 Azure AD B2C에는 적용 되지 않습니다. Azure AD B2C은 사용자 동의가 아닌 관리자 동의만 지원 합니다.

예를 들어 두 개의 범위가 있는 두 개의 리소스가 있는 경우 다음을 수행 합니다.

- https: \/ /mytenant.onmicrosoft.com/customerapi (2 개 범위 `customer.read` 포함 `customer.write` )
- https: \/ /mytenant.onmicrosoft.com/vendorapi (2 개 범위 `vendor.read` 포함 `vendor.write` )

`.WithExtraScopeToConsent`다음 예제와 같이 *extraScopesToConsent* 매개 변수를 포함 하는 한정자를 사용 해야 합니다.

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

이렇게 하면 첫 번째 web API에 대 한 액세스 토큰을 얻을 수 있습니다. 그런 다음 두 번째 web API에 액세스 해야 하는 경우 토큰 캐시에서 토큰을 자동으로 가져올 수 있습니다.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
