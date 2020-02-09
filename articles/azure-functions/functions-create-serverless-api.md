---
title: Azure Functions에서 HTTP 엔드포인트 사용자 지정
description: Azure Functions에서 HTTP 트리거 엔드포인트을 사용자 지정 하는 방법을 알아봅니다.
author: mattchenderson
ms.topic: conceptual
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 61b930eec1385b8c4054f9c202547a82e61e55e7
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769271"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Azure Functions에서 HTTP 엔드포인트 사용자 지정

이 문서에서는 Azure Functions를 사용 하 여 확장성이 뛰어난 Api를 빌드하는 방법을 알아봅니다. Azure Functions은 node.js, C#등을 비롯 한 다양 한 언어로 엔드포인트을 쉽게 작성할 수 있도록 하는 기본 제공 HTTP 트리거 및 바인딩 컬렉션과 함께 제공 됩니다. 이 문서에서는 API 디자인의 특정 작업을 처리 하도록 HTTP 트리거를 사용자 지정 합니다. 또한 Azure Functions 프록시와 통합하고 모의 API를 설정하여 API를 확장할 준비를 진행합니다. 이러한 모든 작업은 Functions의 서버리스 컴퓨팅 환경에서 수행되므로 리소스 확장 문제를 걱정할 필요가 없으며 API 논리에만 집중하면 됩니다.

## <a name="prerequisites"></a>필수 조건 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

결과 함수는이 문서의 나머지 부분에 사용 됩니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

