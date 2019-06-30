---
title: Python 함수에 Azure Storage 큐 바인딩 추가
description: Azure CLI 및 Functions Core Tools를 사용하여 Python 함수에 Azure Storage 큐 출력 바인딩을 추가하는 방법을 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: aaeee4238110faa7a842073af8431b30b885db3c
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870024"
---
# <a name="add-an-azure-storage-queue-binding-to-your-function"></a>함수에 Azure Storage 큐 바인딩 추가

Azure Functions를 사용하면 자체 통합 코드를 작성하지 않고도 함수에 Azure 서비스 및 기타 리소스를 연결할 수 있습니다. 입력과 출력을 모두 나타내는 이러한 *바인딩*은 함수 정의 내에서 선언됩니다. 바인딩의 데이터는 함수에 매개 변수로 제공됩니다. 트리거는 특수한 형식의 입력 바인딩입니다. 한 함수는 하나의 트리거만 가질 수 있지만, 입력 및 출력 바인딩은 여러 개 가질 수 있습니다. 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

[이전의 빠른 시작 문서](functions-create-first-function-python.md)에서 Azure Storage 큐를 사용하여 만든 함수를 통합하는 방법을 보여줍니다. 이 함수에 추가되는 출력 바인딩은 HTTP 요청의 데이터를 큐의 메시지에 씁니다. 

대부분의 바인딩은 Functions에서 바인딩된 서비스에 액세스할 때 사용할 저장된 연결 문자열이 필요합니다. 이 부분을 간편하게 해결하려면 함수 앱으로 만든 Storage 계정을 사용합니다. 이 계정에 대한 연결은 이미 `AzureWebJobsStorage` 앱 설정에 저장되어 있습니다.  

## <a name="prerequisites"></a>필수 조건

이 문서를 시작하기에 앞서 [Python 빠른 시작의 1부](functions-create-first-function-python.md) 단계를 먼저 완료합니다.

## <a name="download-the-function-app-settings"></a>함수 앱 설정 다운로드

이전의 빠른 시작 문서에서는 Azure에서 Storage 계정과 함께 함수 앱을 만들었습니다. 이 계정의 연결 문자열은 Azure의 앱 설정에 안전하게 저장됩니다. 이 문서에서는 같은 계정의 Storage 큐에 메시지를 작성합니다. 함수를 로컬로 실행할 때 Storage 계정에 연결하려면 앱 설정을 local.settings.json 파일에 다운로드해야 합니다. 다음 Azure Functions Core Tools 명령을 실행하여 설정을 local.settings.json에 다운로드하고, `<APP_NAME>`을 이전 문서의 함수 앱 이름으로 바꿉니다.

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Azure 계정에 로그인해야 할 수도 있습니다.

> [!IMPORTANT]  
> local.settings.json 파일은 비밀을 포함하고 있으므로 절대 게시되지 않으며, 소스 제어에서 제외되어야 합니다.

Storage 계정 연결 문자열인 `AzureWebJobsStorage` 값이 필요합니다. 이 연결을 사용하여 출력 바인딩이 예상대로 작동하는지 확인합니다.

## <a name="add-an-output-binding"></a>출력 바인딩 추가

