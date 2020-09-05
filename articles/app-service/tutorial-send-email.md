---
title: '자습서: Logic Apps를 사용하여 이메일 보내기'
description: App Service 앱에서 비즈니스 프로세스를 호출하는 방법을 알아봅니다. 이메일, 트윗, Facebook 게시물을 보내고 메일 그룹에 추가하는 등의 작업을 수행합니다.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: ddd57bfe653766d133824f42098d9ad9adb1e56b
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962199"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>자습서: App Service에서 이메일 보내기 및 다른 비즈니스 프로세스 호출

이 자습서에서는 App Service 앱을 비즈니스 프로세스와 통합하는 방법에 대해 알아봅니다. 이는 다음과 같은 웹앱 시나리오에서 일반적입니다.

- 트랜잭션에 대한 확인 이메일 보내기
- Facebook 그룹에 사용자 추가
- SAP, Salesforce 등과 같은 타사 시스템에 연결
- 표준 B2B 메시지 교환

이 자습서에서는 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 App Service 앱에서 Gmail을 통해 이메일을 보냅니다. 언어 프레임워크에서 제공하는 SMTP 구성과 같이 웹앱에서 이메일을 보내는 다른 방법이 있습니다. 그러나 Logic Apps는 복잡성을 코드에 추가하지 않고 App Service 앱에 훨씬 더 많은 기능을 제공합니다. Logic Apps는 가장 인기 있는 비즈니스 통합을 위한 간단한 구성 인터페이스를 제공하고, 앱에서 언제든지 HTTP 요청을 통해 이를 호출할 수 있습니다.

## <a name="prerequisite"></a>필수 요소

