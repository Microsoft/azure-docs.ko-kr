---
title: 디먼 앱에서 web API 호출-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 디먼 앱을 빌드하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 338b638d6b33bcbbb5cf377643a96c71b0d314bd
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775185"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>웹 Api를 호출 하는 디먼 앱-앱에서 web API 호출

.NET 데몬 앱은 웹 API를 호출할 수 있습니다. .NET 데몬 앱은 미리 승인 된 여러 웹 Api를 호출할 수도 있습니다.

## <a name="calling-a-web-api-from-a-daemon-application"></a>디먼 응용 프로그램에서 web API 호출

토큰을 사용 하 여 API를 호출 하는 방법은 다음과 같습니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>여러 Api 호출

디먼 앱의 경우 호출 하는 웹 Api를 미리 승인 해야 합니다. 디먼 앱에는 증분 동의가 없습니다. 사용자 상호 작용이 없습니다. 테넌트 관리자는 응용 프로그램 및 모든 API 권한에 대해 사전에 동의를 제공 해야 합니다. 여러 Api를 호출 하려는 경우 `AcquireTokenForClient`를 호출할 때마다 각 리소스에 대 한 토큰을 획득 해야 합니다. MSAL은 응용 프로그램 토큰 캐시를 사용 하 여 불필요 한 서비스 호출을 방지 합니다.

## <a name="next-steps"></a>다음 단계

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [디먼 앱-프로덕션으로 이동](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [디먼 앱-프로덕션으로 이동](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [디먼 앱-프로덕션으로 이동](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
