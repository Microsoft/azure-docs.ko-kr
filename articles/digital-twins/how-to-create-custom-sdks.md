---
title: AutoRest를 사용 하 여 Azure Digital Twins에 대 한 사용자 지정 Sdk 만들기
titleSuffix: Azure Digital Twins
description: 'C # 이외의 언어로 Azure Digital Twins를 사용 하려면 사용자 지정 Sdk를 생성 하는 방법을 참조 하세요.'
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 3cf14ce3e8ef9b1d783191fe6c01c5e311d57786
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855945"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>AutoRest를 사용 하 여 Azure Digital Twins에 대 한 사용자 지정 Sdk 만들기

현재 Azure Digital Twins Api와 상호 작용 하기 위해 게시 된 데이터 평면 SDK만 .NET (c #) 용입니다. .NET SDK 및 일반적인 Api에 대 한 자세한 내용은 [*방법: Azure Digital Twins api 및 Sdk 사용*](how-to-use-apis-sdks.md)을 참조 하세요. 다른 언어로 작업 하는 경우이 문서에서는 AutoRest를 사용 하 여 원하는 언어로 고유한 데이터 평면 SDK를 생성 하는 방법을 보여 줍니다.

>[!NOTE]
> 원하는 경우 AutoRest를 사용 하 여 제어 평면 SDK를 생성할 수도 있습니다. 이렇게 하려면 데이터 평면 1 대신 [제어 평면 Swagger (OpenAPI) 파일](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview) 을 사용 하 여이 문서의 단계를 완료 합니다.

## <a name="set-up-your-machine"></a>컴퓨터 설정

SDK를 생성 하려면 다음이 필요 합니다.
* [AutoRest](https://github.com/Azure/autorest), 버전 2.0.4413 (현재 버전 3은 지원 되지 않음)
* AutoRest에 대 한 필수 구성 요소 [Node.js](https://nodejs.org)
* *에서digitaltwins.js*되는 Azure Digital twins [데이터 평면 Swagger (openapi) 파일과](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins/preview/2020-05-31-preview) 함께 제공 되는 예제 폴더입니다. Swagger 파일 및 해당 폴더의 예제를 로컬 컴퓨터에 다운로드 합니다.

위의 목록에 있는 모든 항목이 컴퓨터에 장착 되 면 AutoRest를 사용 하 여 SDK를 만들 준비가 된 것입니다.

## <a name="create-the-sdk-with-autorest"></a>AutoRest를 사용 하 여 SDK 만들기 

Node.js 설치 되어 있는 경우이 명령을 실행 하 여 올바른 버전의 AutoRest가 설치 되어 있는지 확인할 수 있습니다.
```cmd/sh
npm install -g autorest@2.0.4413
```

Azure Digital Twins Swagger 파일에 대해 AutoRest를 실행 하려면 다음 단계를 수행 합니다.
1. Azure Digital Twins Swagger 파일 및 함께 제공 되는 예제 폴더를 작업 디렉터리에 복사 합니다.
2. 명령 프롬프트 창을 사용 하 여 해당 작업 디렉터리로 전환 합니다.
3. 다음 명령을 사용 하 여 AutoRest를 실행 합니다. `<language>`자리 표시자를 원하는 언어 (,, 등)로 바꿉니다 `python` `java` `go` . [AUTOREST 추가 정보](https://github.com/Azure/autorest)에서 전체 옵션 목록을 찾을 수 있습니다.

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

결과적으로 작업 디렉터리에 *Adtapi* 라는 새 폴더가 표시 됩니다. 생성 된 SDK 파일에는 *Adtapi*네임 스페이스가 포함 됩니다. 이 문서의 나머지 사용 예에서는이 네임 스페이스를 계속 사용 합니다.

AutoRest는 다양 한 언어 코드 생성기를 지원 합니다.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Visual Studio 프로젝트에 SDK 추가

AutoRest에서 생성 된 파일을 .NET 솔루션에 직접 포함할 수 있습니다. 그러나 여러 개별 프로젝트 (클라이언트 앱, Azure Functions 앱 등)에 Azure Digital Twins SDK를 포함 하려는 경우가 있습니다. 따라서 생성 된 파일에서 별도의 프로젝트 (.NET 클래스 라이브러리)를 빌드하는 것이 유용할 수 있습니다. 그런 다음이 클래스 라이브러리 프로젝트를 여러 솔루션에 프로젝트 참조로 포함할 수 있습니다.

이 섹션에서는 SDK를 클래스 라이브러리로 빌드하는 방법에 대 한 지침을 제공 합니다 .이 라이브러리는 자체 프로젝트 이며 다른 프로젝트에 포함 될 수 있습니다. 이러한 단계는 **Visual Studio** 를 사용 합니다. [여기](https://visualstudio.microsoft.com/downloads/)에서 최신 버전을 설치할 수 있습니다.

단계는 다음과 같습니다.

1. 클래스 라이브러리에 대 한 새 Visual Studio 솔루션 만들기
2. *Adtapi* 를 프로젝트 이름으로 사용
3. 솔루션 탐색기에서 생성 된 솔루션의 *Adtapi* 프로젝트를 마우스 오른쪽 단추로 선택 하 고 *기존 항목 > 추가* ...를 선택 합니다.
4. SDK를 생성 한 폴더를 찾고 루트 수준에서 파일을 선택 합니다.
5. "확인"을 누릅니다.
6. 프로젝트에 폴더를 추가 합니다 (솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 선택 하 고 *> 새 폴더 추가*선택).
7. 폴더 이름을 *모델* 으로
8. 솔루션 탐색기에서 *모델* 폴더를 마우스 오른쪽 단추로 선택 하 고 *기존 항목 > 추가* ...를 선택 합니다.
9. 생성 된 SDK의 *모델* 폴더에서 파일을 선택 하 고 "확인"을 누릅니다.

SDK를 성공적으로 빌드하려면 프로젝트에 다음 참조가 필요 합니다.
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

이러한 도구를 추가 하려면 *> Nuget 패키지 관리자 > 솔루션에 대 한 Nuget 패키지 관리*...를 엽니다.

1. 패널에서 *찾아보기* 탭이 선택 되어 있는지 확인 합니다.
2. *Microsoft Rest를 검색 합니다.*
3. `ClientRuntime`및 `ClientRuntime.Azure` 패키지를 선택 하 고 솔루션에 추가 합니다.

이제 프로젝트를 빌드하고 작성 한 모든 Azure 디지털 쌍 응용 프로그램에 프로젝트 참조로 포함할 수 있습니다.

## <a name="general-guidelines-for-generated-sdks"></a>생성 된 Sdk에 대 한 일반 지침

이 섹션에는 생성 된 SDK 사용에 대 한 일반 정보 및 지침이 포함 되어 있습니다.

### <a name="synchronous-and-asynchronous-calls"></a>동기 및 비동기 호출

모든 SDK 함수는 동기 및 비동기 버전으로 제공 됩니다.

### <a name="typed-and-untyped-data"></a>형식화 된 데이터 및 형식화 되지 않은 데이터

REST API 호출은 일반적으로 강력한 형식의 개체를 반환 합니다. 그러나 Azure Digital Twins는 사용자가 쌍에 대해 고유한 사용자 지정 형식을 정의할 수 있으므로 여러 Azure 디지털 쌍 호출에 대해 정적 반환 데이터를 미리 정의할 수 없습니다. 대신, Api는 해당 하는 경우 강력한 형식의 래퍼 형식을 반환 하 고 사용자 지정 쌍 데이터는 Json.NET 개체에 있습니다 (데이터 형식이 "object"가 API 서명에 표시 되는 경우에 사용 됨). 코드에서 이러한 개체를 적절 하 게 캐스팅할 수 있습니다.

### <a name="error-handling"></a>오류 처리

SDK에서 오류가 발생할 때마다 (404 등의 HTTP 오류 포함) SDK에서 예외가 throw 됩니다. 따라서 try/catch 블록 내에서 모든 API 호출을 캡슐화 하는 것이 중요 합니다.

다음은 쌍을 추가 하 고이 프로세스에서 오류를 catch 하는 코드 조각입니다.

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>페이징

AutoRest는 SDK에 대해 두 가지 유형의 페이징 패턴을 생성 합니다.
* 쿼리 API를 제외한 모든 Api에 대 한 하나
* 쿼리 API에 대 한 하나

쿼리가 아닌 페이징 패턴에는 각 호출에 대 한 두 가지 버전이 있습니다.
* 초기 호출을 수행할 버전 (예: `DigitalTwins.ListEdges()` )
* 다음 페이지를 가져올 버전입니다. 이러한 호출의 접미사는 "Next" (예: `DigitalTwins.ListEdgesNext()` )입니다.

다음은 Azure Digital Twins에서 나가는 관계의 페이징된 목록을 검색 하는 방법을 보여 주는 코드 조각입니다.
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

두 번째 패턴은 쿼리 API에 대해서만 생성 됩니다. 를 `continuationToken` 명시적으로 사용 합니다.

이 패턴의 예는 다음과 같습니다.

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>다음 단계

SDK를 사용할 수 있는 클라이언트 앱을 만드는 단계를 안내 합니다.
* [*자습서: 클라이언트 앱 코딩*](tutorial-code.md)