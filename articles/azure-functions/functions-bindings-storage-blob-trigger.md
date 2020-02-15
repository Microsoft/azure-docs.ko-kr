---
title: Azure Functions에 대 한 Azure Blob storage 트리거
description: Azure Blob storage 데이터 변경으로 Azure 함수를 실행 하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 0cdff3ac6eb2faed0c0b6b8796fdb3b6b0411018
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202087"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Azure Functions에 대 한 Azure Blob storage 트리거

Blob Storage 트리거는 신규 또는 업데이트된 Blob를 검색할 때 함수를 시작합니다. Blob 콘텐츠는 [함수에 대 한 입력](./functions-bindings-storage-blob-input.md)으로 제공 됩니다.

Azure Blob storage 트리거에는 범용 저장소 계정이 필요 합니다. Blob 전용 계정을 사용 하거나 응용 프로그램에 특수 한 요구 사항이 있는 경우이 트리거 사용에 대 한 대안을 검토 합니다.

설정 및 구성 세부 정보에 대 한 자세한 내용은 [개요](./functions-bindings-storage-blob.md)를 참조 하세요.

## <a name="alternatives"></a>대안

### <a name="event-grid-trigger"></a>Event Grid 트리거

또한 [Event Grid 트리거](functions-bindings-event-grid.md) 는 [blob 이벤트](../storage/blobs/storage-blob-event-overview.md)를 기본적으로 지원 합니다. 다음과 같은 시나리오에 대해 Blob Storage 트리거 대신 Event Grid를 사용합니다.

