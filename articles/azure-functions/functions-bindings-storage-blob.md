---
title: Azure Functions의 Azure Blob Storage 바인딩
description: Azure Functions에서 Azure Blob Storage 트리거 및 바인딩을 사용하는 방법을 파악합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 593d596881d0ec53b0f18e8af5a71b55c619f7a5
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922111"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Functions의 Azure Blob Storage 바인딩

이 문서에서는 Azure Functions에서 Azure Blob Storage 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 Blob에 대한 트리거, 입력 및 출력 바인딩을 지원합니다. 문서에는 각 바인딩에 대한 섹션이 포함됩니다.

* [Blob 트리거](#trigger)
* [Blob 입력 바인딩](#input)
* [Blob 출력 바인딩](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> BLOB 전용 스토리지 계정 및 높은 확장성을 위해 또는 대기 시간을 줄이기 위해 Blob Storage 트리거 대신 Event Grid 트리거를 사용합니다. 자세한 내용은 [트리거](#trigger) 섹션을 참조하세요.

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Blob Storage 바인딩은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>패키지-함수 2.x 이상

Blob 스토리지 바인딩은 [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 패키지 버전 3.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>트리거

Blob Storage 트리거는 신규 또는 업데이트된 Blob를 검색할 때 함수를 시작합니다. Blob 내용은 함수의 입력으로 제공됩니다.

[Event Grid 트리거](functions-bindings-event-grid.md)는 [BLOB 이벤트](../storage/blobs/storage-blob-event-overview.md)에 대해 기본 지원하며 신규 또는 업데이트된 BLOB을 검색할 때 함수를 시작하는 데 사용할 수 있습니다. 예를 들어 [Event Grid를 사용하여 이미지 크기 조정](../event-grid/resize-images-on-storage-blob-upload-event.md) 자습서를 참조합니다.

다음과 같은 시나리오에 대해 Blob Storage 트리거 대신 Event Grid를 사용합니다.

* Blob Storage 계정
* 높은 확장성
* 대기 시간 최소화

### <a name="blob-storage-accounts"></a>Blob Storage 계정

[Blob Storage 계정](../storage/common/storage-account-overview.md#types-of-storage-accounts)은 BLOB 입력 및 바인딩 출력을 지원하지만 Blob 트리거는 지원하지 않습니다. Blob Storage 트리거에는 범용 스토리지 계정이 필요합니다.

### <a name="high-scale"></a>높은 확장성

높은 확장성은 100,000개 이상의 BLOB이 있는 컨테이너 또는 초당 100개 이상의 BLOB 업데이트가 있는 스토리지 계정으로 정의할 수 있습니다.

### <a name="latency-issues"></a>대기 시간 문제

함수 앱이 소비 계획에 있는 경우 함수 앱이 유휴 상태가 되면 새 Blob 처리에 하루 최대 10분이 걸릴 수 있습니다. 이 대기 시간을 방지하려면 Always On을 사용하도록 설정한 App Service 계획으로 전환하면 됩니다. Blob Storage 계정으로 [Event Grid 트리거](functions-bindings-event-grid.md)를 사용할 수도 있습니다. 예를 들어 [Event Grid 자습서](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)를 참조하세요. 

### <a name="queue-storage-trigger"></a>Queue Storage 트리거

Event Grid 외에 BLOB 처리를 위한 다른 방법은 Queue storage 트리거이지만 BLOB 이벤트에 대해 기본 지원은 되지 않습니다. Blob을 만들거나 업데이트할 때 큐 메시지를 만들어야 합니다. 완료했다고 가정하는 예제는 [이 문서의 뒷부분에 나오는 Blob 입력 바인딩 예제](#input---example)를 참조하세요.

## <a name="trigger---example"></a>트리거 - 예제

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

다음 예제에서는 `samples-workitems` 컨테이너에서 Blob을 추가하거나 업데이트할 때 로그를 기록하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

blob 트리거 경로 `samples-workitems/{name}`의 문자열 `{name}`은 함수 코드에서 사용할 수 있는 [바인딩 식](./functions-bindings-expressions-patterns.md)을 만들어 트리거 blob의 파일 이름에 액세스합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Blob 이름 패턴](#trigger---blob-name-patterns)을 참조하세요.

`BlobTrigger` 특성에 대한 자세한 내용은 [트리거 - 특성](#trigger---attributes)을 참조하세요.

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

다음 예제에서는 *함수. json* 파일의 blob 트리거 바인딩과 바인딩을 사용 하는 코드를 보여 줍니다. 함수는 `samples-workitems` [컨테이너](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)에서 blob을 추가 하거나 업데이트할 때 로그를 기록 합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 트리거 경로 `samples-workitems/{name}`의 문자열 `{name}`은 함수 코드에서 사용할 수 있는 [바인딩 식](./functions-bindings-expressions-patterns.md)을 만들어 트리거 blob의 파일 이름에 액세스합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Blob 이름 패턴](#trigger---blob-name-patterns)을 참조하세요.

*function.json* 파일 속성에 대한 자세한 내용은 이러한 속성을 설명하는 [구성](#trigger---configuration) 섹션을 참조하세요.

`Stream`에 바인딩되는 C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

`CloudBlockBlob`에 바인딩되는 C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 코드](functions-reference-node.md)의 Blob 트리거 바인딩을 보여줍니다. 함수는 `samples-workitems` 컨테이너에서 Blob을 추가하거나 업데이트할 때 로그를 씁니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 트리거 경로 `samples-workitems/{name}`의 문자열 `{name}`은 함수 코드에서 사용할 수 있는 [바인딩 식](./functions-bindings-expressions-patterns.md)을 만들어 트리거 blob의 파일 이름에 액세스합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Blob 이름 패턴](#trigger---blob-name-patterns)을 참조하세요.

*function.json* 파일 속성에 대한 자세한 내용은 이러한 속성을 설명하는 [구성](#trigger---configuration) 섹션을 참조하세요.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [Python 코드](functions-reference-python.md)의 Blob 트리거 바인딩을 보여 줍니다. 함수는 `samples-workitems` [컨테이너](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)에서 blob을 추가 하거나 업데이트할 때 로그를 기록 합니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 트리거 경로 `samples-workitems/{name}`의 문자열 `{name}`은 함수 코드에서 사용할 수 있는 [바인딩 식](./functions-bindings-expressions-patterns.md)을 만들어 트리거 blob의 파일 이름에 액세스합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Blob 이름 패턴](#trigger---blob-name-patterns)을 참조하세요.

*function.json* 파일 속성에 대한 자세한 내용은 이러한 속성을 설명하는 [구성](#trigger---configuration) 섹션을 참조하세요.

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [Java 코드](functions-reference-java.md)의 Blob 트리거 바인딩을 보여 줍니다. 함수는 `myblob` 컨테이너에서 Blob을 추가하거나 업데이트할 때 로그를 씁니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Java 코드는 다음과 같습니다.

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="trigger---attributes"></a>트리거 - 특성

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 다음 특성을 사용하여 Blob 트리거를 구성합니다.

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  특성의 생성자는 조사할 컨테이너 및 선택적으로 [Blob 이름 패턴](#trigger---blob-name-patterns)을 나타내는 경로 문자열을 사용합니다. 예를 들면 다음과 같습니다.

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  전체 예제는 [트리거 예](#trigger---example)를 참조 하세요.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  사용할 스토리지 계정을 지정하는 다른 방법을 제공합니다. 생성자는 스토리지 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 매개 변수, 메서드 또는 클래스 수준에서 특성을 적용할 수 있습니다. 다음 예제에서는 클래스 수준 및 메서드 수준을 보여줍니다.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

사용할 스토리지 계정은 다음과 같은 순서로 결정됩니다.

* `BlobTrigger` 특성의 `Connection` 속성
* `BlobTrigger` 특성과 동일한 매개 변수에 적용된 `StorageAccount` 특성
* 함수에 적용된 `StorageAccount` 특성
* 클래스에 적용된 `StorageAccount` 특성
* 함수 앱의 기본 스토리지 계정("AzureWebJobsStorage" 앱 설정)

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

스크립트에서 C# 특성을 지원 하지 않습니다.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원 하지 않습니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python에서 특성을 지원 하지 않습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobTrigger` 특성은 함수를 트리거한 blob에 대한 액세스 권한을 제공 하는 데 사용 됩니다. 자세한 내용은 [트리거 예](#trigger---example) 를 참조 하세요.

---

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일 및 `BlobTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | n/a | `blobTrigger`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | n/a | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. 예외는 [사용](#trigger---usage) 섹션에서 표시됩니다. |
|**name** | n/a | 함수 코드에서 Blob을 나타내는 변수의 이름입니다. |
|**path** | **BlobPath** |모니터링할 [컨테이너](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)입니다.  [Blob 이름 패턴](#trigger---blob-name-patterns)일 수 있습니다. |
|**연결** | **연결** | 이 바인딩에 사용할 스토리지 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`를 "MyStorage"로 설정 하는 경우 함수 런타임은 "MyStorage" 라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.<br><br>연결 문자열은 [Blob Storage 계정](../storage/common/storage-account-overview.md#types-of-storage-accounts)이 아닌 범용 스토리지 계정의 문자열이어야 합니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>트리거 - 사용

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

`<NAME>`에서 `context.bindings.<NAME>`를 사용 하 여 blob 데이터에 액세스 합니다. 여기서는 *json*에 정의 된 값과 일치 합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

[InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)으로 형식화 된 매개 변수를 통해 blob 데이터에 액세스 합니다. 자세한 내용은 [트리거 예](#trigger---example) 를 참조 하세요.

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobTrigger` 특성은 함수를 트리거한 blob에 대한 액세스 권한을 제공 하는 데 사용 됩니다. 자세한 내용은 [트리거 예](#trigger---example) 를 참조 하세요.

---

## <a name="trigger---blob-name-patterns"></a>트리거 - Blob 이름 패턴

*function.json*의 `path` 속성 또는 `BlobTrigger` 특성 생성자에서 Blob 이름 패턴을 지정할 수 있습니다. 이름 패턴은 [필터 또는 바인딩 식](./functions-bindings-expressions-patterns.md)일 수 있습니다. 다음 섹션에서는 예제를 제공합니다.

### <a name="get-file-name-and-extension"></a>파일 이름 및 확장명 가져오기

다음 예제에서는 Blob 파일 이름 및 확장명에 별도로 바인딩하는 방법을 보여줍니다.

```json
"path": "input/{blobname}.{blobextension}",
```

Blob이 *original-Blob1.txt*인 경우 함수 코드에 있는 `blobname` 및 `blobextension` 변수의 값은 *original-Blob1* 및 *txt*입니다.

### <a name="filter-on-blob-name"></a>Blob 이름에 대한 필터링

다음 예제는 "original-" 문자열로 시작하는 `input` 컨테이너에 있는 Blob에서만 트리거됩니다.

```json
"path": "input/original-{name}",
```

Blob 이름이 *original-Blob1.txt*인 경우 함수 코드에 있는 `name` 변수의 값은 `Blob1`입니다.

### <a name="filter-on-file-type"></a>파일 형식에 대한 필터링

다음 예제는 *.png* 파일에서만 트리거됩니다.

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>파일 이름에서 중괄호에 대한 필터링

파일 이름에서 중괄호를 찾으려면 2개의 중괄호를 사용하여 중괄호를 이스케이프합니다. 다음 예제는 중괄호가 이름에 포함된 Blob에 대해서만 필터링됩니다.

```json
"path": "images/{{20140101}}-{name}",
```

Blob의 이름이 *{20140101}-soundfile.mp3*인 경우 함수 코드에서 `name` 변수 값은 *soundfile.mp3*입니다.

## <a name="trigger---metadata"></a>트리거 - 메타데이터

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python에서는 메타 데이터를 사용할 수 없습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

메타 데이터는 Java에서 사용할 수 없습니다.

---

## <a name="trigger---blob-receipts"></a>트리거 - Blob 수신 확인

Azure Functions 런타임은 동일한 새 Blob 또는 업데이트된 Blob에 대해 Blob 트리거 함수가 두 번 이상 호출되지 않도록 합니다. 지정된 Blob 버전이 처리되었는지 확인하려면 *Blob 수신 확인*을 유지 관리합니다.

Azure Functions는 사용자 함수 앱에서 사용하는(`AzureWebJobsStorage` 앱 설정에서 지정됨) Azure Storage 계정의 *azure-webjobs-hosts*라는 컨테이너에 Blob 수신 확인을 저장합니다. Blob 수신 확인에는 다음 정보가 포함됩니다.

* 트리거된 함수(" *&lt;함수 앱 이름>* .Functions. *&lt;함수 이름>* ", 예: "MyFunctionApp.Functions.CopyBlob")
* 컨테이너 이름
* Blob 유형("BlockBlob" 또는 "PageBlob")
* Blob 이름
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

Blob을 강제로 처리하려면 *azure-webjobs-hosts* 컨테이너에서 해당 Blob에 대한 Blob 수신 확인을 수동으로 삭제하면 됩니다. 다시 처리는 즉시 발생 하지 않을 수 있지만 나중에 발생 하는 것이 보장 됩니다.

## <a name="trigger---poison-blobs"></a>트리거 - 포이즌 Blob

지정된 Blob에 대한 Blob 트리거 함수가 실패한 경우 Azure Functions는 기본적으로 총 5번 해당 함수를 다시 시도합니다.

5번 모두 실패한 경우 Azure Functions는 *webjobs-blobtrigger-poison*이라는 스토리지 큐에 메시지를 추가합니다. 포이즌 Blob에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(형식에서 *&lt;함수 앱 이름>* .Functions. *&lt;함수 이름>* )
* BlobType("BlockBlob" 또는 "PageBlob")
* ContainerName
* BlobName
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>트리거 - 동시성 및 메모리 사용량

Blob 트리거는 큐를 내부적으로 사용하므로 동시 함수 호출의 최대 수는 [host.json의 큐 구성](functions-host-json.md#queues)에 의해 제어됩니다. 기본 설정은 동시성을 24 호출로 제한합니다. 이 제한은 Blob 트리거를 사용하는 각 함수에 개별적으로 적용됩니다.

[소비 계획은](functions-scale.md#how-the-consumption-and-premium-plans-work) 하나의 VM (가상 머신)에서 1.5 GB의 메모리로 함수 앱을 제한 합니다. 메모리는 각각 동시에 함수 인스턴스를 실행하여 함수 런타임 자체에서 사용됩니다. Blob 트리거된 함수에서 전체 Blob을 메모리로 로드하는 경우 Blob에 대해 해당 함수에서 사용되는 최대 메모리는 24 * 최대 Blob 크기입니다. 예를 들어 세 개의 Blob 트리거된 함수 및 기본 설정이 있는 함수 앱은 3*24 = 72 함수 호출의 최대 VM당 동시성을 갖습니다.

JavaScript 및 Java 함수는 전체 Blob을 메모리에 로드하고 C# 함수는 `string`, `Byte[]` 또는 POCO로 바인딩하는 경우 로드합니다.

## <a name="trigger---polling"></a>트리거 - 폴링

폴링은 로그 검사와 정기적인 컨테이너 검색 실행 간의 하이브리드 방식으로 작동 합니다. Blob은 간격 간에 사용 되는 연속 토큰을 사용 하 여 한 번에 1만 그룹으로 스캔 됩니다.

> [!WARNING]
> 또한 [스토리지 로그는 "최선을 다해" 생성됩니다](/rest/api/storageservices/About-Storage-Analytics-Logging). 하지만 모든 이벤트가 캡처되는 것은 아닙니다. 경우에 따라 로그가 누락될 수 있습니다.
> 
> 더 빠르거나 안정적인 Blob 처리가 필요한 경우 Blob을 만들 때 [큐 메시지](../storage/queues/storage-dotnet-how-to-use-queues.md)를 만드는 것이 좋습니다. 그런 다음 Blob 트리거 대신 [큐 트리거](functions-bindings-storage-queue.md)를 사용하여 Blob을 처리합니다. 다른 옵션은 Event Grid를 사용하는 겻입니다. [Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](../event-grid/resize-images-on-storage-blob-upload-event.md) 자습서를 참조하세요.
>

## <a name="input"></a>입력

Blob Storage 입력 바인딩을 사용하여 Blob을 읽습니다.

## <a name="input---example"></a>입력 - 예제

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

다음 예제는 하나의 큐 트리거와 입력 Blob 바인딩을 사용하는 [C# 함수](functions-dotnet-class-library.md)입니다. 큐 메시지에는 Blob의 이름이 포함되고 함수는 Blob의 크기를 기록합니다.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트(.csx)](functions-reference-csharp.md) 코드에서 Blob 입력 및 출력 바인딩을 보여줍니다. 함수는 텍스트 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy*입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 스크립트](functions-reference-node.md) 코드에서 Blob 입력 및 출력 바인딩을 보여 줍니다. 함수는 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy*입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [Python 코드](functions-reference-python.md)에서 Blob 입력 및 출력 바인딩을 보여 줍니다. 함수는 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy*입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

이 섹션에는 다음 예제가 포함되어 있습니다.

* [HTTP 트리거, 쿼리 문자열에서 Blob 이름 조회](#http-trigger-look-up-blob-name-from-query-string)
* [큐 트리거, 큐 메시지에서 Blob 이름 수신](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>쿼리 문자열에서 blob 이름을 조회 하는 HTTP 트리거

 다음 예제는 `HttpTrigger` 주석을 사용하여 Blob Storage 컨테이너에 있는 파일 이름을 포함하는 매개 변수를 수신하는 Java 함수를 보여 줍니다. 그런 다음, `BlobInput` 주석이 파일을 읽고 파일 내용을 함수에 `byte[]`로 전달합니다.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>큐 트리거, 큐 메시지에서 blob 이름 수신

 다음 예제는 `QueueTrigger` 주석을 사용하여 Blob Storage 컨테이너에 있는 파일 이름을 포함하는 메시지를 수신하는 Java 함수를 보여 줍니다. 그런 다음, `BlobInput` 주석이 파일을 읽고 파일 내용을 함수에 `byte[]`로 전달합니다.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Blob에서 제공되는 매개 변수에 대한 `@BlobInput` 주석을 사용합니다.  `Optional<T>`을 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

---

## <a name="input---attributes"></a>입력 - 특성

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)를 사용합니다.

특성의 생성자는 다음 예제에 표시된 대로 Blob에 대한 경로 및 읽기 또는 쓰기를 나타내는 `FileAccess` 매개 변수를 사용합니다.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 스토리지 계정을 지정합니다. 자세한 내용은 [트리거 - 특성](#trigger---attributes)을 참조하세요.

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

스크립트에서 C# 특성을 지원 하지 않습니다.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원 하지 않습니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python에서 특성을 지원 하지 않습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobInput` 특성은 함수를 트리거한 blob에 대한 액세스를 제공 합니다. 특성에 바이트 배열을 사용 하는 경우 `dataType`를 `binary`로 설정 합니다. 자세한 내용은 [입력 예](#input---example) 를 참조 하세요.

---

## <a name="input---configuration"></a>입력 - 구성

다음 표에서는 *function.json* 파일 및 `Blob` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | n/a | `blob`로 설정해야 합니다. |
|**direction** | n/a | `in`로 설정해야 합니다. 예외는 [사용](#input---usage) 섹션에서 표시됩니다. |
|**name** | n/a | 함수 코드에서 Blob을 나타내는 변수의 이름입니다.|
|**path** |**BlobPath** | Blob에 대한 경로입니다. |
|**연결** |**연결**| 이 바인딩에 사용할 [스토리지 연결 문자열](../storage/common/storage-configure-connection-string.md)을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`를 "MyStorage"로 설정 하는 경우 함수 런타임은 "MyStorage" 라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.<br><br>연결 문자열은 [Blob 전용 스토리지 계정](../storage/common/storage-account-overview.md#types-of-storage-accounts)이 아닌 범용 스토리지 계정의 문자열이어야 합니다.|
|n/a | **Access** | 읽기 또는 쓰기를 나타냅니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>입력 - 사용

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

`<NAME>`에서 `context.bindings.<NAME>`를 사용 하 여 blob 데이터에 액세스 합니다. 여기서는 *json*에 정의 된 값과 일치 합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

[InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)으로 형식화 된 매개 변수를 통해 blob 데이터에 액세스 합니다. 자세한 내용은 [입력 예](#input---example) 를 참조 하세요.

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobInput` 특성은 함수를 트리거한 blob에 대한 액세스를 제공 합니다. 특성에 바이트 배열을 사용 하는 경우 `dataType`를 `binary`로 설정 합니다. 자세한 내용은 [입력 예](#input---example) 를 참조 하세요.

---

## <a name="output"></a>출력

Blob Storage 출력 바인딩을 사용하여 Blob을 작성합니다.

## <a name="output---example"></a>출력 - 예제

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

다음 예제는 하나의 Blob 트리거와 두 개의 출력 Blob 바인딩을 사용하는 [C# 함수](functions-dotnet-class-library.md)입니다. *샘플 이미지* 컨테이너에서 이미지 Blob을 만들어서 함수를 트리거합니다. 그러면 이미지 Blob의 소량 및 중간 크기 복사본을 만듭니다.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트(.csx)](functions-reference-csharp.md) 코드에서 Blob 입력 및 출력 바인딩을 보여줍니다. 함수는 텍스트 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy*입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 스크립트](functions-reference-node.md) 코드에서 Blob 입력 및 출력 바인딩을 보여 줍니다. 함수는 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy*입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [Python 코드](functions-reference-python.md)에서 Blob 입력 및 출력 바인딩을 보여 줍니다. 함수는 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy*입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

이 섹션에는 다음 예제가 포함되어 있습니다.

* [HTTP 트리거, OutputBinding 사용](#http-trigger-using-outputbinding-java)
* [큐 트리거, 함수 반환 값 사용](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP 트리거, OutputBinding 사용(Java)

 다음 예제는 `HttpTrigger` 주석을 사용하여 Blob Storage 컨테이너에 있는 파일 이름을 포함하는 매개 변수를 수신하는 Java 함수를 보여 줍니다. 그런 다음, `BlobInput` 주석이 파일을 읽고 파일 내용을 함수에 `byte[]`로 전달합니다. `BlobOutput` 주석은 `OutputBinding outputItem`에 바인딩되고, 이는 입력 Blob의 콘텐츠를 구성된 스토리지 컨테이너에 쓰기 위해 함수에서 사용됩니다.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>큐 트리거, 함수 반환 값 사용(Java)

 다음 예제는 `QueueTrigger` 주석을 사용하여 Blob Storage 컨테이너에 있는 파일 이름을 포함하는 메시지를 수신하는 Java 함수를 보여 줍니다. 그런 다음, `BlobInput` 주석이 파일을 읽고 파일 내용을 함수에 `byte[]`로 전달합니다. `BlobOutput` 주석은 함수 반환 값에 바인딩되고, 이는 입력 Blob의 콘텐츠를 구성된 스토리지 컨테이너에 쓰기 위해 런타임에서 사용됩니다.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 [Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Blob Storage의 개체에 기록될 함수 매개 변수에 대한 `@BlobOutput` 주석을 사용합니다.  매개 변수 형식은 `OutputBinding<T>`이어야 합니다. 여기서 T는 원시 Java 형식 또는 POJO입니다.

---

## <a name="output---attributes"></a>출력 - 특성

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)를 사용합니다.

특성의 생성자는 다음 예제에 표시된 대로 Blob에 대한 경로 및 읽기 또는 쓰기를 나타내는 `FileAccess` 매개 변수를 사용합니다.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

스크립트에서 C# 특성을 지원 하지 않습니다.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원 하지 않습니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python에서 특성을 지원 하지 않습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobOutput` 특성은 함수를 트리거한 blob에 대한 액세스를 제공 합니다. 특성에 바이트 배열을 사용 하는 경우 `dataType`를 `binary`로 설정 합니다. 자세한 내용은 [출력 예제](#output---example) 를 참조 하십시오.

---

전체 예제는 [출력 예제](#output---example)를 참조 하세요.

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 스토리지 계정을 지정합니다. 자세한 내용은 [트리거 - 특성](#trigger---attributes)을 참조하세요.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일 및 `Blob` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | n/a | `blob`로 설정해야 합니다. |
|**direction** | n/a | 출력 바인딩에 대해 `out`로 설정해야 합니다. 예외는 [사용](#output---usage) 섹션에서 표시됩니다. |
|**name** | n/a | 함수 코드에서 Blob을 나타내는 변수의 이름입니다.  `$return`으로 설정하여 함수 반환 값을 참조합니다.|
|**path** |**BlobPath** | Blob 컨테이너에 대한 경로입니다. |
|**연결** |**연결**| 이 바인딩에 사용할 스토리지 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`를 "MyStorage"로 설정 하는 경우 함수 런타임은 "MyStorage" 라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.<br><br>연결 문자열은 [Blob 전용 스토리지 계정](../storage/common/storage-account-overview.md#types-of-storage-accounts)이 아닌 범용 스토리지 계정의 문자열이어야 합니다.|
|n/a | **Access** | 읽기 또는 쓰기를 나타냅니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>출력 - 사용

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 `context.bindings.<name from function.json>`을 사용하여 Blob 데이터에 액세스합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

함수 매개 변수를 다음 형식으로 선언 하 여 blob 저장소에 쓸 수 있습니다.

* `func.Out(str)` 문자열
* `func.Out(func.InputStream)`로 스트림

자세한 내용은 [출력 예제](#output---example) 를 참조 하십시오.

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobOutput` 특성은 함수를 트리거한 blob에 대한 액세스를 제공 합니다. 특성에 바이트 배열을 사용 하는 경우 `dataType`를 `binary`로 설정 합니다. 자세한 내용은 [출력 예제](#output---example) 를 참조 하십시오.

---

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 |  참조 |
|---|---|
| Blob | [Blob 오류 코드](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, 테이블, 큐 |  [스토리지 오류 코드](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, 테이블, 큐 |  [문제 해결](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>다음 단계

* [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
