---
title: Azure Functions에 대한 Python 개발자 참조
description: Python으로 함수를 개발하는 방법 이해
ms.topic: article
ms.date: 12/13/2019
ms.custom: devx-track-python
ms.openlocfilehash: f9b81a7263dc9a1bdae9fd881519ac734da2c6bc
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642200"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 개발자 가이드

이 문서에서는 Python을 사용하여 Azure Functions를 개발하는 방법을 소개합니다. 아래 내용은 [Azure Functions 개발자 가이드](functions-reference.md)를 이미 읽었다고 가정합니다.

Python의 독립실행형 함수 프로젝트 샘플은 [Python Functions 샘플](/samples/browse/?products=azure-functions&languages=python)을 참조하세요.

## <a name="programming-model"></a>프로그래밍 모델

Azure Functions의 함수는 입력을 처리하고 출력을 생성하는 Python 스크립트의 상태 비저장 메서드여야 합니다. 기본적으로 런타임은 메서드가 `__init__.py` 파일에서 `main()`이라는 글로벌 메서드로 구현될 것으로 예상합니다. [대체 진입점을 지정](#alternate-entry-point)할 수도 있습니다.

트리거 및 바인딩의 데이터는 *function.json* 파일에 정의된 `name` 속성을 사용하여 메서드 특성을 통해 함수에 바인딩됩니다. 예를 들어 아래의 _function.json_은 `req`라는 HTTP 요청에 의해 트리거되는 단순 함수를 설명합니다.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

이 정의에 따라 함수 코드를 포함하는 `__init__.py` 파일은 다음 예제와 비슷합니다.

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

또한 Python 형식 주석을 사용하여 함수에서 매개 변수 형식 및 반환 형식을 명시적으로 선언할 수 있습니다. 이렇게 하면 여러 Python 코드 편집기에서 제공하는 인텔리센스 및 자동 완성 기능을 사용할 수 있습니다.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

[azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 패키지에 포함된 Python 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다.

## <a name="alternate-entry-point"></a>대체 진입점

필요한 경우 *function.json* 파일에서 `scriptFile` 및 `entryPoint` 속성을 지정하여 함수의 기본 동작을 변경할 수 있습니다. 예를 들어 아래의 _function.json_은 _main.py_ 파일의 `customentry()` 메서드를 Azure 함수의 진입점으로 사용하도록 런타임에 지시합니다.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>폴더 구조

Python Functions 프로젝트에 권장하는 폴더 구조는 다음 예제와 같습니다.

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 | - Dockerfile
 tests
```
기본 프로젝트 폴더(\_\_app\_\_)에는 다음 파일이 포함될 수 있습니다.

* *local.settings.json*: 로컬에서 실행될 때 앱 설정과 연결 문자열을 저장하는 데 사용됩니다. 이 파일은 Azure에 게시되지 않습니다. 자세한 내용은 [local.settings.file](functions-run-local.md#local-settings-file)을 참조하세요.
* *requirements.txt*: Azure에 게시할 때 설치되는 패키지 목록이 포함됩니다.
* *host.json*: 함수 앱의 모든 함수에 영향을 주는 글로벌 구성 옵션이 포함됩니다. 이 파일은 Azure에 게시됩니다. 로컬로 실행할 경우 일부 옵션이 지원되지 않습니다. 자세한 내용은 [host.json](functions-host-json.md)을 참조하세요.
* *.funcignore*: (선택 사항) Azure에 게시하면 안 되는 파일을 선언합니다.
* *Dockerfile*: (선택 사항) [사용자 지정 컨테이너](functions-create-function-linux-custom-image.md)에서 프로젝트를 게시할 때 사용됩니다.

각 함수에는 자체 코드 파일과 바인딩 구성 파일(function.json)이 있습니다.

Azure의 함수 앱에 프로젝트를 배포할 때 기본 프로젝트( *\_\_app\_\_* ) 폴더 자체가 아닌 폴더의 전체 내용을 패키지에 포함해야 합니다. 프로젝트 폴더와 다른 별도의 폴더에 테스트를 유지하는 것이 좋습니다(이 예제에서는 `tests`). 이렇게 하면 앱에서 테스트 코드를 배포하는 일이 없습니다. 자세한 내용은 [단위 테스트](#unit-testing)를 참조하세요.

## <a name="import-behavior"></a>가져오기 동작

함수 코드에서 명시적 상대 참조와 절대 참조를 모두 사용하여 모듈을 가져올 수 있습니다. 위에 표시된 폴더 구조에 따라, 다음 가져오기는 함수 파일 *\_\_app\_\_\my\_first\_function\\_\_init\_\_.py* 내부에서 작동합니다.

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

다음 가져오기는 동일한 파일 내에서 *작동하지 않습니다*.

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

공유 코드는 *\_\_app\_\_* 에 있는 별도의 폴더에 보관해야 합니다. *shared\_code* 폴더의 모듈을 참조하려면 다음 구문을 사용합니다.

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>트리거 및 입력

입력은 Azure Functions에서 트리거 입력과 추가 입력의 두 가지 범주로 나뉩니다. `function.json`에서는 둘이 서로 다르지만, Python 코드에서는 사용법이 동일합니다.  트리거 및 입력 소스의 연결 문자열 또는 비밀은 로컬로 실행할 때에는 `local.settings.json` 파일의 값에 매핑되고, Azure에서 실행할 때에는 애플리케이션 설정에 매핑됩니다.

예를 들어 다음 코드는 둘 사이의 차이점을 보여줍니다.

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

함수가 호출되면 HTTP 요청이 `req`로 함수에 전달됩니다. 항목은 경로 URL의 _ID_를 기반으로 Azure Blob Storage에서 검색되고 함수 본문에서 `obj`로 제공됩니다.  여기서 지정된 스토리지 계정은 AzureWebJobsStorage 앱 설정에 있는 연결 문자열로, 함수 앱에서 사용하는 것과 동일한 스토리지 계정입니다.


## <a name="outputs"></a>outputs

출력은 반환 값 및 출력 매개 변수 둘 다로 표현될 수 있습니다. 출력이 하나만 있는 경우 반환 값을 사용하는 것이 좋습니다. 다중 출력의 경우 출력 매개 변수를 사용해야 합니다.

함수의 반환 값을 출력 바인딩의 값으로 사용하려면 바인딩의 `name` 속성을 `function.json`의 `$return`으로 설정해야 합니다.

다중 출력을 생성하려면 [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) 인터페이스에서 제공하는 `set()` 메서드를 사용하여 바인딩에 값을 할당합니다. 예를 들어 다음 함수는 메시지를 큐로 푸시하고 HTTP 응답도 반환할 수 있습니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>로깅

함수 앱의 루트 [`logging`](https://docs.python.org/3/library/logging.html#module-logging) 처리기를 통해 Azure Functions 런타임 로거에 액세스할 수 있습니다. 이 로거는 Application Insights에 연결되어 있으며, 함수를 실행하는 동안 발생하는 경고 및 오류에 플래그를 지정할 수 있도록 합니다.

다음 예제는 HTTP 트리거를 통해 함수가 호출될 때 정보 메시지를 기록합니다.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

다양한 추적 수준에서 콘솔에 쓸 수 있는 추가 로깅 메서드가 제공됩니다.

| 방법                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | 루트 로거에 위험 수준의 메시지를 기록합니다.  |
| **`error(_message_)`**   | 루트 로거에 오류 수준의 메시지를 기록합니다.    |
| **`warning(_message_)`**    | 루트 로거에 경고 수준의 메시지를 기록합니다.  |
| **`info(_message_)`**    | 루트 로거에 정보 수준의 메시지를 기록합니다.  |
| **`debug(_message_)`** | 루트 로거에 디버그 수준의 메시지를 기록합니다.  |

로깅에 대한 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

## <a name="http-trigger-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP 트리거는 파일의 function.js에 정의 되어 있습니다. 바인딩의 `name`은 함수의 명명된 매개 변수와 일치해야 합니다.
이전 예제에서는 바인딩 이름 `req`를 사용합니다. 이 매개 변수는 [HttpRequest] 개체이며 [HttpResponse] 개체가 반환됩니다.

[HttpRequest] 개체에서 요청 헤더, 쿼리 매개 변수, 경로 매개 변수 및 메시지 본문을 가져올 수 있습니다.

다음 예제는 [Python용 HTTP 트리거 템플릿](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)에서 가져온 것입니다.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

이 함수에서 `name` 쿼리 매개 변수의 값은 [HttpRequest] 개체의 `params` 매개 변수에서 가져옵니다. JSON으로 인코딩된 메시지 본문은 `get_json` 메서드를 사용하여 읽습니다.

마찬가지로, 반환된 [HttpResponse] 개체의 응답 메시지에 대한 `status_code` 및 `headers`를 설정할 수 있습니다.

## <a name="scaling-and-concurrency"></a>스케일링 및 동시성

기본적으로 Azure Functions는 애플리케이션의 부하를 자동으로 모니터링하면서 필요에 따라 Python에 사용할 추가 호스트 인스턴스를 만듭니다. Functions는 QueueTrigger에 대한 메시지 보존 기간, 큐 크기 등의 기본 제공(사용자 구성 불가능) 임계값을 여러 트리거 유형에 사용하여 인스턴스를 추가할 시기를 결정합니다. 자세한 내용은 [사용 계획 및 프리미엄 계획의 작동 방식](functions-scale.md#how-the-consumption-and-premium-plans-work)을 참조하세요.

이러한 스케일링 동작은 여러 애플리케이션을 지원하기에 충분합니다. 그러나 다음과 같은 특징을 가진 애플리케이션은 효과적으로 스케일링되지 않을 수 있습니다.

- 애플리케이션에서 여러 개의 동시 호출을 처리해야 합니다.
- 애플리케이션에서 대량의 I/O 이벤트를 처리합니다.
- 애플리케이션이 I/O 바인딩되어 있습니다.

이러한 경우에는 비동기 패턴과 여러 언어 작업자 프로세스를 사용하여 성능을 향상할 수 있습니다.

### <a name="async"></a>Async

Python은 단일 스레드 런타임이므로, Python에 대한 호스트 인스턴스는 한 번에 하나의 함수 호출만 처리할 수 있습니다. 대량의 I/O 이벤트를 처리하고/처리하거나 I/O 바인딩된 애플리케이션은 함수를 비동기적으로 실행하여 성능을 향상할 수 있습니다.

함수를 비동기적으로 실행하려면 [asyncio](https://docs.python.org/3/library/asyncio.html)를 사용하여 함수를 직접 실행하는 `async def` 문을 사용하세요.

```python
async def main():
    await some_nonblocking_socket_io_op()
```

`async` 키워드가 없는 함수는 asyncio 스레드 풀에서 자동으로 실행됩니다.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>여러 언어 작업자 프로세스 사용

기본적으로 모든 Functions 호스트 인스턴스에는 언어 작업자 프로세스가 하나만 있습니다. [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 애플리케이션 설정을 사용하여 호스트당 작업자 프로세스 수를 10개까지 늘릴 수 있습니다. 그러면 Azure Functions는 이러한 작업자 사이에 동시 함수 호출을 균등하게 분산하려고 시도합니다.

요구 사항을 충족하기 위해 애플리케이션을 스케일 아웃할 때 Functions가 만드는 각 호스트에 FUNCTIONS_WORKER_PROCESS_COUNT가 적용됩니다.

## <a name="context"></a>Context

실행 중에 함수의 호출 컨텍스트를 가져오려면 해당 서명에 [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) 인수를 포함해야 합니다.

다음은 그 예입니다.

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) 클래스에는 다음과 같은 문자열 특성이 있습니다.

`function_directory` 함수가 실행되는 디렉터리입니다.

`function_name` 함수의 이름입니다.

`invocation_id` 현재 함수 호출의 ID입니다.

## <a name="global-variables"></a>글로벌 변수

나중에 실행할 수 있도록 앱 상태가 유지된다는 보장은 없습니다. 그러나 Azure Functions 런타임은 동일한 앱을 여러 차례 실행할 때 종종 동일한 프로세스를 재사용합니다. 비용이 많이 드는 컴퓨팅 결과를 캐시하려면 글로벌 변수로 선언하면 됩니다.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>환경 변수

Functions에서 서비스 연결 문자열 같은 [애플리케이션 설정](functions-app-settings.md)은 실행 중에 환경 변수로 공개됩니다. `import os`를 선언한 다음, `setting = os.environ["setting-name"]`을 사용하여 이러한 설정에 액세스할 수 있습니다.

다음 예제에서는 `myAppSetting`이라는 키가 있는 [애플리케이션 설정](functions-how-to-use-azure-function-app-settings.md#settings)을 가져옵니다.

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

로컬에서 개발하는 경우 애플리케이션 설정은 [local.settings.json 파일에 유지됩니다](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Python 버전

Azure Functions에서 지원하는 Python 버전은 다음과 같습니다.

| Functions 버전 | Python<sup>*</sup> 버전 |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>공식 CPython 배포판

Azure에서 함수 앱을 만들 때 특정 Python 버전을 요청하려면 [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) 명령의 `--runtime-version` 옵션을 사용합니다. Functions 런타임 버전은 `--functions-version` 옵션을 통해 설정됩니다. Python 버전은 함수 앱을 만들 때 설정되며 변경할 수 없습니다.

로컬로 실행하는 경우 런타임에서는 사용 가능한 Python 버전을 사용합니다.

## <a name="package-management"></a>패키지 관리

Azure Functions Core Tools 또는 Visual Studio Code를 사용하여 로컬에서 개발하는 경우 필요한 패키지의 이름과 버전을 `requirements.txt` 파일에 추가하고 `pip`를 사용하여 설치합니다.

예를 들어 다음 요구 사항 파일 및 pip 명령을 사용하여 PyPI에서 `requests` 패키지를 설치할 수 있습니다.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Azure에 게시

게시할 준비가 되면 공개적으로 제공되는 모든 종속성이 프로젝트 디렉터리의 루트에 있는 requirements.txt 파일에 나열되는지 확인합니다.

가상 환경 폴더를 비롯하여 게시에서 제외되는 프로젝트 파일과 폴더는 .funcignore 파일에 나열됩니다.

Azure에 Python 프로젝트를 게시 하는 데 지원 되는 세 가지 빌드 작업은 원격 빌드, 로컬 빌드 및 사용자 지정 종속성을 사용한 빌드입니다.

또한 Azure Pipelines를 사용 하 여 종속성을 빌드하고 CD (지속적인 업데이트)를 사용 하 여 게시할 수 있습니다. 자세히 알아보려면 [Azure DevOps를 사용 하 여 지속적인](functions-how-to-azure-devops.md)업데이트를 참조 하세요.

### <a name="remote-build"></a>원격 빌드

원격 빌드를 사용 하는 경우 서버에서 복원 된 종속성과 기본 종속성이 프로덕션 환경과 일치 합니다. 이로 인해 더 작은 배포 패키지가 업로드 됩니다. Windows에서 Python 앱을 개발할 때 원격 빌드를 사용 합니다. 프로젝트에 사용자 지정 종속성이 있는 경우 [추가 인덱스 URL로 원격 빌드를 사용할](#remote-build-with-extra-index-url)수 있습니다.

종속성은 requirements.txt 파일의 내용에 따라 원격으로 가져옵니다. [원격 빌드](functions-deployment-technologies.md#remote-build)는 권장 빌드 방법입니다. 기본적으로 Azure Functions Core Tools는 다음과 같은 [func azure functionapp publish](functions-run-local.md#publish) 명령을 사용하여 Python 프로젝트를 Azure에 게시할 때 원격 빌드가 필요합니다.

```bash
func azure functionapp publish <APP_NAME>
```

`<APP_NAME>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다.

[Visual Studio Code용 Azure Functions 확장](functions-create-first-function-vs-code.md#publish-the-project-to-azure) 역시 기본적으로 원격 빌드를 요청합니다.

### <a name="local-build"></a>로컬 빌드

종속성은 requirements.txt 파일의 내용에 따라 로컬로 가져옵니다. 다음 [func azure functionapp publish](functions-run-local.md#publish) 명령을 사용하여 로컬 빌드로 게시하면 원격 빌드를 차단할 수 있습니다.

```command
func azure functionapp publish <APP_NAME> --build local
```

`<APP_NAME>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다.

`--build local` 옵션을 사용하면 requirements.txt 파일에서 프로젝트 종속성을 읽고 해당하는 종속 패키지를 로컬로 다운로드하여 설치합니다. 프로젝트 파일과 종속성은 로컬 컴퓨터에서 Azure로 배포됩니다. 결과적으로 더 큰 배포 패키지가 Azure에 업로드됩니다. 어떤 이유로 Core Tools가 requirements.txt 파일의 종속성이 얻을 수 없는 경우 사용자 지정 종속성 옵션을 사용해야 합니다.

Windows에서 로컬로 개발 하는 경우에는 로컬 빌드를 사용 하지 않는 것이 좋습니다.

### <a name="custom-dependencies"></a>사용자 지정 종속성

프로젝트에는 [Python 패키지 인덱스](https://pypi.org/)에서 종속성이 없는 경우 두 가지 방법으로 프로젝트를 빌드할 수 있습니다. 빌드 메서드는 프로젝트를 빌드하는 방법에 따라 달라 집니다.

#### <a name="remote-build-with-extra-index-url"></a>추가 인덱스 URL을 사용 하 여 원격 빌드

액세스할 수 있는 사용자 지정 패키지 인덱스에서 패키지를 사용할 수 있는 경우 원격 빌드를 사용 합니다. 게시 하기 전에 이라는 [앱 설정을 만들어야](functions-how-to-use-azure-function-app-settings.md#settings) `PIP_EXTRA_INDEX_URL` 합니다. 이 설정의 값은 사용자 지정 패키지 인덱스의 URL입니다. 이 설정을 사용 하면 옵션을 사용 하 여 원격 빌드가 실행 되도록 지시할 수 `pip install` `--extra-index-url` 있습니다. 자세히 알아보려면 [Python pip 설치 설명서](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)를 참조 하세요.

추가 패키지 인덱스 Url을 사용 하 여 기본 인증 자격 증명을 사용할 수도 있습니다. 자세히 알아보려면 Python 설명서의 [기본 인증 자격 증명](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) 을 참조 하세요.

#### <a name="install-local-packages"></a>로컬 패키지 설치

당사의 도구에서 공개적으로 제공하지 않는 패키지를 프로젝트에 사용하는 경우 해당 패키지를 \_\_app\_\_/.python_packages 디렉터리에 배치하면 앱에서 사용할 수 있습니다. 게시하기 전에, 다음 명령을 실행하여 종속성을 로컬로 설치합니다.

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

프로젝트 폴더에 종속성을 이미 설치 했으므로 사용자 지정 종속성을 사용 하는 경우 `--no-build` 게시 옵션을 사용 해야 합니다.

```command
func azure functionapp publish <APP_NAME> --no-build
```

`<APP_NAME>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다.

## <a name="unit-testing"></a>단위 테스트

Python으로 작성된 함수는 다른 Python 코드와 마찬가지로 표준 테스트 프레임워크를 사용하여 테스트할 수 있습니다. 대부분의 바인딩에서 `azure.functions` 패키지로 적절한 클래스 인스턴스를 만들어 모의 입력 개체를 만들 수 있습니다. [`azure.functions`](https://pypi.org/project/azure-functions/) 패키지는 즉시 사용할 수 없으므로, 위의 [패키지 관리](#package-management) 섹션에서 설명한 대로 `requirements.txt` 파일을 통해 설치해야 합니다.

예를 들어 다음은 HTTP 트리거 함수의 모의 테스트입니다.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
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
    }
  ]
}
```

```python
# __app__/HttpTrigger/__init__.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

다음은 큐 트리거 함수를 사용하는 또 다른 예입니다.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```
## <a name="temporary-files"></a>임시 파일

`tempfile.gettempdir()` 메서드는 임시 폴더를 반환하며, 이 폴더는 Linux에서 `/tmp`입니다. 애플리케이션에서는 함수가 실행 중에 만들어서 사용하는 임시 파일을 이 디렉터리에 저장할 수 있습니다.

> [!IMPORTANT]
> 임시 디렉터리에 기록된 파일은 호출 간에 유지되지 않을 수 있습니다. 스케일 아웃 도중에 임시 파일은 인스턴스 간에 공유되지 않습니다.

다음은 임시 디렉터리(`/tmp`)에 명명된 임시 파일을 만드는 예제입니다.

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

프로젝트 폴더와 다른 별도의 폴더에 테스트를 유지하는 것이 좋습니다. 이렇게 하면 앱에서 테스트 코드를 배포하는 일이 없습니다.

## <a name="preinstalled-libraries"></a>사전 설치 된 라이브러리

Python 함수 런타임과 함께 제공 되는 몇 가지 라이브러리가 있습니다.

### <a name="python-standard-library"></a>Python 표준 라이브러리

Python 표준 라이브러리에는 각 Python 배포와 함께 제공 되는 기본 제공 Python 모듈 목록이 포함 되어 있습니다. 이러한 라이브러리의 대부분은 파일 i/o와 같은 시스템 기능에 액세스 하는 데 도움이 됩니다. Windows 시스템에서 이러한 라이브러리는 Python을 사용 하 여 설치 됩니다. Unix 기반 시스템에서 패키지 컬렉션에 의해 제공 됩니다.

이러한 라이브러리의 전체 목록에 대 한 자세한 내용을 보려면 아래 링크를 방문 하세요.

* [Python 3.6 표준 라이브러리](https://docs.python.org/3.6/library/)
* [Python 3.7 표준 라이브러리](https://docs.python.org/3.7/library/)
* [Python 3.8 표준 라이브러리](https://docs.python.org/3.8/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python 작업자 종속성

함수 Python 작업자에는 특정 라이브러리 집합이 필요 합니다. 함수에서 이러한 라이브러리를 사용할 수도 있지만 Python 표준의 일부가 아닙니다. 함수가 이러한 라이브러리를 사용 하는 경우 Azure Functions 외부에서 실행 될 때 코드에서 사용 하지 못할 수 있습니다. [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) 파일의 **install \_ requires** 섹션에서 종속성의 자세한 목록을 찾을 수 있습니다.

> [!NOTE]
> 함수 앱의 requirements.txt에 항목이 포함 되어 있는 경우 `azure-functions-worker` 제거 합니다. 함수 작업자는 Azure Functions 플랫폼에 의해 자동으로 관리 되며 새로운 기능 및 버그 수정을 사용 하 여 정기적으로 업데이트 합니다. requirements.txt에 이전 버전의 작업자를 수동으로 설치 하면 예기치 않은 문제가 발생할 수 있습니다.

### <a name="azure-functions-python-library"></a>Python 라이브러리 Azure Functions

모든 Python worker 업데이트에는 [Azure Functions Python 라이브러리 (Azure. 함수)](https://github.com/Azure/azure-functions-python-library)의 새 버전이 포함 되어 있습니다. 이 접근 방식을 사용 하면 각 업데이트가 이전 버전과 호환 되므로 Python 함수 앱을 계속 해 서 업데이트할 수 있습니다. 이 라이브러리의 릴리스 목록은 [azure-함수 PyPi](https://pypi.org/project/azure-functions/#history)에서 찾을 수 있습니다.

런타임 라이브러리 버전은 Azure에서 수정 되며 requirements.txt로 재정의할 수 없습니다. `azure-functions`requirements.txt의 항목은 lint 및 고객 인식에만 해당 됩니다.

런타임에 Python 함수 라이브러리의 실제 버전을 추적 하려면 다음 코드를 사용 합니다.

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>런타임 시스템 라이브러리

Python worker Docker 이미지의 사전 설치 된 시스템 라이브러리 목록을 보려면 아래 링크를 따라 이동 하세요.

|  함수 런타임  | Debian 버전 | Python 버전 |
|------------|------------|------------|
| 버전 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| 버전 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile) |

## <a name="cross-origin-resource-sharing"></a>크로스-원본 자원 공유

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS는 Python 함수 앱을 완벽하게 지원합니다.

## <a name="known-issues-and-faq"></a>알려진 문제 및 FAQ

다음은 일반적인 문제에 대 한 문제 해결 가이드 목록입니다.

* [ModuleNotFoundError 및 ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [' Cygrpc '를 가져올 수 없습니다.](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

모든 알려진 문제 및 기능 요청은 [GitHub 문제](https://github.com/Azure/azure-functions-python-worker/issues) 목록을 사용하여 추적됩니다. 문제가 발생하여 GitHub에서 해당 문제를 찾을 수 없는 경우 새 문제를 열고 해당 문제에 대한 자세한 설명을 제공해 주세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions 패키지 API 설명서](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Blob Storage 바인딩](functions-bindings-storage-blob.md)
* [HTTP 및 Webhook 바인딩](functions-bindings-http-webhook.md)
* [Queue Storage 바인딩](functions-bindings-storage-queue.md)
* [타이머 트리거](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
