---
title: Azure Functions에 대한 Microsoft Graph 바인딩
description: Azure Functions에서 Microsoft Graph 트리거 및 바인딩을 사용하는 방법을 파악합니다.
services: functions
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: f112bdf9eacf51852659ab49a5673b0c8bfb0e46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438111"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Azure Functions에 대한 Microsoft Graph 바인딩

이 문서에서는 Azure Functions에서 Microsoft Graph 트리거 및 바인딩을 구성하고 사용하는 방법에 대해 설명합니다. 이러한 방법을 배우고 나면 Azure Functions를 사용하여 [Microsoft Graph](https://developer.microsoft.com/graph)에서 데이터, 자세한 정보 및 이벤트를 작업할 수 있습니다.

Microsoft Graph 확장에는 다음과 같은 바인딩이 제공됩니다.
- 모든 Microsoft Graph API와 상호 작용할 수 있게 해주는 [인증 토큰 입력 바인딩](#token-input).
- Excel에서 데이터를 읽을 수 있게 해주는 [Excel 테이블 입력 바인딩](#excel-input).
- Excel 데이터를 수정할 수 있게 해주는 [Excel 테이블 출력 바인딩](#excel-output).
- OneDrive에서 파일을 읽을 수 있게 해주는 [OneDrive 파일 입력 바인딩](#onedrive-input).
- OneDrive에 파일을 쓸 수 있게 해주는 [OneDrive 파일 출력 바인딩](#onedrive-output).
- Outlook을 통해 전자 메일을 보낼 수 있게 해주는 [Outlook 메시지 출력 바인딩](#outlook-output).
- Microsoft Graph의 이벤트에 대응할 수 있게 해주는 [Microsoft Graph 웹후크 트리거 및 바인딩](#webhooks) 컬렉션.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph 바인딩은 현재 Azure Functions 버전 2.x에서 미리 보기로 제공됩니다. Functions 버전 1.x에서 지원되지 않습니다.

## <a name="packages"></a>패키지

인증 토큰 입력 바인딩은 [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet 패키지에서 제공됩니다. 다른 Microsoft Graph 바인딩은 [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) 패키지에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>확장 설정

Microsoft Graph 바인딩은 _바인딩 확장_을 통해 제공됩니다. 바인딩 확장은 Azure Functions 런타임의 선택적 구성 요소입니다. 이 섹션에서는 Microsoft Graph 및 인증 토큰 확장을 설정하는 방법을 설명합니다.

### <a name="enabling-functions-20-preview"></a>Functions 2.0 미리 보기를 사용하도록 설정

바인딩 확장은 Azure Functions 2.0 미리 보기에서만 사용할 수 있습니다. 

미리 보기 2.0 버전의 Functions 런타임을 사용하도록 함수 앱을 설정하는 방법에 대한 내용은 [버전 2.0 런타임을 대상으로 지정하는 방법](set-runtime-version.md)을 참조하세요.

### <a name="installing-the-extension"></a>확장 설치

Azure Portal에서 확장을 설치하려면 확장을 참조하는 템플릿 또는 바인딩으로 이동합니다. 새 함수를 만들고, 템플릿 선택 화면에서 "Microsoft Graph" 시나리오를 선택합니다. 이 시나리오의 템플릿 중 하나를 선택합니다. 또는 기존 함수의 "통합" 탭으로 이동하여 이 문서에서 다루는 바인딩 중 하나를 선택할 수도 있습니다.

어떤 방법을 선택하든 설치할 확장을 지정하는 경고가 표시됩니다. **설치**를 클릭하여 확장을 가져옵니다. 각 확장을 함수 앱마다 한 번씩만 설치하면 됩니다. 

> [!Note] 
> 포털 내 설치 프로세스는 소비 계획에 대해 최대 10분이 소요될 수 있습니다.

Visual Studio를 사용하는 경우 [앞서 이 아티클에서 나열된 NuGet 패키지](#packages)를 설치하여 확장을 설치할 수 있습니다.

### <a name="configuring-authentication--authorization"></a>인증/권한 부여 구성

이 문서에서 설명하는 바인딩은 반드시 ID를 사용해야 합니다. 이를 통해 Microsoft Graph가 권한을 적용하고 상호 작용을 감사할 수 있습니다. ID는 애플리케이션에 액세스하는 사용자일 수도 있고 애플리케이션 자체일 수도 있습니다. 이 ID를 구성하려면 Azure Active Directory를 사용하여 [App Service 인증/권한 부여](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)를 설정합니다. 그리고 함수에 필요한 리소스 권한을 요청해야 합니다.

> [!Note] 
> Microsoft Graph 확장은 Azure AD 인증만 지원합니다. 사용자는 회사 또는 학교 계정으로 로그인해야 합니다.

Azure Portal을 사용하는 경우 확장을 설치할지 묻는 메시지 아래에 경고가 표시됩니다. 이 경고는 App Service 인증/권한 부여를 구성하고, 템플릿 또는 바인딩에 필요한 사용 권한을 요구하라는 메시지를 표시합니다. **지금 Azure AD 구성** 또는 **지금 권한 추가**를 적절히 클릭합니다.



<a name="token-input"></a>
## <a name="auth-token"></a>인증 토큰

인증 토큰 입력 바인딩은 지정된 리소스에 대한 Azure AD 토큰을 획득하여 코드에 문자열로 제공합니다. 애플리케이션이 권한을 갖고 있는 모든 것을 리소스로 사용할 수 있습니다. 

이 섹션은 다음 하위 섹션을 포함합니다.

* [예제](#auth-token---example)
* [특성](#auth-token---attributes)
* [구성](#auth-token---configuration)
* [사용 현황](#auth-token---usage)

### <a name="auth-token---example"></a>인증 토큰 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>인증 토큰 - C# 스크립트 예제

다음 예제에서는 사용자 프로필 정보를 가져옵니다.

*function.json* 파일은 토큰 입력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 스크립트 코드는 토큰을 사용하여 Microsoft Graph에 대한 HTTP 호출을 만들고 그 결과를 반환합니다.

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>인증 토큰 - JavaScript 예제

다음 예제에서는 사용자 프로필 정보를 가져옵니다.

*function.json* 파일은 토큰 입력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 JavaScript 코드는 토큰을 사용하여 Microsoft Graph에 대한 HTTP 호출을 만들고 그 결과를 반환합니다.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>인증 토큰 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) 특성을 사용합니다.

### <a name="auth-token---configuration"></a>인증 토큰 - 구성

다음 표에서는 *function.json* 파일 및 `Token` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**name**||필수 - 인증 토큰의 함수 코드에 사용되는 변수 이름입니다. [코드에서 인증 토큰 입력 바인딩 사용](#token-input-code)을 참조하세요.|
|**type**||필수 - `token`으로 설정해야 합니다.|
|**direction**||필수 - `in`으로 설정해야 합니다.|
|**identity**|**ID**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId**|**UserId**  |_identity_ 가 `userFromId`으로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|**UserToken**|_identity_ 가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**리소스**|**resource**|필수 - 토큰이 요청되는 Azure AD 리소스 URL입니다.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>인증 토큰 - 사용

바인딩 자체는 Azure AD 권한이 전혀 필요 없지만, 토큰이 사용되는 방식에 따라 추가 권한을 요청해야 할 수도 있습니다. 토큰을 사용하여 액세스하려는 리소스의 요구 사항을 확인하세요.

토큰은 항상 코드에 문자열로 표시됩니다.

> [!Note]
> `userFromId`, `userFromToken` 또는 `userFromRequest` 옵션 중 하나를 사용하여 로컬로 개발할 경우 필요한 토큰을 [수동으로 가져오고](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) 호출 클라이언트 애플리케이션의 `X-MS-TOKEN-AAD-ID-TOKEN` 요청 헤더에 지정할 수 있습니다.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel 입력

Excek 테이블 입력 바인딩은 OneDrive에 저장된 Excel 테이블의 콘텐츠를 읽습니다.

이 섹션은 다음 하위 섹션을 포함합니다.

* [예제](#excel-input---example)
* [특성](#excel-input---attributes)
* [구성](#excel-input---configuration)
* [사용 현황](#excel-input---usage)

### <a name="excel-input---example"></a>Excel 입력 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel 입력 - C# 스크립트 예제

다음 *function.json* 파일은 Excel 입력 바인딩을 사용하여 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 스크립트 코드는 지정된 테이블의 내용을 읽어서 사용자에게 반환합니다.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel 입력 - JavaScript 예제

다음 *function.json* 파일은 Excel 입력 바인딩을 사용하여 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 JavaScript 코드는 지정된 테이블의 내용을 읽어서 사용자에게 반환합니다.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel 입력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) 특성을 사용합니다.

### <a name="excel-input---configuration"></a>Excel 입력 - 구성

다음 표에서는 *function.json* 파일 및 `Excel` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**name**||필수 - Excel 테이블의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Excel 테이블 입력 바인딩 사용](#excel-input-code)을 참조하세요.|
|**type**||필수 - `excel`으로 설정해야 합니다.|
|**direction**||필수 - `in`으로 설정해야 합니다.|
|**identity**|**ID**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId**|**UserId**  |_identity_ 가 `userFromId`으로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|**UserToken**|_identity_ 가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**path**|**Path**|필수 - OneDrive에서 Excel 통합 문서의 경로입니다.|
|**worksheetName**|**WorksheetName**|테이블이 검색되는 워크시트입니다.|
|**tableName**|**TableName**|테이블의 이름입니다. 지정하지 않으면 워크시트의 콘텐츠가 사용됩니다.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel 입력 - 사용

이 바인딩에는 다음 Azure AD 권한이 필요합니다.

|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자 파일 읽기|

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- string[][]
- Microsoft.Graph.WorkbookTable
- 사용자 지정 개체 형식(구조 모델 바인딩 사용)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel 출력

Excel 출력 바인딩은 OneDrive에 저장된 Excel 테이블의 콘텐츠를 수정합니다.

이 섹션은 다음 하위 섹션을 포함합니다.

* [예제](#excel-output---example)
* [특성](#excel-output---attributes)
* [구성](#excel-output---configuration)
* [사용 현황](#excel-output---usage)

### <a name="excel-output---example"></a>Excel 출력 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel 출력 - C# 스크립트 예제

다음 예제에서는 Excel 테이블에 행을 추가합니다.

*function.json* 파일은 Excel 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

이 C# 스크립트 코드는 쿼리 문자열의 입력을 기반으로 테이블에(단일 열로 가정) 새 행을 추가합니다.

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Excel 출력 - JavaScript 예제

다음 예제에서는 Excel 테이블에 행을 추가합니다.

*function.json* 파일은 Excel 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 JavaScript 코드는 쿼리 문자열의 입력을 기반으로 테이블에(단일 열로 가정) 새 행을 추가합니다.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel 출력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) 특성을 사용합니다.

### <a name="excel-output---configuration"></a>Excel 출력 - 구성

다음 표에서는 *function.json* 파일 및 `Excel` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**name**||필수 - 인증 토큰의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Excel 테이블 출력 바인딩 사용](#excel-output-code)을 참조하세요.|
|**type**||필수 - `excel`으로 설정해야 합니다.|
|**direction**||필수 - `out`으로 설정해야 합니다.|
|**identity**|**ID**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**UserId** |**userId** |_identity_ 가 `userFromId`로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|**UserToken**|_identity_ 가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**path**|**Path**|필수 - OneDrive에서 Excel 통합 문서의 경로입니다.|
|**worksheetName**|**WorksheetName**|테이블이 검색되는 워크시트입니다.|
|**tableName**|**TableName**|테이블의 이름입니다. 지정하지 않으면 워크시트의 콘텐츠가 사용됩니다.|
|**updateType**|**UpdateType**|필수 - 테이블에 적용해야 하는 변경 형식입니다. 다음 값 중 하나입니다.<ul><li><code>update</code> - OneDrive에 있는 테이블의 콘텐츠를 대체합니다.</li><li><code>append</code> - 새 행을 만들어서 OneDrive에 있는 테이블의 끝에 페이로드를 추가합니다.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel 출력 - 사용

이 바인딩에는 다음 Azure AD 권한이 필요합니다.

|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자 파일에 대한 전체 액세스 권한이 있음|

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- 사용자 지정 개체 형식(구조 모델 바인딩 사용)





<a name="onedrive-input"></a>
## <a name="file-input"></a>파일 입력

OneDrive 파일 입력 바인딩은 OneDrive에 저장된 파일의 콘텐츠를 읽습니다.

이 섹션은 다음 하위 섹션을 포함합니다.

* [예제](#file-input---example)
* [특성](#file-input---attributes)
* [구성](#file-input---configuration)
* [사용 현황](#file-input---usage)

### <a name="file-input---example"></a>파일 입력 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>파일 입력 - C# 스크립트 예제

다음 예제에서는 OneDrive에 저장된 파일을 읽습니다.

*function.json* 파일은 OneDrive 파일 입력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 쿼리 문자열에 지정된 파일을 읽고 그 길이를 기록합니다.

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>파일 입력 - JavaScript 예제

다음 예제에서는 OneDrive에 저장된 파일을 읽습니다.

*function.json* 파일은 OneDrive 파일 입력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 JavaScript 코드는 쿼리 문자열에 지정된 파일을 읽고 그 길이를 반환합니다.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>파일 입력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) 특성을 사용합니다.

### <a name="file-input---configuration"></a>파일 입력 - 구성

다음 표에서는 *function.json* 파일 및 `OneDrive` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**name**||필수 - 파일의 함수 코드에 사용되는 변수 이름입니다. [코드에서 OneDrive 파일 입력 바인딩 사용](#onedrive-input-code)을 참조하세요.|
|**type**||필수 - `onedrive`으로 설정해야 합니다.|
|**direction**||필수 - `in`으로 설정해야 합니다.|
|**identity**|**ID**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId**|**UserId**  |_identity_ 가 `userFromId`으로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|**UserToken**|_identity_ 가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**path**|**Path**|필수 - OneDrive에서 파일의 경로입니다.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>파일 입력 - 사용

이 바인딩에는 다음 Azure AD 권한이 필요합니다.

|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자 파일 읽기|

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- byte[]
- Stream
- 문자열
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>파일 출력

OneDrive 파일 출력 바인딩은 OneDrive에 저장된 파일의 콘텐츠를 수정합니다.

이 섹션은 다음 하위 섹션을 포함합니다.

* [예제](#file-output---example)
* [특성](#file-output---attributes)
* [구성](#file-output---configuration)
* [사용 현황](#file-output---usage)

### <a name="file-output---example"></a>파일 출력 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>파일 출력 - C# 스크립트 예제

다음 예제에서는 OneDrive에 저장된 파일에 씁니다.

*function.json* 파일은 OneDrive 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 스크립트 코드는 쿼리 문자열에서 텍스트를 가져와서 호출자의 OneDrive 루트에 있는 텍스트 파일(이전 예제에 정의된 FunctionsTest.txt)에 기록합니다.

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>파일 출력 - JavaScript 예제

다음 예제에서는 OneDrive에 저장된 파일에 씁니다.

*function.json* 파일은 OneDrive 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 쿼리 문자열에서 텍스트를 가져와서 호출자의 OneDrive 루트에 있는 텍스트 파일(위의 config에 정의된 FunctionsTest.txt)에 기록합니다.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>파일 출력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) 특성을 사용합니다.

### <a name="file-output---configuration"></a>파일 출력 - 구성

다음 표에서는 *function.json* 파일 및 `OneDrive` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**name**||필수 - 파일의 함수 코드에 사용되는 변수 이름입니다. [코드에서 OneDrive 파일 출력 바인딩 사용](#onedrive-output-code)을 참조하세요.|
|**type**||필수 - `onedrive`으로 설정해야 합니다.|
|**direction**||필수 - `out`으로 설정해야 합니다.|
|**identity**|**ID**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**UserId** |**userId** |_identity_ 가 `userFromId`로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|**UserToken**|_identity_ 가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**path**|**Path**|필수 - OneDrive에서 파일의 경로입니다.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>파일 출력 - 사용

이 바인딩에는 다음 Azure AD 권한이 필요합니다.

|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자 파일에 대한 전체 액세스 권한이 있음|

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- byte[]
- Stream
- 문자열
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook 출력

Outlook 메시지 출력 바인딩은 Outlook을 통해 메일 메시지를 보냅니다.

이 섹션은 다음 하위 섹션을 포함합니다.

* [예제](#outlook-output---example)
* [특성](#outlook-output---attributes)
* [구성](#outlook-output---configuration)
* [사용 현황](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook 출력 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook 출력 - C# 스크립트 예제

다음 예제에서는 Outlook 통해 전자 메일을 보냅니다.

*function.json* 파일은 Outlook 메시지 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 호출자의 메일을 쿼리 문자열에 지정된 받는 사람에게 보냅니다.

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Outlook 출력 - JavaScript 예제

다음 예제에서는 Outlook 통해 전자 메일을 보냅니다.

*function.json* 파일은 Outlook 메시지 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 호출자의 메일을 쿼리 문자열에 지정된 받는 사람에게 보냅니다.

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Outlook 출력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) 특성을 사용합니다.

### <a name="outlook-output---configuration"></a>Outlook 출력 - 구성

다음 표에서는 *function.json* 파일 및 `Outlook` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**name**||필수 - 메일 메시지의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Outlook 메시지 출력 바인딩 사용](#outlook-output-code)을 참조하세요.|
|**type**||필수 - `outlook`으로 설정해야 합니다.|
|**direction**||필수 - `out`으로 설정해야 합니다.|
|**identity**|**ID**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId**|**UserId**  |_identity_ 가 `userFromId`으로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|**UserToken**|_identity_ 가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook 출력 - 사용

이 바인딩에는 다음 Azure AD 권한이 필요합니다.

|리소스|사용 권한|
|--------|--------|
|Microsoft Graph|사용자로 메일 보내기|

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- 문자열
- 사용자 지정 개체 형식(구조 모델 바인딩 사용)






## <a name="webhooks"></a>Webhook

웹후크를 사용하여 Microsoft Graph의 이벤트에 대응할 수 있습니다. 웹후크를 지원하려면 함수를 만들고, 새로 고치고, _웹후크 구독_에 반응해야 합니다. 완전한 웹후크 솔루션을 구성하려면 다음과 같은 바인딩 조합이 필요합니다.
- 들어오는 웹후크에 대응할 수 있는 [Microsoft Graph 웹후크 트리거](#webhook-trigger).
- 기존 구독을 나열하고 필요에 따라 새로 고칠 수 있는 [Microsoft Graph 웹후크 구독 입력 바인딩](#webhook-input).
- 웹후크 구독을 만들거나 삭제할 수 있는 [Microsoft Graph 웹후크 구독 출력 바인딩](#webhook-output).

바인딩 자체는 Azure AD 권한이 필요 없지만, 대응하려는 리소스 종류와 관련된 권한을 요청해야 합니다. 각 리소스 종류에 필요한 권한 목록은 [구독 권한](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0)을 참조하세요.

웹후크에 대한 자세한 내용은 [Microsoft Graph에서 웹후크 작업]을 참조하세요.





## <a name="webhook-trigger"></a>웹후크 트리거

Microsoft Graph 웹후크 트리거를 사용하면 함수가 Microsoft Graph에서 들어오는 웹후크에 대응할 수 있습니다. 이 트리거의 각 인스턴스는 한 가지 Microsoft Graph 리소스 종류에 대응할 수 있습니다.

이 섹션은 다음 하위 섹션을 포함합니다.

* [예제](#webhook-trigger---example)
* [특성](#webhook-trigger---attributes)
* [구성](#webhook-trigger---configuration)
* [사용 현황](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>웹후크 트리거 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>웹후크 트리거 - C# 스크립트 예제

다음 예제에서는 들어오는 Outlook 메시지에 대한 웹후크를 처리합니다. 웹후크 트리거를 사용하려면 [구독을 만들고](#webhook-output---example) 만료되지 않도록 [새로 고칠 수 있습니다](#webhook-subscription-refresh).

*function.json* 파일은 웹후크 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 들어오는 이메일 메시지에 반응하여 받는 사람이 보냈고 제목에 "Azure Functions"가 포함된 이메일 메시지의 본문을 기록합니다.

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>웹후크 트리거 - JavaScript 예제

다음 예제에서는 들어오는 Outlook 메시지에 대한 웹후크를 처리합니다. 웹후크 트리거를 사용하려면 [구독을 만들고](#webhook-output---example) 만료되지 않도록 [새로 고칠 수 있습니다](#webhook-subscription-refresh).

*function.json* 파일은 웹후크 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 들어오는 이메일 메시지에 반응하여 받는 사람이 보냈고 제목에 "Azure Functions"가 포함된 이메일 메시지의 본문을 기록합니다.

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>웹후크 트리거 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) 특성을 사용합니다.

### <a name="webhook-trigger---configuration"></a>웹후크 트리거 - 구성

다음 표에서는 *function.json* 파일 및 `GraphWebHookTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**name**||필수 - 메일 메시지의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Outlook 메시지 출력 바인딩 사용](#outlook-output-code)을 참조하세요.|
|**type**||필수 - `graphWebhook`으로 설정해야 합니다.|
|**direction**||필수 - `trigger`으로 설정해야 합니다.|
|**resourceType**|**ResourceType**|필수 - 이 함수가 웹후크에 응답해야 하는 그래프 리소스입니다. 다음 값 중 하나입니다.<ul><li><code>#Microsoft.Graph.Message</code> - Outlook 메시지의 변경 내용입니다.</li><li><code>#Microsoft.Graph.DriveItem</code> - OneDrive 루트 항목의 변경 내용입니다.</li><li><code>#Microsoft.Graph.Contact</code> - Outlook의 개인 연락처에 대한 변경 내용입니다.</li><li><code>#Microsoft.Graph.Event</code> - Outlook 일정 항목의 변경 내용입니다.</li></ul>|

> [!Note]
> 함수 앱은 지정된 `resourceType` 값에 대해 등록된 함수 하나만 사용할 수 있습니다.

### <a name="webhook-trigger---usage"></a>웹후크 트리거 - 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- 리소스 종류와 관련된 Microsoft Graph SDK 형식(예: `Microsoft.Graph.Message` 또는 `Microsoft.Graph.DriveItem`)
- 사용자 지정 개체 형식(구조 모델 바인딩 사용)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>웹후크 입력

Microsoft Graph 웹후크 입력 바인딩을 사용하면 이 함수 앱이 관리하는 구독 목록을 검색할 수 있습니다. 이 바인딩은 함수 앱 저장소에서 정보를 읽으므로, 앱 외부에서 작성된 다른 구독은 반영하지 않습니다.

이 섹션은 다음 하위 섹션을 포함합니다.

* [예제](#webhook-input---example)
* [특성](#webhook-input---attributes)
* [구성](#webhook-input---configuration)
* [사용 현황](#webhook-input---usage)

### <a name="webhook-input---example"></a>웹후크 입력 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>웹후크 입력 - C# 스크립트 예제

다음 예제는 호출하는 사용자에 대한 모든 구독을 가져와서 삭제합니다.

*function.json* 파일은 삭제 작업을 사용하는 구독 입력 바인딩 및 구독 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 구독을 가져와서 삭제합니다.

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>웹후크 입력 - JavaScript 예제

다음 예제는 호출하는 사용자에 대한 모든 구독을 가져와서 삭제합니다.

*function.json* 파일은 삭제 작업을 사용하는 구독 입력 바인딩 및 구독 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 구독을 가져와서 삭제합니다.

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>웹후크 입력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) 특성을 사용합니다.

### <a name="webhook-input---configuration"></a>웹후크 입력 - 구성

다음 표에서는 *function.json* 파일 및 `GraphWebHookSubscription` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**name**||필수 - 메일 메시지의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Outlook 메시지 출력 바인딩 사용](#outlook-output-code)을 참조하세요.|
|**type**||필수 - `graphWebhookSubscription`으로 설정해야 합니다.|
|**direction**||필수 - `in`으로 설정해야 합니다.|
|**filter**|**Filter**| `userFromRequest`로 설정하면 바인딩이 호출하는 사용자 소유의 구독만 검색합니다([HTTP 트리거]에만 유효).| 

### <a name="webhook-input---usage"></a>웹후크 입력 - 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- string[]
- 사용자 지정 개체 형식 배열
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>웹후크 출력

웹후크 구독 출력 바인딩을 사용하면 Microsoft Graph에서 웹후크 구독을 만들고, 삭제하고, 새로 고칠 수 있습니다.

이 섹션은 다음 하위 섹션을 포함합니다.

* [예제](#webhook-output---example)
* [특성](#webhook-output---attributes)
* [구성](#webhook-output---configuration)
* [사용 현황](#webhook-output---usage)

### <a name="webhook-output---example"></a>웹후크 출력 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>웹후크 출력 - C# 스크립트 예제

다음 예제는 구독을 만듭니다. 만료되지 않도록 [구독을 새로 고칠 수 있습니다](#webhook-subscription-refresh).

*function.json* 파일은 만들기 작업을 사용하여 구독 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 호출하는 사용자가 Outlook 메시지를 받으면 이 함수 앱에 그 사실을 알리는 웹후크를 등록합니다.

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>웹후크 출력 - JavaScript 예제

다음 예제는 구독을 만듭니다. 만료되지 않도록 [구독을 새로 고칠 수 있습니다](#webhook-subscription-refresh).

*function.json* 파일은 만들기 작업을 사용하여 구독 출력 바인딩으로 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 호출하는 사용자가 Outlook 메시지를 받으면 이 함수 앱에 그 사실을 알리는 웹후크를 등록합니다.

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>웹후크 출력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) 특성을 사용합니다.

### <a name="webhook-output---configuration"></a>웹후크 출력 - 구성

다음 표에서는 *function.json* 파일 및 `GraphWebHookSubscription` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**name**||필수 - 메일 메시지의 함수 코드에 사용되는 변수 이름입니다. [코드에서 Outlook 메시지 출력 바인딩 사용](#outlook-output-code)을 참조하세요.|
|**type**||필수 - `graphWebhookSubscription`으로 설정해야 합니다.|
|**direction**||필수 - `out`으로 설정해야 합니다.|
|**identity**|**ID**|필수 - 작업 수행에 사용되는 ID입니다. 다음 값 중 하나입니다.<ul><li><code>userFromRequest</code> - [HTTP 트리거]에만 유효합니다. 호출하는 사용자의 ID를 사용합니다.</li><li><code>userFromId</code> - 지정된 ID를 사용하여 이전에 로그인한 사용자의 ID를 사용합니다. <code>userId</code> 속성을 참조하세요.</li><li><code>userFromToken</code> - 지정된 토큰으로 표시된 ID를 사용합니다. <code>userToken</code> 속성을 참조하세요.</li><li><code>clientCredentials</code> - 함수 앱의 ID를 사용합니다.</li></ul>|
|**userId**|**UserId**  |_identity_ 가 `userFromId`으로 설정된 경우에만 필요합니다. 이전에 로그인한 사용자와 연결된 사용자 계정 ID입니다.|
|**userToken**|**UserToken**|_identity_ 가 `userFromToken`으로 설정된 경우에만 필요합니다. 함수 앱에 유효한 토큰입니다. |
|**action**|**작업**|필수 - 바인딩이 수행해야 하는 작업을 지정합니다. 다음 값 중 하나입니다.<ul><li><code>create</code> - 새 구독을 등록합니다.</li><li><code>delete</code> - 지정된 구독을 삭제합니다.</li><li><code>refresh</code> - 구독이 만료되지 않도록 지정된 구독을 새로 고칩니다.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|_action_ 이 `create`로 설정된 경우에만 필요합니다. 변경 내용이 모니터링될 Microsoft Graph 리소스를 지정합니다. [Microsoft Graph에서 웹후크 작업]을 참조하세요. |
|**changeType**|**ChangeType**|_action_ 이 `create`로 설정된 경우에만 필요합니다. 구독하는 리소스에서 알림을 발생시키는 변경 형식을 나타냅니다. 지원되는 값은 `created`, `updated`, `deleted`입니다. 쉼표로 구분된 목록을 사용하여 여러 값을 조합할 수 있습니다.|

### <a name="webhook-output---usage"></a>웹후크 출력 - 사용

이 바인딩은 .NET 함수에 다음 형식을 노출합니다.
- 문자열
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>웹후크 구독 새로 고침

구독을 새로 고치는 두 가지 방법이 있습니다.

- 애플리케이션 ID를 사용하여 모든 구독을 처리하는 방법. 이렇게 하려면 Azure Active Directory 관리자의 동의가 필요합니다. 이 방법은 Azure Functions에서 지원하는 모든 언어에 사용할 수 있습니다.
- 각 사용자 ID를 수동으로 바인딩하여 각 구독에 연결된 ID를 사용하는 방법. 이렇게 하려면 일부 사용자 지정 코드에서 바인딩을 수행해야 합니다. 이 방법은 .NET 함수에만 사용할 수 있습니다.

이 섹션에는 이러한 각 방법의 예제가 나와 있습니다.

* [앱 ID 예제](#webhook-subscription-refresh---app-identity-example)
* [사용자 ID 예제](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>웹후크 구독 새로 고침 - 앱 ID 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>앱 ID 새로 고침 - C# 스크립트 예제

다음 예제에서는 애플리케이션 ID를 사용하여 구독을 새로 고칩니다.

*function.json*은 구독 입력 바인딩 및 구독 출력 바인딩으로 타이머 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 구독을 새로 고칩니다.

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>앱 ID 새로 고침 - C# 스크립트 예제

다음 예제에서는 애플리케이션 ID를 사용하여 구독을 새로 고칩니다.

*function.json*은 구독 입력 바인딩 및 구독 출력 바인딩으로 타이머 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 구독을 새로 고칩니다.

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>웹후크 구독 새로 고침 - 사용자 ID 예제

다음 예제에서는 사용자 ID를 사용하여 구독을 새로 고칩니다.

*function.json* 파일은 타이머 트리거를 정의하고 함수 코드에 대한 구독 입력 바인딩을 지연합니다.

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 각 사용자의 ID를 사용하여 코드에서 구독을 새로 고치고, 출력 바인딩을 만듭니다.

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

[HTTP 트리거]: functions-bindings-http-webhook.md
[Microsoft Graph에서 웹후크 작업]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
