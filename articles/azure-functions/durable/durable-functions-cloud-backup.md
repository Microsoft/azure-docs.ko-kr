---
title: 지속성 함수의 팬아웃/팬인 시나리오 - Azure
description: Azure Functions의 지속성 함수 확장에서 팬아웃/팬인 시나리오를 구현하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 91128033696af6a56488db7991987f1e384b719e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027652"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>지속성 함수의 팬아웃/팬인 시나리오 - 클라우드 백업 예제

*팬아웃/팬인* 은 여러 함수를 동시에 실행한 다음 결과에 대해 일부 집계를 수행하는 패턴을 나타냅니다. 이 문서에서는 [지속성 함수](durable-functions-overview.md)를 사용하여 팬인/팬아웃 시나리오를 구현하는 샘플에 대해 설명합니다. 샘플은 앱의 사이트 콘텐츠 전부 또는 일부를 Azure Storage에 백업하는 지속성 함수입니다.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>시나리오 개요

이 샘플에서 함수는 지정한 디렉터리 아래의 모든 파일을 Blob Storage에 재귀적으로 업로드합니다. 또한 업로드된 총 바이트 수를 계산합니다.

모든 작업을 처리하는 단일 함수를 작성할 수 있습니다. 실행 시의 주요 문제는 **확장성** 입니다. 단일 함수 실행은 단일 가상 머신에서만 실행될 수 있으므로 처리량은 해당 단일 VM의 처리량으로 제한됩니다. 또 하나의 문제는 **안정성** 입니다. 중간에 오류가 있거나 전체 프로세스에 5분 넘게 걸리는 경우 부분적으로 완료된 상태에서 백업이 실패할 수 있습니다. 그러면 다시 시작해야 합니다.

더 강력한 방법은 다음 두 가지 일반 함수를 작성하는 것입니다. 하나는 파일을 열거하고 큐에 파일 이름을 추가하며, 다른 하나는 파일을 큐에서 읽고 Blob Storage에 업로드합니다. 이 접근 방식은 처리량과 안정성 면에서 더 효율적이지만 큐를 프로비전하고 관리해야 합니다. 더 중요한 것은 업로드된 총 바이트 수의 보고와 같이 더 많은 작업을 수행하려는 경우 **상태 관리** 및 **조정** 과 관련하여 상당한 복잡성이 도입된다는 것입니다.

지속성 함수 방법은 언급된 모든 이점을 매우 낮은 오버헤드로 제공합니다.

## <a name="the-functions"></a>함수

이 문서에서는 샘플 앱의 다음 함수에 대해 설명합니다.