선택한 언어 프레임워크가 포함된 앱을 App Service에 배포합니다. 자습서에 따라 샘플 앱을 배포하려면 아래를 참조하세요.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[자습서: Azure App Service에서 ASP.NET Core 및 SQL Database 앱 빌드](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.JS](#tab/node)

[자습서: Azure에서 Node.js 및 MongoDB 앱 빌드](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[자습서: Azure에서 PHP 및 MySQL 앱 빌드](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[자습서: Azure App Service에서 PostgreSQL을 사용하여 Python(Django) 웹앱 실행](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Azure App Service on Linux에서 Ruby 및 Postgres 앱 빌드](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>논리 앱 만들기

1. [Azure Portal](https://portal.azure.com)에서 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)의 지침에 따라 빈 논리 앱을 만듭니다. **Logic Apps 디자이너**가 표시되면 이 자습서로 돌아갑니다.
1. Logic Apps 디자이너의 시작 페이지에서 **일반적인 트리거로 시작** 아래의 **HTTP 요청을 수신한 경우**를 선택합니다.

    ![H T T P 요청 수신 시 강조 표시되는 Logic Apps 디자이너의 시작 페이지를 보여주는 스크린샷.](./media/tutorial-send-email/receive-http-request.png)
1. **HTTP 요청을 수신한 경우** 대화 상자에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

    ![H T T P 요청 대화 상자와 샘플 페이로드를 사용하여 선택한 스키마 옵션을 생성하는 경우를 보여주는 스크린샷. ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. 다음 샘플 JSON을 텍스트 상자에 복사하고, **완료**를 선택합니다.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    이제 원하는 요청 데이터에 대한 스키마가 생성됩니다. 실제로는 애플리케이션 코드에서 생성하는 실제 요청 데이터를 캡처하여 Azure에서 JSON 스키마를 생성하도록 할 수 있습니다. 
1. Logic Apps 디자이너의 위쪽에서 **저장**을 선택합니다. 

    이제 HTTP 요청 트리거의 URL을 볼 수 있습니다. 나중에 사용할 수 있도록 복사하려면 복사 아이콘을 선택합니다.

    ![H T T P 요청 트리거의 U R L을 복사하는 복사 아이콘을 강조 표시하는 스크린샷.](./media/tutorial-send-email/http-request-url.png)

    이 HTTP 요청 정의는 이 논리 앱에서 수행하려는 작업(Gmail 등)에 대한 트리거입니다. 나중에 App Service 앱에서 이 URL을 호출합니다. 요청 트리거에 대한 자세한 내용은 [HTTP 요청/응답 참조](../connectors/connectors-native-reqres.md)를 참조하세요.

1. 디자이너 아래쪽에서 **새 단계**를 클릭하고, 작업 검색 상자에서 **Gmail**을 입력하고, **이메일 보내기(V2)** 를 찾아서 선택합니다.
    
    > [!TIP]
    > SendGrid, MailChimp, Office 365 및 SalesForce와 같은 다른 유형의 통합을 검색할 수 있습니다. 자세한 내용은 [Logic Apps 설명서](../logic-apps/index.yml)를 참조하세요.
1. **Gmail** 대화 상자에서 **로그인**을 선택하고, 이메일을 보낼 Gmail 계정에 로그인합니다.

    ![이메일을 보내려는 Gmail 계정에 로그인하는 데 사용하는 Gmail 대화 상자를 보여주는 스크린샷.](./media/tutorial-send-email/gmail-sign-in.png)

1. 로그인하면 **받는 사람** 텍스트 상자를 클릭합니다. 그러면 동적 콘텐츠 대화 상자가 자동으로 열립니다.

1. **HTTP 요청을 수신한 경우** 작업 옆에 있는 **자세히 보기**를 선택합니다.

    ![H T T P 요청이 수신되는 경우 옆에 있는 추가 정보 보기 단추를 보여주는 스크린샷.](./media/tutorial-send-email/expand-dynamic-content.png)

    이제 이전에 사용한 샘플 JSON 데이터의 세 가지 속성을 볼 수 있습니다. 이 단계에서는 HTTP 요청의 이러한 속성을 사용하여 이메일을 구성합니다.
1. **받는 사람** 필드의 값을 선택하고 있으므로 **이메일**을 선택합니다. 원하는 경우 **동적 콘텐츠 추가**를 클릭하여 동적 콘텐츠 대화 상자를 해제합니다.

    ![이메일 옵션 및 동적 경합 추가 옵션이 강조 표시된 스크린샷.](./media/tutorial-send-email/hide-dynamic-content.png)

1. **새 매개 변수 추가** 드롭다운에서 **제목** 및 **본문**을 선택합니다.

1. **제목** 텍스트 상자를 클릭하고, 동일한 방식으로 **작업**을 선택합니다. 커서가 **제목** 상자에 그대로 있는 상태에서 *만든 날짜*를 입력합니다. 

1. **본문**을 클릭하고, 동일한 방식으로 **기한**을 선택합니다. 커서를 **기한**의 왼쪽으로 이동하고, *이 작업 항목의 기한*을 입력합니다.

    > [!TIP]
    > 이메일 본문에서 HTML 콘텐츠를 직접 편집하려면 Logic Apps 디자이너 창의 위쪽에서 **코드 보기**를 선택합니다. 단, 동적 콘텐츠 코드(예: `@{triggerBody()?['due']}`)는 유지해야 합니다.
    >
    > ![이메일 본문에서 직접 H T M L 콘텐츠를 볼 수 있는 코드 보기를 보여주는 스크린샷.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. 다음으로, 비동기 HTTP 응답을 HTTP 트리거에 추가합니다. HTTP 트리거와 Gmail 작업 사이에 있는 **+** 기호를 클릭하고, **병렬 분기 추가**를 선택합니다.

    ![\+ 기호 및 병렬 분기 추가 옵션이 강조 표시된 스크린샷.](./media/tutorial-send-email/add-http-response.png)

1. 검색 상자에서 **응답**을 검색한 다음, **응답** 작업을 선택합니다.

    ![강조 표시된 검색 창과 응답 작업을 보여주는 스크린샷.](./media/tutorial-send-email/choose-response-action.png)

    기본적으로 응답 작업은 HTTP 200을 보냅니다. 이 자습서에는 이 작업으로 충분합니다. 자세한 내용은 [HTTP 요청/응답 참조](../connectors/connectors-native-reqres.md)를 참조하세요.

1. Logic Apps 디자이너의 위쪽에서 **저장**을 다시 선택합니다. 

## <a name="add-http-request-code-to-app"></a>앱에 HTTP 요청 코드 추가

이전에 지정한 HTTP 요청 트리거의 URL을 복사했는지 확인합니다. 중요한 정보가 포함되어 있으므로 코드에 직접 배치하지 않는 것이 좋습니다. App Service를 사용하면 앱 설정을 사용하여 환경 변수로 대신 참조할 수 있습니다. 

[Cloud Shell](https://shell.azure.com)에서 다음 명령을 사용하여 앱 설정을 만듭니다( *\<app-name>* , *\<resource-group-name>* 및 *\<logic-app-url>* 대체).

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

코드에서 다음 구성을 사용하여 언어 프레임워크에서 사용할 수 있는 HTTP 클라이언트 언어를 사용하여 URL에 대한 표준 HTTP post를 수행합니다.

- 요청 본문에는 논리 앱에 제공한 것과 동일한 JSON 형식이 포함됩니다.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- 요청에는 `Content-Type: application/json` 머리글이 포함됩니다. 
- 성능을 최적화하려면 가능한 경우 요청을 비동기적으로 보냅니다.

아래에서 기본 설정 언어/프레임워크 탭을 클릭하여 예제를 살펴보세요.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

ASP.NET에서는 [System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient) 클래스를 사용하여 HTTP post를 보낼 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

[자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)의 샘플 앱에서 테스트하는 경우 `Todo` 항목이 추가된 후 [Create 작업](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)에서 이메일 확인을 보내는 데 이 코드를 사용할 수 있습니다. 위의 비동기 코드를 사용하려면 Create 작업을 비동기로 변환합니다.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

ASP.NET Core에서는 [System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient) 클래스를 사용하여 HTTP post를 보낼 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> 이 코드는 데모의 편의를 위해 작성되었습니다. 실제로는 각 요청에 대해 `HttpClient` 개체를 인스턴스화하지 않습니다. [IHttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현의](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 지침을 따르세요.

[자습서: Azure App Service에서 ASP.NET Core 및 SQL Database 앱 빌드](tutorial-dotnetcore-sqldb-app.md)의 샘플 앱에서 테스트하는 경우 `Todo` 항목이 추가된 후 [Create 작업](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)에서 이메일 확인을 보내는 데 이 코드를 사용할 수 있습니다.

### <a name="nodejs"></a>[Node.JS](#tab/node)

Node.js에서는 [axios](https://www.npmjs.com/package/axios)와 같은 npm 패키지를 사용하여 HTTP post를 쉽게 보낼 수 있습니다. 예를 들면 다음과 같습니다.

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

[자습서: Azure에서 Node.js 및 MongoDB 앱 빌드](tutorial-nodejs-mongodb-app.md)의 샘플 앱에서 테스트하는 경우 [문서가 성공적으로 저장](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)된 후 [create 함수](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)에서 이메일 확인을 보내는 데 이 코드를 사용할 수 있습니다.

### <a name="php"></a>[PHP](#tab/php)

PHP에서는 [Guzzle](http://docs.guzzlephp.org/en/stable/index.html)을 사용하여 HTTP post를 쉽게 보낼 수 있습니다. 예를 들면 다음과 같습니다.

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

[자습서: Azure에서 PHP 및 MySQL 앱 빌드](tutorial-php-mysql-app.md)의 샘플 앱에서 테스트하는 경우 return 문 바로 앞의 [Route::post 함수](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)에서 이메일 확인을 보내는 데 이 코드를 사용할 수 있습니다.

### <a name="python"></a>[Python](#tab/python)

Python에서는 [requests](https://pypi.org/project/requests/)를 사용하여 HTTP post를 쉽게 보낼 수 있습니다. 예를 들면 다음과 같습니다.

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

[자습서: Azure App Service에서 PostgreSQL을 사용하여 Python(Django) 웹앱 실행](tutorial-python-postgresql-app.md)의 샘플 앱에서 테스트하는 경우 return 문 바로 앞의 [Route::post 함수](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)에서 이메일 확인을 보내는 데 이 코드를 사용할 수 있습니다.

### <a name="ruby"></a>[Ruby](#tab/ruby)

Ruby에서는 [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient)를 사용하여 HTTP post를 쉽게 보낼 수 있습니다. 다음은 그 예입니다.

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

[자습서: Azure App Service on Linux에서 Ruby 및 Postgres 앱 빌드](tutorial-ruby-postgres-app.md)의 샘플 앱에서 테스트하는 경우 [@task.save가 성공하면](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30) [create](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) 작업에서 이메일 확인을 보내는 데 이 코드를 사용할 수 있습니다.

---

## <a name="more-resources"></a>추가 리소스

[자습서: Azure App Service에서 CORS를 통해 RESTful API 호스팅](app-service-web-tutorial-rest-api.md)  
[Logic Apps에 대한 HTTP 요청/응답 참조](../connectors/connectors-native-reqres.md)  
[빠른 시작: Azure Logic Apps를 사용하여 첫 번째 워크플로 만들기 - Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)