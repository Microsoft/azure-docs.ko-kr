---
title: REST API 오류 코드 - Azure Key Vault
description: 이러한 오류 코드는 Azure Key Vault 웹 서비스에 대 한 작업에 의해 반환 될 수 있습니다.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 95fb9798bc62236d785c911ec75db8d6e11f6e97
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685717"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API 오류 코드
 
Azure Key Vault 웹 서비스에 대 한 작업에 의해 반환 되는 오류 코드는 다음과 같습니다.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: 인증 되지 않은 요청

401는 Key Vault에 대 한 요청이 인증 되지 않았음을 의미 합니다. 

다음 경우 요청이 인증 됩니다.

- 키 자격 증명 모음은 호출자의 id를 알고 있습니다. 하거나
- 호출자가 Key Vault 리소스에 대 한 액세스를 시도할 수 있습니다. 

요청에서 401를 반환할 수 있는 여러 가지 이유가 있습니다.

### <a name="no-authentication-token-attached-to-the-request"></a>요청에 연결 된 인증 토큰이 없습니다. 

비밀 값을 설정 하는 PUT 요청 예제는 다음과 같습니다.

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

"Authorization" 헤더는 데이터 평면 작업에 대 한 Key Vault를 호출할 때마다 필요한 액세스 토큰입니다. 헤더가 누락 된 경우 응답은 401 이어야 합니다.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>토큰에 연결 된 올바른 리소스가 부족 합니다. 

Azure OAUTH 끝점에서 액세스 토큰을 요청 하는 경우 "resource" 라는 매개 변수는 필수입니다. 토큰 공급자는 토큰의 용도에 맞게 토큰의 범위를 사용 하므로이 값은 중요 합니다. Key Vault에 액세스 하는 **모든** 토큰에 대 한 리소스는 *https: \/ /vault.keyvault.net* (후행 슬래시 없음)입니다.

### <a name="the-token-is-expired"></a>토큰이 만료 되었습니다.

