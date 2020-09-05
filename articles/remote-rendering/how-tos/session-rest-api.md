---
title: 세션 관리 REST API
description: 세션을 관리 하는 방법을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: c27c5fae45f7cde57f2db12c05107d2b77b90a2c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89012384"
---
# <a name="use-the-session-management-rest-api"></a>세션 관리 REST API 사용

Azure 원격 렌더링 기능을 사용 하려면 *세션*을 만들어야 합니다. 각 세션은 Azure에서 할당 되는 VM (가상 머신)에 해당 하며 클라이언트 장치가 연결 될 때까지 대기 합니다. 장치가 연결 되 면 VM은 요청 된 데이터를 렌더링 하 고 결과를 비디오 스트림으로 사용 합니다. 세션을 만드는 동안 실행 하려는 서버 종류를 선택 하 여 가격 책정을 결정 합니다. 세션이 더 이상 필요 하지 않으면 중지 되어야 합니다. 수동으로 중지 되지 않은 경우 세션의 *임대 시간이* 만료 되 면 자동으로 종료 됩니다.

서비스의 사용을 보여 주는 *RenderingSession.ps1*이라는 *스크립트* 폴더의 [ARR 샘플 리포지토리에서](https://github.com/Azure/azure-remote-rendering) PowerShell 스크립트를 제공 합니다. 스크립트 및 해당 구성은 여기에 설명 되어 있습니다. [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)

> [!TIP]
> 이 페이지에 나열 된 PowerShell 명령은 서로를 보완 하기 위한 것입니다. 동일한 PowerShell 명령 프롬프트 내에서 모든 스크립트를 순서 대로 실행 하는 경우 서로를 기반으로 빌드됩니다.

## <a name="regions"></a>영역

요청을 보낼 기본 Url에 대 한 [사용 가능한 지역 목록을](../reference/regions.md) 참조 하세요.

아래 샘플 스크립트의 경우 *westus2*지역을 선택 합니다.

### <a name="example-script-choose-an-endpoint"></a>예제 스크립트: 끝점 선택

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>계정

원격 렌더링 계정이 없는 경우 [새로 만듭니다](create-an-account.md). 각 리소스는 세션 Api 전체에서 사용 되는 *accountId*로 식별 됩니다.

### <a name="example-script-set-accountid-and-accountkey"></a>예제 스크립트: accountId 및 accountKey 설정

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>일반 요청 헤더

* *권한 부여* 헤더의 값은 "" 이어야 합니다 `Bearer TOKEN` . 여기서 " `TOKEN` "는 [보안 토큰 서비스에서 반환](tokens.md)된 인증 토큰입니다.

### <a name="example-script-request-a-token"></a>예제 스크립트: 토큰 요청

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>일반적인 응답 헤더

* 제품 팀에서 *MS CV* 헤더를 사용 하 여 서비스 내에서 호출을 추적할 수 있습니다.

## <a name="create-a-session"></a>세션 만들기

이 명령은 세션을 만듭니다. 새 세션의 ID를 반환 합니다. 다른 모든 명령에 대 한 세션 ID가 필요 합니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/create | POST |

**요청 본문:**

* maxLeaseTime (timespan): 세션을 자동으로 해제 하는 시간 제한 값
* 모델 (배열): 미리 로드할 자산 컨테이너 Url
* size (string): 구성할 서버 크기 ([**"standard"**](../reference/vm-sizes.md) 또는 [**"premium"**](../reference/vm-sizes.md))입니다. 특정 [크기 제한](../reference/limits.md#overall-number-of-polygons)을 참조 하세요.

**보낸**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 202 | -sessionId: GUID | 성공 |

### <a name="example-script-create-a-session"></a>예제 스크립트: 세션 만들기

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>예제 스크립트: 저장소 sessionId

위의 요청 응답에는 모든 후속 요청에 필요한 **sessionId**가 포함 되어 있습니다.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="modify-and-query-session-properties"></a>세션 속성 수정 및 쿼리

기존 세션의 매개 변수를 쿼리하거나 수정 하는 몇 가지 명령이 있습니다.

> [!CAUTION]
모든 REST 호출의 경우와 마찬가지로, 이러한 명령을 너무 자주 보내면 서버가 제한을 초과 하 여 결국 오류가 반환 됩니다. 이 경우 상태 코드는 429 ("요청이 너무 많음")입니다. 이에 대 한 규칙에 따라 **이후 호출 사이에 5-10 초의**지연이 발생 합니다.

### <a name="update-session-parameters"></a>세션 매개 변수 업데이트

이 명령은 세션의 매개 변수를 업데이트 합니다. 현재 세션의 임대 시간만 확장할 수 있습니다.

> [!IMPORTANT]
> 임대 시간은 항상 세션의 시작 이후 총 시간으로 제공 됩니다. 즉, 임대 시간이 1 시간인 세션을 만들고 다른 시간에 대 한 임대 시간을 연장 하려면 해당 maxLeaseTime를 2 시간으로 업데이트 해야 합니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessions/*sessionId* | 패치 |

**요청 본문:**

* maxLeaseTime (timespan): 세션을 자동으로 해제 하는 시간 제한 값

**보낸**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 200 | | Success |

#### <a name="example-script-update-a-session"></a>예제 스크립트: 세션 업데이트

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

### <a name="get-active-sessions"></a>활성 세션 가져오기

이 명령은 활성 세션 목록을 반환 합니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | GET |

**보낸**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 200 | -sessions: 세션 속성의 배열입니다. | 세션 속성에 대 한 설명은 "세션 속성 가져오기" 섹션을 참조 하세요. |

#### <a name="example-script-query-active-sessions"></a>예제 스크립트: 쿼리 활성 세션

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

### <a name="get-sessions-properties"></a>세션 속성 가져오기

이 명령은 VM 호스트 이름 등의 세션에 대 한 정보를 반환 합니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId*/properties | GET |

**보낸**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 200 | -message: 문자열<br/>-sessionElapsedTime: timespan<br/>-sessionHostname: 문자열<br/>-sessionId: string<br/>-sessionMaxLeaseTime: timespan<br/>-sessionSize: enum<br/>-sessionStatus: enum | enum sessionStatus {시작, 준비, 중지, 중지, 만료, 오류}<br/>상태가 ' 오류 ' 또는 ' 만료 됨 ' 이면 메시지에 추가 정보가 포함 됩니다. |

#### <a name="example-script-get-session-properties"></a>예제 스크립트: 세션 속성 가져오기

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>세션 중지

이 명령은 세션을 중지 합니다. 할당 된 VM은 즉시 회수 됩니다.

| URI | 방법 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId* | Delete |

**보낸**

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 204 | | 성공 |

### <a name="example-script-stop-a-session"></a>예제 스크립트: 세션 중지

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

예제 출력:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>다음 단계

* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