* `E2_BackupSiteContent`: `E2_GetFileList`를 호출하여 백업할 파일 목록을 얻은 후 `E2_CopyFileToBlob`을 호출하여 각 파일을 백업하는 [오케스트레이터 함수](durable-functions-bindings.md#orchestration-trigger).
* `E2_GetFileList`: 디렉터리에 있는 파일의 목록을 반환하는 [작업 함수](durable-functions-bindings.md#activity-trigger).
* `E2_CopyFileToBlob`: 단일 파일을 Azure Blob Storage에 백업하는 작업 함수.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent 오케스트레이터 함수

이 오케스트레이터 함수는 기본적으로 다음을 수행합니다.

1. `rootDirectory` 값을 입력 매개 변수로 사용합니다.
2. `rootDirectory`에 있는 파일의 재귀 목록을 가져오는 함수를 호출합니다.
3. Azure Blob Storage에 각 파일을 업로드하기 위해 여러 개의 병렬 함수 호출을 만듭니다.
4. 모든 업로드가 완료될 때까지 기다립니다.
5. Azure Blob Storage에 업로드된 총 바이트 수를 반환합니다.

# <a name="c"></a>[C#](#tab/csharp)

다음은 오케스트레이터 함수를 구현하는 코드입니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

`await Task.WhenAll(tasks);` 줄에 유의하세요. `E2_CopyFileToBlob` 함수에 대한 모든 개별 호출은 대기하지 *않았으며* 병렬로 실행할 수 있습니다. 이 작업 배열을 `Task.WhenAll`에 전달하면 *모든 복사 작업이 완료될 때까지* 완료되지 않는 작업을 다시 가져옵니다. .NET의 TPL(작업 병렬 라이브러리)에 익숙하다면 이러한 작업은 새로운 것이 아닙니다. 차이점은 이러한 작업이 여러 가상 머신에서 동시에 실행될 수 있으며, Durable Functions 확장은 엔드투엔드 실행이 프로세스 재활용에 탄력적으로 수행되도록 보장한다는 것입니다.

`Task.WhenAll`에서 기다린 후에 모든 함수 호출이 완료되고 값을 다시 반환했습니다. `E2_CopyFileToBlob`을 호출할 때마다 업로드된 바이트 수가 반환되므로 총 바이트 수를 계산하는 것은 이러한 반환 값을 모두 추가하는 문제입니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

함수는 오케스트레이터 함수에 표준 *function.json* 을 사용합니다.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

다음은 오케스트레이터 함수를 구현하는 코드입니다.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

`yield context.df.Task.all(tasks);` 줄에 유의하세요. `E2_CopyFileToBlob` 함수에 대한 모든 개별 호출은 일시 중단되지 *않았으며* 병렬로 실행할 수 있습니다. 이 작업 배열을 `context.df.Task.all`에 전달하면 *모든 복사 작업이 완료될 때까지* 완료되지 않는 작업을 다시 가져옵니다. JavaScript의 [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)에 대해 잘 알고 있는 경우 낯설지 않을 것입니다. 차이점은 이러한 작업이 여러 가상 머신에서 동시에 실행될 수 있으며, Durable Functions 확장은 엔드투엔드 실행이 프로세스 재활용에 탄력적으로 수행되도록 보장한다는 것입니다.

> [!NOTE]
> 작업은 개념상 JavaScript 프라미스와 비슷하지만 오케스트레이터 함수는 `Promise.all` 및 `Promise.race` 대신 `context.df.Task.all` 및 `context.df.Task.any`를 사용하여 작업 병렬 처리를 관리해야 합니다.

`context.df.Task.all`에서 일시 중단된 후에 모든 함수 호출이 완료되고 값을 다시 반환했습니다. `E2_CopyFileToBlob`을 호출할 때마다 업로드된 바이트 수가 반환되므로 총 바이트 수를 계산하는 것은 이러한 반환 값을 모두 추가하는 문제입니다.

# <a name="python"></a>[Python](#tab/python)

함수는 오케스트레이터 함수에 표준 *function.json* 을 사용합니다.

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/function.json)]

다음은 오케스트레이터 함수를 구현하는 코드입니다.

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/\_\_init\_\_.py)]

`yield context.task_all(tasks);` 줄에 유의하세요. `E2_CopyFileToBlob` 함수에 대한 모든 개별 호출은 일시 중단되지 *않았으며* 병렬로 실행할 수 있습니다. 이 작업 배열을 `context.task_all`에 전달하면 *모든 복사 작업이 완료될 때까지* 완료되지 않는 작업을 다시 가져옵니다. Python의 [`asyncio.gather`](https://docs.python.org/3/library/asyncio-task.html#asyncio.gather)에 대해 잘 알고 있는 경우 낯설지 않을 것입니다. 차이점은 이러한 작업이 여러 가상 머신에서 동시에 실행될 수 있으며, Durable Functions 확장은 엔드투엔드 실행이 프로세스 재활용에 탄력적으로 수행되도록 보장한다는 것입니다.

> [!NOTE]
> 작업은 개념적으로 Python awaitables와 유사하지만 오케스트레이터 함수는 `yield`뿐만 아니라 `context.task_all` 및 `context.task_any` API를 사용하여 작업 병렬화를 관리해야 합니다.

`context.task_all`에서 일시 중단된 후에 모든 함수 호출이 완료되고 값을 다시 반환했습니다. `E2_CopyFileToBlob`에 대한 각 호출은 업로드된 바이트 수를 반환하므로 모든 반환 값을 함께 추가하여 총 바이트 수를 계산할 수 있습니다.

---

### <a name="helper-activity-functions"></a>도우미 작업 함수

도우미 작업 함수는 다른 샘플과 마찬가지로 `activityTrigger` 트리거 바인딩을 사용하는 일반 함수일 뿐입니다.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList 작업 함수

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E2_GetFileList`에 대한 *function.json* 파일은 다음과 같습니다.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

그리고 구현은 다음과 같습니다.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

함수는 `readdirp` 모듈(버전 2.x)을 사용하여 디렉터리 구조를 재귀적으로 읽습니다.

# <a name="python"></a>[Python](#tab/python)

`E2_GetFileList`에 대한 *function.json* 파일은 다음과 같습니다.

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/function.json)]

그리고 구현은 다음과 같습니다.

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/\_\_init\_\_.py)]

---

> [!NOTE]
> 오케스트레이터 함수에 이 코드를 직접 배치할 수 없는 이유가 궁금할 수도 있습니다. 그렇게 할 수도 있지만, 로컬 파일 시스템 액세스를 포함하여 I/O 작업을 수행하지 않아야 하는 오케스트레이터 함수의 기본 규칙 중 하나가 손상될 수 있습니다. 자세한 내용은 [오케스트레이터 함수 코드 제약 조건](durable-functions-code-constraints.md)을 참조하세요.

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob 작업 함수

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> 샘플 코드를 실행하려면 `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet 패키지를 설치해야 합니다.

이 함수는 Azure Functions 바인딩의 몇 가지 고급 기능(즉, [`Binder` 매개 변수](../functions-dotnet-class-library.md#binding-at-runtime) 사용)을 사용하지만 이 연습에서는 이러한 세부 정보에 대해 걱정할 필요가 없습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E2_CopyFileToBlob`에 대한 *function.json* 파일도 마찬가지로 다음과 같이 간단합니다.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

JavaScript 구현은 [Azure Storage SDK for Node](https://github.com/Azure/azure-storage-node)를 사용하여 파일을 Azure Blob Storage에 업로드합니다.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

# <a name="python"></a>[Python](#tab/python)

`E2_CopyFileToBlob`에 대한 *function.json* 파일도 마찬가지로 다음과 같이 간단합니다.

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/function.json)]

Python 구현은 [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)을 사용하여 파일을 Azure Blob Storage에 업로드합니다.

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/\_\_init\_\_.py)]

