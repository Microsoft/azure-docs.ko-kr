---
title: Azure Functions에 대한 Python 개발자 참조
description: Python으로 함수를 개발하는 방법 이해
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: cfac28c4a759cee66c932c7b8cfea053c9c4f505
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921791"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 개발자 가이드

이 문서에서는 Python을 사용하여 Azure Functions를 개발하는 방법을 소개합니다. 아래 내용은 [Azure Functions 개발자 가이드](functions-reference.md)를 이미 읽었다고 가정합니다. 

Python의 독립 실행형 함수 샘플 프로젝트에 대해서는 [Python 함수 샘플](/samples/browse/?products=azure-functions&languages=python)을 참조 하세요. 

## <a name="programming-model"></a>프로그래밍 모델

Azure Functions는 Python 스크립트에서 입력을 처리 하 고 출력을 생성 하는 상태 비저장 메서드가 될 것으로 예상 합니다. 기본적으로 런타임에서는 메서드가 `__init__.py` 파일의 `main()` 라는 전역 메서드로 구현 될 것으로 예상 합니다. 또한 [대체 진입점을 지정할](#alternate-entry-point)수 있습니다.

트리거와 바인딩의 데이터는 *함수 json* 파일에 정의 된 `name` 속성을 사용 하 여 메서드 특성을 통해 함수에 바인딩됩니다. 예를 들어, 아래 _함수인 json_ 은 `req`라는 HTTP 요청에 의해 트리거되는 간단한 함수를 설명 합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` 파일에는 다음 함수 코드가 포함되어 있습니다.

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Python 형식 주석을 사용 하 여 함수에서 특성 형식 및 반환 형식을 명시적으로 선언할 수도 있습니다. 이를 통해 많은 Python 코드 편집기에서 제공 하는 intellisense 및 자동 완성 기능을 사용할 수 있습니다.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

[azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 패키지에 포함된 Python 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다.

## <a name="alternate-entry-point"></a>대체 진입점

선택적으로 `scriptFile` 및 `entryPoint` 속성을 지정 하 여 함수의 기본 동작을 변경할 수 *있습니다.* 예를 들어 아래 _함수_ 는 _main.py_ 파일의 `customentry()` 메서드를 Azure function의 진입점으로 사용 하도록 런타임에 지시 합니다.

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

Python 함수 프로젝트에 권장 되는 폴더 구조는 다음 예제와 같습니다.

```
 __app__
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 tests
```
기본 프로젝트 폴더 (\_\_app\_\_)에는 다음 파일이 포함 될 수 있습니다.

* *로컬*에서 실행 하는 경우 응용 프로그램 설정 및 연결 문자열을 저장 하는 데 사용 됩니다. 이 파일은 Azure에 게시되지 않습니다. 자세한 내용은 [local. settings. 파일](functions-run-local.md#local-settings-file)을 참조 하세요.
* *요구 사항 .txt*: Azure에 게시할 때 시스템이 설치 하는 패키지 목록을 포함 합니다.
* *host. json*: 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션을 포함 합니다. 이 파일은 Azure에 게시됩니다. 모든 옵션은 로컬로 실행할 때 지원 되지 않습니다. 자세한 내용은 [호스트 json](functions-host-json.md)을 참조 하세요.
* *. funcignore*: (선택 사항) Azure에 게시 되지 않아야 하는 파일을 선언 합니다.
* *. .gitignore*: (선택 사항) git 리포지토리에서 제외 되는 파일을 선언 합니다 (예: 로컬 설정).

각 함수에는 자체 코드 파일과 바인딩 구성 파일(function.json)이 있습니다. 

공유 코드는 \_\_app\_\_에서 별도의 폴더에 유지 해야 합니다. SharedCode 폴더의 모듈을 참조하기 위해 다음 구문을 사용할 수 있습니다.

```python
from __app__.SharedCode import myFirstHelperFunction
```

함수에 대한 로컬 모듈을 참조 하려면 다음과 같이 상대 가져오기 구문을 사용할 수 있습니다.

```python
from . import example
```

Azure에서 함수 앱에 프로젝트를 배포할 때 *Functionapp* 폴더의 전체 콘텐츠는 패키지에 포함 되어야 하지만 폴더 자체에는 포함 되지 않아야 합니다. 프로젝트 폴더와 별도의 폴더에 있는 테스트를 유지 관리 하는 것이 좋습니다 (이 예제에서는 `tests`. 그러면 응용 프로그램에 테스트 코드를 배포할 수 있습니다. 자세한 내용은 [단위 테스트](#unit-testing)를 참조 하십시오.

## <a name="triggers-and-inputs"></a>트리거 및 입력

입력은 Azure Functions에서 트리거 입력과 추가 입력의 두 가지 범주로 나뉩니다. `function.json` 파일에서는 다르지만 사용은 Python 코드에서 동일 합니다.  트리거 및 입력 소스에 대한 연결 문자열 또는 암호는 로컬로 실행할 때 `local.settings.json` 파일의 값에 매핑되고, Azure에서 실행할 때 응용 프로그램 설정에 매핑됩니다. 

예를 들어 다음 코드는 두 가지 간의 차이점을 보여 줍니다.

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

함수가 호출되면 HTTP 요청이 `req`로 함수에 전달됩니다. 항목은 경로 URL의 _ID_ 를 기반으로 Azure Blob Storage에서 검색 되 고 함수 본문에서 `obj` 사용할 수 있게 됩니다.  여기서 지정 된 저장소 계정은 AzureWebJobsStorage 앱 설정에 있는 연결 문자열로,이는 함수 앱에서 사용 하는 것과 동일한 저장소 계정입니다.


## <a name="outputs"></a>outputs

출력은 반환 값 및 출력 매개 변수 둘 다로 표현될 수 있습니다. 출력이 하나만 있는 경우 반환 값을 사용하는 것이 좋습니다. 다중 출력의 경우 출력 매개 변수를 사용해야 합니다.

함수의 반환 값을 출력 바인딩의 값으로 사용하려면 바인딩의 `name` 속성을 `function.json`의 `$return`으로 설정해야 합니다.

여러 출력을 생성 하려면 [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) 인터페이스에서 제공 하는 `set()` 메서드를 사용 하 여 값을 바인딩에 할당 합니다. 예를 들어 다음 함수는 메시지를 큐로 푸시하고 HTTP 응답도 반환할 수 있습니다.

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

로깅에 대해 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 참조 하세요.

## <a name="http-trigger-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP 트리거는 함수인 jon file에 정의 되어 있습니다. 바인딩의 `name`은 함수의 명명 된 매개 변수와 일치 해야 합니다. 이전 예제에서는 바인딩 이름 `req`를 사용 합니다. 이 매개 변수는 [HttpRequest] 개체 이며 [HttpResponse] 개체가 반환 됩니다.

[HttpRequest] 개체에서 요청 헤더, 쿼리 매개 변수, 경로 매개 변수 및 메시지 본문을 가져올 수 있습니다. 

다음 예제는 [Python의 HTTP 트리거 템플릿에서](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)가져온 것입니다. 

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

이 함수에서 `name` 쿼리 매개 변수의 값은 [HttpRequest] 개체의 `params` 매개 변수에서 가져옵니다. JSON으로 인코딩된 메시지 본문은 `get_json` 메서드를 사용 하 여 읽습니다. 

마찬가지로 반환 된 [HttpResponse] 개체의 응답 메시지에 대한 `status_code` 및 `headers`를 설정할 수 있습니다.

## <a name="scaling-and-concurrency"></a>크기 조정 및 동시성

기본적으로 Azure Functions는 응용 프로그램의 부하를 자동으로 모니터링 하 고 필요에 따라 Python의 추가 호스트 인스턴스를 만듭니다. 함수는 다양 한 트리거 유형에 대해 기본 제공 (사용자 구성 불가능) 임계값을 사용 하 여 QueueTrigger에 대한 메시지의 보존 기간 및 큐 크기와 같은 인스턴스를 추가할 시기를 결정 합니다. 자세한 내용은 [소비 및 프리미엄 계획의 작동 방식](functions-scale.md#how-the-consumption-and-premium-plans-work)을 참조 하세요.

이러한 크기 조정 동작은 많은 응용 프로그램에서 충분 합니다. 그러나 다음과 같은 특징을 가진 응용 프로그램은 효과적으로 확장 되지 않을 수 있습니다.

- 응용 프로그램은 여러 동시 호출을 처리 해야 합니다.
- 응용 프로그램은 많은 수의 i/o 이벤트를 처리 합니다.
- 응용 프로그램이 I/o 바인딩되어 있습니다.

이러한 경우에는 비동기 패턴을 사용 하 고 여러 언어 작업자 프로세스를 사용 하 여 성능을 향상 시킬 수 있습니다.

### <a name="async"></a>Async

Python은 단일 스레드 런타임 이므로 Python의 호스트 인스턴스는 한 번에 하나의 함수 호출만 처리할 수 있습니다. 많은 i/o 이벤트를 처리 하는 응용 프로그램 또는 i/o 바인딩된 응용 프로그램의 경우 함수를 비동기적으로 실행 하 여 성능을 향상 시킬 수 있습니다.

함수를 비동기적으로 실행 하려면 [asyncio](https://docs.python.org/3/library/asyncio.html) 를 사용 하 여 함수를 직접 실행 하는 `async def` 문을 사용 합니다.

```python
async def main():
    await some_nonblocking_socket_io_op()
```

`async` 키워드가 없는 함수는 asyncio 스레드 풀에서 자동으로 실행 됩니다.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>여러 언어 작업자 프로세스 사용

기본적으로 모든 함수 호스트 인스턴스에는 단일 언어 작업자 프로세스가 있습니다. [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 응용 프로그램 설정을 사용 하 여 호스트 당 작업자 프로세스 수 (최대 10 개)를 늘릴 수 있습니다. 그런 다음 Azure Functions는 이러한 작업자 간에 동시 함수 호출을 균등 하 게 분산 하려고 시도 합니다. 

FUNCTIONS_WORKER_PROCESS_COUNT는 요구를 충족 하도록 응용 프로그램을 확장할 때 함수가 만드는 각 호스트에 적용 됩니다. 

## <a name="context"></a>Context

실행 중에 함수의 호출 컨텍스트를 가져오려면 시그니처에 [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) 인수를 포함 합니다. 

예:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) 클래스에는 다음과 같은 문자열 특성이 있습니다.

`function_directory`  
함수가 실행되는 디렉터리입니다.

`function_name`  
함수의 이름입니다.

`invocation_id`  
현재 함수 호출의 ID입니다.

## <a name="global-variables"></a>글로벌 변수

나중에 실행 하기 위해 앱의 상태를 유지 하는 것은 보장 되지 않습니다. 그러나 Azure Functions 런타임은 동일한 앱을 여러 번 실행 하는 경우에도 동일한 프로세스를 다시 사용할 수도 있습니다. 비용이 많이 드는 계산 결과를 캐시 하려면 전역 변수로 선언 합니다. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>환경 변수

함수에서 서비스 연결 문자열과 같은 [응용 프로그램 설정은](functions-app-settings.md)실행 중에 환경 변수로 노출 됩니다. `import os`를 선언 하 고 `setting = os.environ["setting-name"]`를 사용 하 여 이러한 설정에 액세스할 수 있습니다.

다음 예제에서는 `myAppSetting`라는 키를 사용 하 여 [응용 프로그램 설정을](functions-how-to-use-azure-function-app-settings.md#settings)가져옵니다.

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

로컬 개발의 경우 응용 프로그램 설정은 [로컬. 설정 json 파일에서 유지 관리](functions-run-local.md#local-settings-file)됩니다.  

## <a name="python-version"></a>Python 버전 

현재 Azure Functions는 Python 3.6 및 3.7 .x를 모두 지원 합니다 (공식 CPython 배포). 로컬로 실행 하는 경우 런타임은 사용 가능한 Python 버전을 사용 합니다. Azure에서 함수 앱을 만들 때 특정 Python 버전을 요청 하려면 [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) 명령의 `--runtime-version` 옵션을 사용 합니다. 버전 변경은 함수 앱 만들기 에서만 허용 됩니다.  

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

게시할 준비가 되 면 공개적으로 사용할 수 있는 모든 종속성이 프로젝트 디렉터리의 루트에 있는 요구 사항 .txt 파일에 나열 되는지 확인 합니다. 

가상 환경 폴더를 비롯 하 여 게시에서 제외 되는 프로젝트 파일 및 폴더는 funcignore 파일에 나열 됩니다.

Python 프로젝트를 Azure에 게시 하는 데 지원 되는 빌드 작업에는 세 가지가 있습니다.

+ 원격 빌드: 종속성은 요구 사항 .txt 파일의 내용에 따라 원격으로 가져옵니다. [원격 빌드](functions-deployment-technologies.md#remote-build) 는 권장 되는 빌드 방법입니다. 원격은 Azure 도구의 기본 빌드 옵션 이기도 합니다. 
+ 로컬 빌드: 종속성은 요구 사항 .txt 파일의 내용에 따라 로컬에서 가져옵니다. 
+ 사용자 지정 종속성: 프로젝트에서 공개적으로 사용할 수 없는 패키지를 도구에 사용 합니다. Docker가 필요 합니다.

종속성을 빌드하고 CD (지속적인 업데이트) 시스템을 사용 하 여 게시 하려면 [Azure Pipelines을 사용](functions-how-to-azure-devops.md)합니다.

### <a name="remote-build"></a>원격 빌드

기본적으로 Azure Functions Core Tools는 다음 [func azure functionapp publish](functions-run-local.md#publish) 명령을 사용 하 여 Python 프로젝트를 azure에 게시할 때 원격 빌드를 요청 합니다. 

```bash
func azure functionapp publish <APP_NAME>
```

`<APP_NAME>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다.

또한 [Visual Studio Code Azure Functions 확장](functions-create-first-function-vs-code.md#publish-the-project-to-azure) 은 기본적으로 원격 빌드를 요청 합니다. 

### <a name="local-build"></a>로컬 빌드

다음 [func azure functionapp publish](functions-run-local.md#publish) 명령을 사용 하 여 로컬 빌드와 함께 게시 하는 방법으로 원격 빌드를 방지할 수 있습니다. 

```command
func azure functionapp publish <APP_NAME> --build local
```

`<APP_NAME>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다. 

`--build local` 옵션을 사용 하면 요구 사항 .txt 파일에서 프로젝트 종속성을 읽고 해당 종속 패키지를 로컬로 다운로드 하 여 설치 합니다. 프로젝트 파일 및 종속성은 로컬 컴퓨터에서 Azure로 배포 됩니다. 결과적으로 더 큰 배포 패키지가 Azure에 업로드 됩니다. 어떤 이유로 든 지 요구 사항 .txt 파일의 종속성을 핵심 도구로 가져올 수 없는 경우 게시를 위해 사용자 지정 종속성 옵션을 사용 해야 합니다. 

### <a name="custom-dependencies"></a>사용자 지정 종속성

프로젝트에서 공개적으로 사용할 수 없는 패키지를 도구에서 사용 하는 경우 \_\_app\_\_/. python_packages 디렉터리에 배치 하 여 앱에서 사용할 수 있도록 설정할 수 있습니다. 게시 하기 전에 다음 명령을 실행 하 여 로컬에서 종속성을 설치 합니다.

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

사용자 지정 종속성을 사용 하는 경우 이미 종속성을 설치 했으므로 `--no-build` 게시 옵션을 사용 해야 합니다.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

`<APP_NAME>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다.

## <a name="unit-testing"></a>단위 테스트

Python으로 작성 된 함수는 표준 테스트 프레임 워크를 사용 하 여 다른 Python 코드와 같이 테스트할 수 있습니다. 대부분의 바인딩에서 `azure.functions` 패키지에서 적절 한 클래스의 인스턴스를 만들어 모의 입력 개체를 만들 수 있습니다. [`azure.functions`](https://pypi.org/project/azure-functions/) 패키지는 즉시 사용할 수 없으므로 위의 [패키지 관리](#package-management) 섹션에 설명 된 대로 `requirements.txt` 파일을 통해 설치 해야 합니다. 

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

큐 트리거 함수를 사용 하는 또 다른 예는 다음과 같습니다.

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
## <a name="temporary-files"></a>임시 파일을 선택합니다

`tempfile.gettempdir()` 메서드는 Linux에서 `/tmp`된 임시 폴더를 반환 합니다. 응용 프로그램은이 디렉터리를 사용 하 여 생성 되 고 실행 중에 함수에서 사용 하는 임시 파일을 저장할 수 있습니다. 

> [!IMPORTANT]
> 임시 디렉터리에 기록 된 파일은 호출 간에 유지 되지 않을 수 있습니다. 규모 확장 중에 임시 파일은 인스턴스 간에 공유 되지 않습니다. 

다음 예에서는 임시 디렉터리 (`/tmp`)에 명명 된 임시 파일을 만듭니다.

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

프로젝트 폴더와 별도의 폴더에 테스트를 유지 관리 하는 것이 좋습니다. 그러면 응용 프로그램에 테스트 코드를 배포할 수 있습니다. 

## <a name="known-issues-and-faq"></a>알려진 문제 및 FAQ

모든 알려진 문제 및 기능 요청은 [GitHub 문제](https://github.com/Azure/azure-functions-python-worker/issues) 목록을 사용하여 추적됩니다. 문제가 발생하여 GitHub에서 해당 문제를 찾을 수 없는 경우 새 문제를 열고 해당 문제에 대한 자세한 설명을 제공해 주세요.

### <a name="cross-origin-resource-sharing"></a>크로스-원본 자원 공유

Azure Functions는 CORS (원본 간 리소스 공유)를 지원 합니다. CORS는 [포털](functions-how-to-use-azure-function-app-settings.md#cors) 및 [Azure CLI](/cli/azure/functionapp/cors)를 통해 구성 됩니다. CORS 허용 원본 목록은 함수 앱 수준에서 적용 됩니다. CORS를 사용 하면 응답에 `Access-Control-Allow-Origin` 헤더가 포함 됩니다. 자세한 내용은 [크로스-원본 자원 공유(CORS)](functions-how-to-use-azure-function-app-settings.md#cors)를 참조하십시오.

현재 Python 함수 앱에 대해 허용 되는 원본 목록이 [지원 되지 않습니다](https://github.com/Azure/azure-functions-python-worker/issues/444) . 이러한 제한 때문에 다음 예제와 같이 HTTP 함수에서 `Access-Control-Allow-Origin` 헤더를 명시적으로 설정 해야 합니다.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

OPTIONS HTTP 메서드를 지원 하도록 함수인 json도 업데이트 해야 합니다.

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

이 HTTP 메서드는 웹 브라우저에서 허용 된 원본 목록을 협상 하는 데 사용 됩니다. 

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