Functions에서 각 바인딩 형식의 `direction`, `type` 및 고유한 `name`을 function.json 파일에 정의해야 합니다. 바인딩 형식에 따라 추가 속성이 필요할 수 있습니다. [큐 출력 구성](functions-bindings-storage-queue.md#output---configuration)은 Azure Storage 큐 바인딩에 필요한 필드를 설명합니다.

바인딩을 만들려면 바인딩 구성 개체를 `function.json` 파일에 추가합니다. HttpTrigger 폴더에서 function.json 파일을 편집하여 다음 속성을 갖고 있는 개체를 `bindings` 배열에 추가합니다.

| 자산 | 값 | 설명 |
| -------- | ----- | ----------- |
| **`name`** | `msg` | 코드에서 참조되는 바인딩 매개 변수를 식별하는 이름입니다. |
| **`type`** | `queue` | 바인딩은 Azure Storage 큐 바인딩입니다. |
| **`direction`** | `out` | 바인딩은 출력 바인딩입니다. |
| **`queueName`** | `outqueue` | 바인딩이 데이터를 쓰는 큐의 이름입니다. 바인딩을 처음 사용할 때 *queueName*이 없으면 바인딩이 알아서 만듭니다. |
| **`connection`** | `AzureWebJobsStorage` | Storage 계정의 연결 문자열이 포함된 앱 설정의 이름입니다. `AzureWebJobsStorage` 설정은 함수 앱을 사용하여 만든 Storage 계정의 연결 문자열을 포함합니다. |

이제 아래의 예제와 같은 function.json 파일을 확인할 수 있습니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>출력 바인딩을 사용하는 코드 추가

구성이 완료되면 바인딩의 `name`을 사용하여 함수 시그니처의 메서드 특성으로 바인딩에 액세스할 수 있습니다. 다음 예제의 `msg`는 [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)의 인스턴스입니다.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

출력 바인딩을 사용하면 인증을 받거나 큐 참조를 가져오거나 데이터를 쓸 때 Azure Storage SDK 코드를 사용할 필요가 없습니다. Functions 런타임 및 큐 출력 바인딩이 이러한 작업을 알아서 처리합니다.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

이전과 마찬가지로 다음 명령을 사용하여 로컬로 Functions 런타임을 시작합니다.

```bash
func host start
```

> [!NOTE]  
> 이전 문서에서 host.json 파일에 확장 번들을 사용하도록 설정했기 때문에 시작하는 동안 [Storage 바인딩 확장](functions-bindings-storage-blob.md#packages---functions-2x)이 자동으로 다운로드 및 설치되었습니다.

런타임 출력에서 `HttpTrigger` 함수의 URL을 복사하고 브라우저의 주소 표시줄에 붙여넣습니다. 이 URL에 쿼리 문자열 `?name=<yourname>`을 추가하고 요청을 실행합니다. 브라우저에 이전 문서와 똑같은 응답이 표시됩니다.

이번에는 출력 바인딩이 Storage 계정에 `outqueue`라는 큐를 만들고 똑같은 이 문자열을 사용하여 메시지를 추가합니다.

다음으로, Azure CLI를 사용하여 새 큐를 살펴보고 메시지가 추가되었는 확인합니다. [Microsoft Azure Storage Explorer][Azure Storage Explorer] 또는 [Azure Portal](https://portal.azure.com)을 사용하여 큐를 볼 수도 있습니다.

### <a name="set-the-storage-account-connection"></a>Storage 계정 연결 설정

local.settings.json 파일을 열고, Storage 계정 연결 문자열인 `AzureWebJobsStorage` 값을 복사합니다. 다음 Bash 명령을 사용하여 `AZURE_STORAGE_CONNECTION_STRING` 환경 변수를 연결 문자열로 설정합니다.

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

`AZURE_STORAGE_CONNECTION_STRING` 환경 변수에 연결 문자열이 설정되면 매번 인증할 필요 없이 Storage 계정에 액세스할 수 있습니다.

### <a name="query-the-storage-queue"></a>Storage 큐 쿼리

다음 예제처럼 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 명령을 사용하여 계정의 Storage 큐를 볼 수 있습니다.

```azurecli-interactive
az storage queue list --output tsv
```

이 명령의 출력에는 함수가 실행될 때 생성된 `outqueue`라는 큐가 포함됩니다.

다음으로, 다음 예제처럼 [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 명령을 사용하여 이 큐의 메시지를 봅니다.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

반환된 문자열은 함수를 테스트하기 위해 보낸 메시지와 동일해야 합니다.

> [!NOTE]  
> 이전 예제는 base64에서 반환된 문자열을 디코딩합니다. Queue 스토리지 바인딩이 Azure Storage에 [base64 문자열](functions-bindings-storage-queue.md#encoding)로 쓰고 읽기 때문입니다.

이제 업데이트된 함수 앱을 Azure에 다시 게시할 차례입니다.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

마찬가지로 cURL 또는 브라우저를 사용하여 배포된 함수를 테스트할 수 있습니다. 앞에서 했던 것처럼, 다음 예제와 같이 `&name=<yourname>` 쿼리 문자열을 URL에 추가합니다.

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

[Storage 큐 메시지를 검사](#query-the-storage-queue)하여 출력 바인딩이 큐에 새 메시지를 다시 생성하는지 확인할 수 있습니다.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

Storage 큐에 데이터를 쓰도록 HTTP 트리거 함수를 업데이트했습니다. Python을 사용하여 Azure Functions를 개발하는 방법에 대한 자세한 내용은 [Azure Functions Python 개발자 가이드](functions-reference-python.md) 및 [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)을 참조하세요.

다음으로, 함수 앱에 Application Insights 모니터링을 사용하도록 설정해야 합니다.

> [!div class="nextstepaction"]
> [Application Insights 통합 사용](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/