---

이 구현은 디스크에서 파일을 로드하고 "backups" 컨테이너에서 동일한 이름의 Blob에 콘텐츠를 비동기적으로 스트림합니다. 반환 값은 스토리지에 복사된 바이트 수이며 오케스트레이터 함수에서 집계 합계를 계산하는 데 사용됩니다.

> [!NOTE]
> 이 예제는 I/O 작업을 `activityTrigger` 함수로 이동하는 완벽한 예제입니다. 작업을 여러 머신에 분산할 수 있을 뿐만 아니라 진행 상황에 대한 검사점 설정의 이점을 얻을 수도 있습니다. 어떤 이유로든 호스트 프로세스가 종료되면 이미 완료된 업로드를 알 수 있습니다.

## <a name="run-the-sample"></a>샘플 실행

Windows에서 다음 HTTP POST 요청을 전송하여 오케스트레이션을 시작할 수 있습니다.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

또는 Linux 함수 앱(Python은 현재 Linux for App Service에서만 실행됨)에서 다음과 같이 오케스트레이션을 시작할 수 있습니다.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"/home/site/wwwroot"
```

> [!NOTE]
> 호출하는 `HttpStart` 함수는 JSON 형식의 콘텐츠에서만 작동합니다. 이러한 이유로 `Content-Type: application/json` 헤더가 필요하며 디렉터리 경로는 JSON 문자열로 인코딩됩니다. 또한 HTTP 코드 조각에서는 모든 HTTP 트리거 함수 URL에서 기본 `api/` 접두사를 제거하는 항목이 `host.json` 파일에 있다고 가정합니다. 샘플의 `host.json` 파일에서 이 구성에 대한 변경 내용을 찾을 수 있습니다.

이 HTTP 요청은 `E2_BackupSiteContent` 오케스트레이터를 트리거하고 `D:\home\LogFiles` 문자열을 매개 변수로 전달합니다. 응답에서는 이 백업 작업의 상태를 가져오는 링크를 제공합니다.

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

함수 앱에 있는 로그 파일의 수에 따라 이 작업을 완료하는 데 몇 분이 걸릴 수 있습니다. 이전 HTTP 202 응답의 `Location` 헤더에 있는 URL을 쿼리하여 최신 상태를 가져올 수 있습니다.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

이 경우 함수는 계속 실행 중입니다. 오케스트레이터 상태와 마지막으로 업데이트된 시간에 저장된 입력을 확인할 수 있습니다. `Location` 헤더 값을 계속 사용하여 완료를 폴링할 수 있습니다. 상태가 "Completed"이면 다음과 비슷한 HTTP 응답 값이 표시됩니다.

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

이제 오케스트레이션이 완료되었으며 완료하는 데 걸린 시간을 확인할 수 있습니다. `output` 필드의 값도 표시되며, 여기서는 약 450KB의 로그가 업로드되었음을 나타냅니다.

## <a name="next-steps"></a>다음 단계

이 예제는 팬아웃/팬인 패턴의 구현 방법을 보여 줍니다. 다음 샘플은 [지속성 타이머](durable-functions-timers.md)를 사용하여 모니터링 패턴을 구현하는 방법을 보여줍니다.

> [!div class="nextstepaction"]
> [모니터링 샘플 실행](durable-functions-monitor.md)