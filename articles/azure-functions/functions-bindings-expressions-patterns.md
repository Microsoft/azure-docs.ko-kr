---
title: Azure Functions 바인딩 식 및 패턴
description: 일반적인 패턴을 기반으로 하는 다른 Azure Functions 바인딩 식을 만드는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/20/2019
ms.author: v-junlch
ms.openlocfilehash: 0c1dbbae5e4be965f195b5ea4fc88b1bc5fb4f87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437874"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions 바인딩 식 패턴

가장 강력한 기능 중 하나 [트리거 및 바인딩](./functions-triggers-bindings.md) 됩니다 *바인딩 식*합니다. *function.json* 파일에서 그리고 함수 매개 변수 및 코드에서 다양한 원본의 값을 확인하는 식을 사용할 수 있습니다.

대부분의 식은 중괄호로 래핑하여 식별됩니다. 예를 들어 큐 트리거 함수에서 `{queueTrigger}`는 큐 메시지 텍스트를 확인합니다. blob 출력 바인딩에 대한 `path` 속성이 `container/{queueTrigger}`이고 함수가 큐 메시지 `HelloWorld`에 의해 트리거되는 경우 `HelloWorld`라는 blob이 만들어집니다.

바인딩 식의 형식

* [앱 설정](#binding-expressions---app-settings)
* [트리거 파일 이름](#trigger-file-name)
* [트리거 메타데이터](#trigger-metadata)
* [JSON 페이로드](#json-payloads)
* [새 GUID](#create-guids)
* [현재 날짜 및 시간](#current-time)

## <a name="binding-expressions---app-settings"></a>바인딩 식 - 앱 설정

비밀과 연결 문자열은 구성 파일이 아닌 앱 설정을 사용하여 관리하는 것이 가장 좋습니다. 그럴 경우 이러한 비밀에 대한 액세스가 제한되고 *function.json*과 같은 파일을 공용 원본 제어 리포지토리에 안전하게 저장할 수 있습니다.

환경을 기준으로 구성을 변경하려는 경우에도 앱 설정이 유용합니다. 예를 들어 테스트 환경에서 다른 큐 또는 Blob Storage 컨테이너를 모니터링할 수 있습니다.

앱 설정 바인딩 식은 다른 바인딩 식과는 다르게 식별됩니다. 중괄호 대신 백분율 기호로 래핑됩니다. 예를 들어 blob 출력 바인딩 경로가 `%Environment%/newblob.txt`이고 `Environment` 앱 설정 값이 `Development`인 경우 blob은 `Development` 컨테이너에 생성됩니다.

함수를 로컬로 실행 중인 경우 앱 설정 값은 *local.settings.json* 파일에서 가져옵니다.

트리거 및 바인딩의 `connection` 속성은 특수한 경우이며, 백분율 기호 없이 앱 설정으로 값을 자동 확인합니다. 

다음 예제는 `%input-queue-name%` 앱 설정을 사용하여 트리거할 큐를 정의하는 Azure Queue Storage 트리거입니다.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

클래스 라이브러리에서 동일한 방법을 사용할 수 있습니다.

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>트리거 파일 이름

Blob 트리거에 대한 `path`는 다른 바인딩 및 함수 코드에서 blob 트리거의 이름을 참조할 수 있도록 하는 패턴일 수 있습니다. 또한 패턴은 함수 호출을 트리거할 수 있는 blob을 지정하는 필터링 조건을 포함할 수 있습니다.

예를 들어, 다음 Blob 트리거 바인딩에서 `path` 패턴은 `sample-images/{filename}`으로, `filename`이라는 바인딩 식을 만듭니다.

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

그런 다음, 식 `filename`은 출력 바인딩에서 생성되는 blob의 이름을 지정하는 데 사용될 수 있습니다.

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

함수 코드는 매개 변수 이름으로 `filename`을 사용하여 이 동일한 값에 액세스할 수 있습니다.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

바인딩 식 및 패턴을 사용하는 동일한 기능은 클래스 라이브러리의 특성에 적용됩니다. 다음 예제에서 특성 생성자 매개 변수는 앞의 *function.json* 예제와 동일한 `path` 값입니다. 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

또한 확장과 같은 파일 이름 부분에 대한 식을 만들 수 있습니다. Blob 경로 문자열에서 식 및 패턴을 사용하는 방법에 대한 자세한 내용은 [저장소 blob 바인딩 참조](functions-bindings-storage-blob.md)를 확인하세요.

## <a name="trigger-metadata"></a>트리거 메타데이터

트리거가 제공한 데이터 페이로드(예: 함수를 트리거한 큐 메시지의 콘텐츠) 이외에 많은 트리거가 추가 메타데이터 값을 제공합니다. 이러한 값은 C# 및 F#에서 입력 매개 변수로 사용하거나 JavaScript에서 `context.bindings` 개체의 속성으로 사용할 수 있습니다. 

예를 들어 Azure Queue 저장소 트리거는 다음 속성을 지원합니다.

* QueueTrigger - 유효한 문자열인 경우 트리거 메시지 내용
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

이러한 메타데이터 값은 *function.json* 파일 속성에서 액세스할 수 있습니다. 예를 들어 큐 트리거를 사용하고 큐 메시지는 읽으려는 Blob의 이름을 포함한다고 가정합니다. *function.json* 파일에서 다음 예제와 같이 Blob `path` 속성에서 `queueTrigger` 메타데이터 속성을 사용할 수 있습니다.

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

각 트리거의 메타데이터 속성은 해당 참조 문서에서 자세히 설명되어 있습니다. 예를 들어 [큐 트리거 메타데이터](functions-bindings-storage-queue.md#trigger---message-metadata)를 참조하세요. 설명서는 Portal에서 **통합** 탭의 바인딩 구성 영역 아래 **설명서** 섹션에서도 참조할 수 있습니다.  

## <a name="json-payloads"></a>JSON 페이로드

트리거 페이로드가 JSON인 경우 같은 함수 및 함수 코드의 다른 바인딩에 대한 구성에서 해당 속성을 참조할 수 있습니다.

다음 예제에 JSON에서 blob 이름을 수신하는 웹후크 함수에 대한 *function.json* 파일이 나와 있습니다(`{"BlobName":"HelloWorld.txt"}`). Blob 입력 바인딩은 blob을 읽고, HTTP 출력 바인딩은 HTTP 응답에서 blob 콘텐츠를 반환합니다. Blob 입력 바인딩은 `BlobName` 속성(`"path": "strings/{BlobName}"`)을 직접 참조하여 blob 이름을 가져옵니다.

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

이렇게 하려면 C# 및 F#에서 다음 예제와 같이 deserialize할 필드를 정의하는 클래스가 필요합니다.

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JavaScript에서 JSON deserialization은 자동으로 실행됩니다.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>점 표기법

JSON 페이로드의 속성 중 일부가 속성을 가진 개체인 경우 점 표기법을 사용하여 직접 참조할 수 있습니다. 예를 들어 JSON이 다음과 같다고 가정합니다.

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

직접 `FileName`을 `BlobName.FileName`으로 참조할 수 있습니다. 이 JSON 형식을 사용할 때 이전 예제에서의 `path` 속성은 다음과 같습니다.

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

C#에서 다음과 같은 두 개의 클래스가 필요합니다.

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>GUID 만들기

`{rand-guid}` 바인딩 식은 GUID를 만듭니다. `function.json` 파일의 다음 blob 경로는 *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*와 같은 이름의 blob을 만듭니다.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>현재 시간

바인딩 식 `DateTime`은 `DateTime.UtcNow`로 확인됩니다. `function.json` 파일의 다음 blob 경로는 *2018-02-16T17-59-55Z.txt*와 같은 이름의 blob을 만듭니다.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>런타임에 바인딩

C# 및 기타 .NET 언어에서는 *function.json* 및 특성의 바인딩과 달리 명령적 바인딩 패턴을 사용할 수 있습니다. 명령적 바인딩은 바인딩 매개 변수를 디자인 타임이 아닌 런타임에 계산해야 할 경우 유용합니다. 자세한 내용은 [C# 개발자 참조](functions-dotnet-class-library.md#binding-at-runtime) 또는 [C# 스크립트 개발자 참조](functions-reference-csharp.md#binding-at-runtime)를 확인하세요.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure 함수 반환 값을 사용 하 여](./functions-bindings-return-value.md)

<!-- Update_Description: link update -->