- **Blob 전용 저장소 계정**: blob [전용 저장소 계정은](../storage/common/storage-account-overview.md#types-of-storage-accounts) blob 입력 및 출력 바인딩에 대해서는 지원 되지만 blob 트리거에는 지원 되지 않습니다.

- **높은 규모**: 높은 배율은 10만 개 이상의 blob이 포함 된 컨테이너 또는 초당 blob 업데이트가 100 개 이상인 저장소 계정으로 느슨하게 정의 될 수 있습니다.

- **대기 시간 최소화**: 함수 앱이 소비 계획에 있는 경우 함수 앱이 유휴 상태가 되는 경우 새 blob을 처리 하는 데 최대 10 분까지 지연 될 수 있습니다. 이 대기 시간을 방지하려면 Always On을 사용하도록 설정한 App Service 계획으로 전환하면 됩니다. Blob Storage 계정으로 [Event Grid 트리거](functions-bindings-event-grid.md)를 사용할 수도 있습니다. 예를 들어 [Event Grid 자습서](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)를 참조하세요.

Event Grid 예제의 [이미지 크기 조정 Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) 자습서를 참조 하세요.

### <a name="queue-storage-trigger"></a>Queue Storage 트리거

Blob을 처리 하는 또 다른 방법은 만들어지거나 수정 되는 blob에 해당 하는 큐 메시지를 작성 한 다음 [queue storage 트리거](./functions-bindings-storage-queue.md) 를 사용 하 여 처리를 시작 하는 것입니다.

## <a name="example"></a>예제

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

다음 예제에서는 [ 컨테이너에서 Blob을 추가하거나 업데이트할 때 로그를 기록하는 ](functions-dotnet-class-library.md)C# 함수`samples-workitems`를 보여줍니다.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

blob 트리거 경로 `{name}`의 문자열 `samples-workitems/{name}`은 함수 코드에서 사용할 수 있는 [바인딩 식](./functions-bindings-expressions-patterns.md)을 만들어 트리거 blob의 파일 이름에 액세스합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Blob 이름 패턴](#blob-name-patterns)을 참조하세요.

`BlobTrigger` 특성에 대 한 자세한 내용은 [특성 및 주석](#attributes-and-annotations)을 참조 하세요.

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

blob 트리거 경로 `{name}`의 문자열 `samples-workitems/{name}`은 함수 코드에서 사용할 수 있는 [바인딩 식](./functions-bindings-expressions-patterns.md)을 만들어 트리거 blob의 파일 이름에 액세스합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Blob 이름 패턴](#blob-name-patterns)을 참조하세요.

*function.json* 파일 속성에 대한 자세한 내용은 이러한 속성을 설명하는 [구성](#configuration) 섹션을 참조하세요.

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

blob 트리거 경로 `{name}`의 문자열 `samples-workitems/{name}`은 함수 코드에서 사용할 수 있는 [바인딩 식](./functions-bindings-expressions-patterns.md)을 만들어 트리거 blob의 파일 이름에 액세스합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Blob 이름 패턴](#blob-name-patterns)을 참조하세요.

*function.json* 파일 속성에 대한 자세한 내용은 이러한 속성을 설명하는 [구성](#configuration) 섹션을 참조하세요.

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

blob 트리거 경로 `{name}`의 문자열 `samples-workitems/{name}`은 함수 코드에서 사용할 수 있는 [바인딩 식](./functions-bindings-expressions-patterns.md)을 만들어 트리거 blob의 파일 이름에 액세스합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Blob 이름 패턴](#blob-name-patterns)을 참조하세요.

*function.json* 파일 속성에 대한 자세한 내용은 이러한 속성을 설명하는 [구성](#configuration) 섹션을 참조하세요.

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

이 함수는 `myblob` 컨테이너에서 blob을 추가 하거나 업데이트할 때 로그를 기록 합니다.

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

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 다음 특성을 사용하여 Blob 트리거를 구성합니다.

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  특성의 생성자는 조사할 컨테이너 및 선택적으로 [Blob 이름 패턴](#blob-name-patterns)을 나타내는 경로 문자열을 사용합니다. 예를 들면 다음과 같습니다.

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

  전체 예제는 [트리거 예](#example)를 참조 하세요.

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
* `StorageAccount` 특성과 동일한 매개 변수에 적용된 `BlobTrigger` 특성
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

`@BlobTrigger` 특성은 함수를 트리거한 blob에 대 한 액세스 권한을 제공 하는 데 사용 됩니다. 자세한 내용은 [트리거 예](#example) 를 참조 하세요.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `BlobTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | `blobTrigger`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. 예외는 [사용](#usage) 섹션에서 표시됩니다. |
|**name** | 해당 없음 | 함수 코드에서 Blob을 나타내는 변수의 이름입니다. |
|**path** | **BlobPath** |모니터링할 [컨테이너](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)입니다.  [Blob 이름 패턴](#blob-name-patterns)일 수 있습니다. |
|**연결** | **연결** | 이 바인딩에 사용할 스토리지 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyStorage"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.<br><br>연결 문자열은 [Blob Storage 계정](../storage/common/storage-account-overview.md#types-of-storage-accounts)이 아닌 범용 스토리지 계정의 문자열이어야 합니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

`<NAME>`에서 `context.bindings.<NAME>`를 사용 하 여 blob 데이터에 액세스 합니다. 여기서는 *json*에 정의 된 값과 일치 합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

[InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)으로 형식화 된 매개 변수를 통해 blob 데이터에 액세스 합니다. 자세한 내용은 [트리거 예](#example) 를 참조 하세요.

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobTrigger` 특성은 함수를 트리거한 blob에 대 한 액세스 권한을 제공 하는 데 사용 됩니다. 자세한 내용은 [트리거 예](#example) 를 참조 하세요.

---

## <a name="blob-name-patterns"></a>Blob 이름 패턴

`path`function.json*의*  속성 또는 `BlobTrigger` 특성 생성자에서 Blob 이름 패턴을 지정할 수 있습니다. 이름 패턴은 [필터 또는 바인딩 식](./functions-bindings-expressions-patterns.md)일 수 있습니다. 다음 섹션에서는 예제를 제공합니다.

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

## <a name="metadata"></a>메타데이터

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

## <a name="blob-receipts"></a>Blob 수신 확인

Azure Functions 런타임은 동일한 새 Blob 또는 업데이트된 Blob에 대해 Blob 트리거 함수가 두 번 이상 호출되지 않도록 합니다. 지정된 Blob 버전이 처리되었는지 확인하려면 *Blob 수신 확인*을 유지 관리합니다.

Azure Functions는 사용자 함수 앱에서 사용하는(*앱 설정에서 지정됨) Azure Storage 계정의*azure-webjobs-hosts`AzureWebJobsStorage`라는 컨테이너에 Blob 수신 확인을 저장합니다. Blob 수신 확인에는 다음 정보가 포함됩니다.

* 트리거된 함수(" *&lt;함수 앱 이름>* .Functions. *&lt;함수 이름>* ", 예: "MyFunctionApp.Functions.CopyBlob")
* 컨테이너 이름
* Blob 유형("BlockBlob" 또는 "PageBlob")
* Blob 이름
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

Blob을 강제로 처리하려면 *azure-webjobs-hosts* 컨테이너에서 해당 Blob에 대한 Blob 수신 확인을 수동으로 삭제하면 됩니다. 다시 처리는 즉시 발생 하지 않을 수 있지만 나중에 발생 하는 것이 보장 됩니다.

## <a name="poison-blobs"></a>포이즌 blob

지정된 Blob에 대한 Blob 트리거 함수가 실패한 경우 Azure Functions는 기본적으로 총 5번 해당 함수를 다시 시도합니다.

5번 모두 실패한 경우 Azure Functions는 *webjobs-blobtrigger-poison*이라는 스토리지 큐에 메시지를 추가합니다. 포이즌 Blob에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(형식에서 *&lt;함수 앱 이름>* .Functions. *&lt;함수 이름>* )
* BlobType("BlockBlob" 또는 "PageBlob")
* ContainerName
* BlobName
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>동시성 및 메모리 사용량

Blob 트리거는 큐를 내부적으로 사용하므로 동시 함수 호출의 최대 수는 [host.json의 큐 구성](functions-host-json.md#queues)에 의해 제어됩니다. 기본 설정은 동시성을 24 호출로 제한합니다. 이 제한은 Blob 트리거를 사용하는 각 함수에 개별적으로 적용됩니다.

[소비 계획은](functions-scale.md#how-the-consumption-and-premium-plans-work) 하나의 VM (가상 머신)에서 1.5 GB의 메모리로 함수 앱을 제한 합니다. 메모리는 각각 동시에 함수 인스턴스를 실행하여 함수 런타임 자체에서 사용됩니다. Blob 트리거된 함수에서 전체 Blob을 메모리로 로드하는 경우 Blob에 대해 해당 함수에서 사용되는 최대 메모리는 24 * 최대 Blob 크기입니다. 예를 들어 세 개의 Blob 트리거된 함수 및 기본 설정이 있는 함수 앱은 3*24 = 72 함수 호출의 최대 VM당 동시성을 갖습니다.

JavaScript 및 Java 함수는 전체 Blob을 메모리에 로드하고 C# 함수는 `string`, `Byte[]` 또는 POCO로 바인딩하는 경우 로드합니다.

## <a name="polling"></a>폴링

폴링은 로그 검사와 정기적인 컨테이너 검색 실행 간의 하이브리드 방식으로 작동 합니다. Blob은 간격 간에 사용 되는 연속 토큰을 사용 하 여 한 번에 1만 그룹으로 스캔 됩니다.

> [!WARNING]
> 또한 [스토리지 로그는 "최선을 다해" 생성됩니다](/rest/api/storageservices/About-Storage-Analytics-Logging). 하지만 모든 이벤트가 캡처되는 것은 아닙니다. 경우에 따라 로그가 누락될 수 있습니다.
> 
> 더 빠르거나 안정적인 Blob 처리가 필요한 경우 Blob을 만들 때 [큐 메시지](../storage/queues/storage-dotnet-how-to-use-queues.md)를 만드는 것이 좋습니다. 그런 다음 Blob 트리거 대신 [큐 트리거](functions-bindings-storage-queue.md)를 사용하여 Blob을 처리합니다. 다른 옵션은 Event Grid를 사용하는 겻입니다. [Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](../event-grid/resize-images-on-storage-blob-upload-event.md) 자습서를 참조하세요.
>

## <a name="next-steps"></a>다음 단계

- [함수가 실행 될 때 blob 저장소 데이터 읽기](./functions-bindings-storage-blob-input.md)
- [함수에서 blob 저장소 데이터 쓰기](./functions-bindings-storage-blob-output.md)
