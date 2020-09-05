---
title: 인증 토큰 캐시
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 인증 토큰을 캐시 하는 방법을 보여 줍니다.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 6850012272251c910362b61363dacd9090976680
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918420"
---
# <a name="how-to-cache-the-authentication-token"></a>인증 토큰을 캐시 하는 방법

이 문서에서는 응용 프로그램의 성능을 향상 시키기 위해 인증 토큰을 캐시 하는 방법을 보여 줍니다.

## <a name="using-aspnet"></a>ASP.NET 사용

토큰을 획득 하는 데 사용 되는 **system.identitymodel. ActiveDirectory** NuGet 패키지를 가져옵니다. 다음으로, `AuthenticationResult` [몰입 형 판독기 리소스를 만들](./how-to-create-immersive-reader.md)때 얻은 인증 값을 사용 하 여 다음 코드를 사용 하 여을 가져옵니다.

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

개체에는 `AuthenticationResult` `AccessToken` SDK를 사용 하 여 몰입 형 판독기를 시작할 때 사용 하는 실제 토큰의 속성이 있습니다. 또한 `ExpiresOn` 토큰이 만료 되는 경우를 나타내는 속성도 있습니다. 몰입 형 판독기를 시작 하기 전에 토큰이 만료 되었는지 여부를 확인 하 고 만료 된 경우에만 새 토큰을 획득할 수 있습니다.

## <a name="using-nodejs"></a>Node.JS 사용

[**Npm 패키지를 프로젝트**](https://www.npmjs.com/package/request) 에 추가 합니다. [몰입 형 판독기 리소스를 만들](./how-to-create-immersive-reader.md)때 얻은 인증 값을 사용 하 여 토큰을 가져오려면 다음 코드를 사용 합니다.

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

`expires_on`속성은 토큰이 만료 되는 날짜와 시간으로, 1970 UTC 이후의 초 수로 표시 됩니다. 토큰의 만료 여부를 확인 하려면이 값을 사용 하 여 새 토큰을 획득 하려고 시도 합니다.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기