토큰은 b a s e 64로 인코딩되고 값은와 같은 웹 사이트에서 디코딩할 수 있습니다 [http://jwt.calebb.net](http://jwt.calebb.net) . 다음은 디코딩된 토큰입니다.

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

이 토큰에서 많은 중요 한 부분을 확인할 수 있습니다.

- aud (대상 그룹): 토큰의 리소스입니다. 이는입니다 `https://vault.azure.net` . 이 토큰은 그래프와 같이 명시적으로이 값과 일치 하지 않는 리소스에 대해서는 작동 하지 않습니다.
- iat (에서 발급 됨): 토큰이 발급 될 때 epoch가 시작 된 이후의 틱 수입니다.
- nbf (이전 아님):이 토큰이 유효 해지면 epoch 시작 이후 틱 수입니다.
- exp (만료):이 토큰이 만료 될 때 epoch가 시작 된 이후의 틱 수입니다.
- appid (응용 프로그램 ID):이 요청을 수행 하는 응용 프로그램 ID의 GUID입니다.
- tid (테 넌 트 ID):이 요청을 수행 하는 보안 주체의 테 넌 트 ID의 GUID입니다.

요청을 처리 하기 위해 토큰에서 모든 값을 올바르게 식별 하는 것이 중요 합니다. 모든 항목이 올바르면 요청에서 401이 발생 하지 않습니다.

### <a name="troubleshooting-401"></a>401 문제 해결

401s 키 자격 증명 모음에 대 한 요청이 만들어지기 전에 토큰 생성 지점에서 조사 되어야 합니다. 일반적으로 코드는 토큰을 요청 하는 데 사용 됩니다. 토큰이 수신 되 면 Key Vault 요청에 전달 됩니다. 코드를 로컬로 실행 하는 경우 Fiddler를 사용 하 여에 대 한 요청/응답을 캡처할 수 있습니다 `https://login.microsoftonline.com` . 요청은 다음과 같습니다.

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Mush 사용자 제공 정보는 다음과 같습니다.

- 주요 자격 증명 모음 테 넌 트 ID
- 리소스 값이 https %3 A %2 F %2 F 자격 증명 모음. azure (URL 인코딩)로 설정 되어 있습니다.
- 클라이언트 ID
- 클라이언트 암호

나머지 요청은 거의 동일 해야 합니다.

응답 액세스 토큰을 가져올 수 있는 경우에는이 토큰을 디코딩하는 방법 (위에 표시 된 것 처럼)을 통해 테 넌 트 ID, 클라이언트 ID (앱 ID) 및 리소스를 확인할 수 있습니다.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: 권한이 부족 합니다.

HTTP 403은 요청이 인증 되었지만 (요청 id를 알고 있음을 의미 함), id에는 요청 된 리소스에 대 한 액세스 권한이 없습니다. 다음과 같은 두 가지 원인이 있습니다.

- Id에 대 한 액세스 정책이 없습니다.
- 요청 하는 리소스의 IP 주소는 key vault의 방화벽 설정에서 허용 목록 되지 않습니다.

HTTP 403은 고객의 응용 프로그램이 고객이 생각 하는 클라이언트 ID를 사용 하지 않는 경우 종종 발생 합니다. 이는 일반적으로 액세스 정책이 실제 호출 id에 대해 올바르게 설정 되지 않았음을 의미 합니다.

### <a name="troubleshooting-403"></a>403 문제 해결

먼저 로깅을 설정 합니다. 이 작업을 수행 하는 방법에 대 한 지침은 [Azure Key Vault 로깅](logging.md)을 참조 하세요.

로깅이 설정 되 면 액세스 정책 또는 방화벽 정책으로 인 한 403 인지 확인할 수 있습니다.

#### <a name="error-due-to-firewall-policy"></a>방화벽 정책으로 인 한 오류

"클라이언트 주소 (00.00.00.00)는 권한이 없고 호출자는 신뢰할 수 있는 서비스가 아닙니다."

"Azure Trusted Services"의 제한 된 목록이 있습니다. Azure 웹 사이트는 신뢰할 수 있는 Azure 서비스가 **아닙니다** . 자세한 내용은 블로그 게시물 [Key Vault Azure 앱 Services에서 방화벽 액세스](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)를 참조 하세요.

Azure 웹 사이트의 IP 주소를 Key Vault에 추가 하 여 작동 하도록 해야 합니다.

액세스 정책으로 인 한 경우: 요청에 대 한 개체 ID를 찾고 사용자가 액세스 정책을 할당 하려는 개체와 개체 ID가 일치 하는지 확인 합니다. AAD에는 이름이 같은 개체가 여러 개 있을 수 있으므로 올바른 항목을 선택 하는 것이 매우 중요 합니다. 액세스 정책을 삭제 하 고 다시 추가 하면 이름이 같은 개체가 여러 개 있는지 확인할 수 있습니다.

또한 대부분의 액세스 정책에는 포털에 표시 된 것 처럼 "권한 있는 응용 프로그램"을 사용할 필요가 없습니다. 권한 있는 응용 프로그램은 거의 발생 하지 않는 "just-in-time" 인증 시나리오에 사용 됩니다. 


## <a name="http-429-too-many-requests"></a>HTTP 429: 요청이 너무 많음

요청 수가 해당 기간에 대해 명시 된 최대값을 초과 하면 제한이 발생 합니다. 제한이 발생 하면 Key Vault의 응답이 HTTP 429가 됩니다. 수행 된 요청 유형에 대 한 최대값을 입력 합니다. 예: HSM 2048 비트 키 생성은 10 초 당 5 개의 요청 이지만 다른 모든 HSM 트랜잭션은 1000 요청/10 초 제한을 갖습니다. 따라서 제한의 원인을 확인할 때 어떤 유형의 호출을 수행 하는지 이해 하는 것이 중요 합니다.
일반적으로 Key Vault에 대 한 요청은 2000 요청/10 초로 제한 됩니다. 예외는 [Key Vault 서비스 제한](service-limits.md) 에 설명 된 대로 주요 작업입니다.

### <a name="troubleshooting-429"></a>429 문제 해결
다음 기술을 사용 하 여 제한을 해결할 수 있습니다.

- 요청 된 리소스에 대 한 패턴이 있는지 확인 하 고 호출 하는 응용 프로그램에서이를 캐시 하려고 시도 하 여 Key Vault에 대 한 요청 수를 줄입니다. 

- Key Vault 제한이 발생 하면 다시 시도 하는 데 지 수 백오프를 사용 하도록 요청 하는 코드를 조정 합니다. 이 알고리즘은 [앱을 제한 하는 방법에](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits) 설명 되어 있습니다.

- 캐싱을 통해 요청 수를 줄일 수 없고 시간 제한이 백오프 작동 하지 않는 경우 여러 키 자격 증명 모음으로 키를 분할 하는 것이 좋습니다. 단일 구독에 대 한 서비스 제한은 개별 Key Vault 제한 보다 5 배입니다. 키 자격 증명 모음을 5 개 이상 사용 하는 경우 여러 구독을 사용 하도록 고려해 야 합니다. 

제한 증가 요청을 포함 한 자세한 지침은 다음을 참조 하세요. [Key Vault 제한 지침](overview-throttling.md)