Azure Portal을 엽니다. 이렇게 하려면 Azure 계정으로 [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.

## <a name="customize-your-http-function"></a>HTTP 함수 사용자 지정

기본적으로 HTTP 트리거 함수는 모든 HTTP 메서드를 허용하도록 구성됩니다. `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` 형식의 기본 URL도 있습니다. 빠른 시작을 진행한 경우 `<funcname>`이 "HttpTriggerJS1"과 같을 수 있습니다. 이 섹션에서는 대신 `/api/hello` 경로에 대한 GET 요청에만 응답하도록 함수를 수정할 것입니다. 

1. Azure Portal에서 해당 함수로 이동합니다. 왼쪽 탐색 영역에서 **통합**을 선택합니다.

    ![HTTP 함수 사용자 지정](./media/functions-create-serverless-api/customizing-http.png)

1. 테이블에 지정된 것처럼 HTTP 트리거 설정을 사용합니다.

    | 필드 | 샘플 값 | Description |
    |---|---|---|
    | 허용된 HTTP 메서드 | 선택된 메서드 | 이 함수를 호출하는 데 사용할 수 있는 HTTP 메서드 결정 |
    | 선택한 HTTP 메서드 | GET | 선택한 HTTP 메서드만 이 함수를 호출하는 데 사용할 수 있도록 허용 |
    | 경로 템플릿 | /hello | 이 함수를 호출하는 데 사용할 경로 결정 |
    | 권한 부여 수준 | 익명 | 선택 사항: 함수가 API 키 없이 액세스할 수 있게 됩니다 |

    > [!NOTE] 
    > `/api` 기본 경로 접두사는 전역 설정에 의해 처리되므로 경로 템플릿에 포함하지 않았습니다.

1. **저장**을 클릭합니다.

[Azure Functions HTTP 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)에서 HTTP 함수 사용자 지정에 대해 자세히 알아보세요.

### <a name="test-your-api"></a>API 테스트

다음에는 함수를 테스트하여 새 API 화면에서 작동하는 모습을 확인합니다.
1. 왼쪽 탐색 영역에서 함수 이름을 클릭하여 개발 페이지로 다시 이동합니다.
1. **함수 URL 가져오기**를 클릭하고 URL을 복사합니다. 이제 `/api/hello` 경로가 사용되는 것을 볼 수 있습니다.
1. 새 브라우저 탭 또는 기본 REST 클라이언트에 URL을 복사합니다. 브라우저는 기본적으로 GET을 사용합니다.
1. URL의 검색어 문자열에 매개 변수를 추가합니다(예: `/api/hello/?name=John`).
1. 'Enter' 키를 눌러 작동하는지 확인합니다. "*Hello John*"이라는 응답이 표시됩니다.
1. 다른 HTTP 메서드로 엔드포인트를 호출하여 함수가 실행되지 않는지 확인할 수도 있습니다. 이 경우 cURL, Postman 또는 Fiddler와 같은 REST 클라이언트를 사용해야 합니다.

## <a name="proxies-overview"></a>프록시 개요

다음 섹션에서는 프록시를 통해 API를 노출합니다. Azure Functions 프록시를 사용하면 요청을 다른 리소스로 전달할 수 있습니다. HTTP 트리거를 사용할 때처럼 HTTP 엔드포인트를 정의하지만, 해당 엔드포인트가 호출될 때 실행할 코드를 작성하지 않고 원격 구현에 대한 URL을 제공합니다. 이렇게 하면 여러 API 원본을 클라이언트가 쉽게 사용할 수 있는 단일 API 화면으로 작성할 수 있습니다. 이러한 방식은 API를 마이크로 서비스로 빌드하려는 경우에 특히 유용합니다.

프록시는 다음과 같은 HTTP 리소스를 가리킬 수 있습니다.
- Azure Function 
- [Azure App Service](https://docs.microsoft.com/azure/app-service/overview)의 API 앱
- [Linux의 App Service](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)에 있는 Docker 컨테이너
- 기타 호스트된 API

프록시에 대한 자세한 내용은 [Azure Functions 프록시 사용]을 참조하세요.

## <a name="create-your-first-proxy"></a>첫 번째 프록시 만들기

이 섹션에서는 전체 API에 대한 프런트 엔드로 사용되는 새 프록시를 만듭니다. 

### <a name="setting-up-the-frontend-environment"></a>프런트 엔드 환경 설정

[함수 앱 만들기](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) 단계를 반복하여 프록시를 만들 새 함수 앱을 만듭니다. 이 새로운 앱의 URL은 API에 대한 프런트 엔드로 사용되고 이전에 편집하던 함수 앱은 백 엔드로 사용됩니다.

1. 포털의 새 프런트 엔드 함수 앱으로 이동합니다.
1. **플랫폼 기능**을 선택하고 **애플리케이션 설정**을 선택합니다.
1. 키/값 쌍이 저장된 **애플리케이션 설정**으로 스크롤하고 "HELLO_HOST" 키로 새 설정을 만듭니다. 해당 값을 백 엔드 함수 앱의 호스트(예: `<YourBackendApp>.azurewebsites.net`)로 설정합니다. 이것은 HTTP 함수를 테스트할 때 이전에 복사한 URL의 일부입니다. 나중에 구성에서 이 설정을 참조할 수 있습니다.

    > [!NOTE] 
    > 프록시에 대한 하드 코드된 환경 종속성을 방지하려면 호스트 구성에 대해 앱 설정이 권장됩니다. 앱 설정을 사용할 경우 환경 간에 프록시 구성을 이동할 수 있고 환경 관련 앱 설정이 적용됩니다.

1. **저장**을 클릭합니다.

### <a name="creating-a-proxy-on-the-frontend"></a>프런트 엔드에 프록시 만들기

1. 포털의 프런트 엔드 함수 앱으로 다시 이동합니다.
1. 왼쪽 탐색 영역에서 "프록시" 옆에 있는 '+'(더하기) 기호를 클릭합니다.
    ![프록시 만들기](./media/functions-create-serverless-api/creating-proxy.png)
1. 표에 지정된 것처럼 프록시 설정을 사용합니다. 

    | 필드 | 샘플 값 | Description |
    |---|---|---|
    | 이름 | HelloProxy | 관리에 대해서만 사용되는 이름 |
    | 경로 템플릿 | /api/remotehello | 이 프록시를 호출하는 데 사용할 경로 결정 |
    | 백 엔드 URL | https://%HELLO_HOST%/api/hello | 요청을 프록시 처리할 엔드포인트를 지정합니다. |
    
1. 프록시는 `/api` 기본 경로 접두사를 제공하지 않으며 경로 템플릿에 포함되어야 합니다.
1. `%HELLO_HOST%` 구문은 이전에 만든 앱 설정을 참조합니다. 확인된 URL은 원래 함수를 가리킵니다.
1. **만들기**를 클릭합니다.
1. 프록시 URL을 복사하고 브라우저 또는 자주 사용하는 HTTP 클라이언트에서 테스트하여 새 프록시를 시험해볼 수 있습니다.
    1. 익명 함수의 경우 다음을 사용합니다.
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`
    1. 권한이 부여된 함수의 경우 다음을 사용합니다.
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>모의 API 만들기

다음으로 프록시를 사용하여 솔루션에 대한 모의 API 만듭니다. 이를 통해 백 엔드를 완전히 구현하지 않고도 클라이언트 개발을 진행할 수 있습니다. 개발 후반부에 이 논리를 지원하는 새 함수 앱을 만들고 프록시를 해당 앱으로 리디렉션할 수 있습니다.

이 모의 API를 만들려면 이번에는 [App Service 편집기](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)를 사용하여 새 프록시를 만듭니다. 시작하려면 포털의 함수 앱으로 이동합니다. **플랫폼 기능**을 선택하고 **개발 도구**에서 **App Service 편집기**를 찾습니다. 이를 클릭하면 새 탭에서 App Service 편집기가 열립니다.

왼쪽 탐색 영역에서 `proxies.json`을 선택합니다. 모든 사용자 프록시에 대한 구성이 저장되는 파일입니다. [함수 배포 방법](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) 중 하나를 사용하는 경우 이 파일이 소스 제어에 유지됩니다. 이 파일에 대한 자세한 내용은 [프록시 고급 구성](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration)을 참조하세요.

지금까지 작업을 잘 진행했으면 proxies.json이 다음과 같이 표시됩니다.

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

다음으로 모의 API를 추가합니다. proxies.json 파일을 다음으로 바꿉니다.

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

이렇게 하면 backendUri 속성을 제외한 새 프록시 "GetUserByName"이 추가됩니다. 다른 리소스를 호출하지 않고 응답 재정의를 사용하여 프록시의 기본 응답을 수정합니다. 요청 및 응답 재정의를 백 엔드 URL과 함께 사용할 수도 있습니다. 이는 헤더, 쿼리 매개 변수 등을 수정 해야 할 수 있는 레거시 시스템에 프록시를 사용할 때 특히 유용 합니다. 요청 및 응답 재정의에 대해 자세히 알아보려면 [프록시의 요청 및 응답 수정](https://docs.microsoft.com/azure/azure-functions/functions-proxies)을 참조 하세요.

브라우저 또는 자주 사용하는 REST 클라이언트를 통해 `<YourProxyApp>.azurewebsites.net/api/users/{username}` 엔드포인트를 호출하여 모의 API를 테스트합니다. _{username}_ 을 사용자 이름을 나타내는 문자열 값으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Functions에서 API를 빌드하고 사용자 지정 하는 방법을 배웠습니다. 또한 모의 API를 비롯한 여러 API를 하나의 통합 API 화면에 표시하는 방법도 알아보았습니다. 이러한 기법을 사용하여 복잡성에 관계없이 Azure Functions에서 제공하는 서버리스 컴퓨팅 모델에서 실행되는 API를 빌드할 수 있습니다.

다음 참조는 API를 추가로 개발하는 경우 유용할 수 있습니다.

- [Azure Functions HTTP 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Azure Functions 프록시 사용]
- [Azure Functions API 문서화(미리 보기)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Azure Functions 프록시 사용]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
