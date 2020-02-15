---
title: Azure Logic Apps에서 Azure Functions를 추가 하 고 호출 합니다.
description: Azure Logic Apps의 자동화 된 작업 및 워크플로에서 Azure Functions에서 사용자 지정 코드를 호출 하 고 실행 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 68975f21ab810398da969384db4d3bddd22f1bd9
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116911"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Azure Logic Apps에서 Azure 함수를 호출 합니다.

논리 앱에서 특정 작업을 수행 하는 코드를 실행 하려는 경우 [Azure Functions](../azure-functions/functions-overview.md)를 사용 하 여 고유한 함수를 만들 수 있습니다. 이 서비스는 node.js, C#및 F# 함수를 만드는 데 도움이 되므로 코드를 실행 하기 위해 전체 응용 프로그램 또는 인프라를 빌드할 필요가 없습니다. [Azure Functions에서 논리 앱을 호출](#call-logic-app)할 수도 있습니다. Azure Functions는 클라우드에서 서버리스 컴퓨팅을 제공하며, 다음 예제와 같은 작업을 수행하는 데 유용합니다.

* Node.js 또는 C#의 함수를 사용하여 논리 앱의 동작을 확장합니다.
* 논리 앱 워크플로에서 계산을 수행합니다.
* 논리 앱에서 고급 서식 지정 또는 컴퓨팅 필드를 적용합니다.

Azure 함수를 만들지 않고 코드 조각을 실행 하려면 [인라인 코드를 추가 하 고 실행](../logic-apps/logic-apps-add-run-inline-code.md)하는 방법을 알아보세요.

> [!NOTE]
> 현재 Logic Apps와 Azure Functions 간의 통합은 슬롯을 사용 하는 경우 작동 하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Azure 함수를 사용 하 여 azure 함수를 위한 컨테이너인 Azure 함수 앱입니다. 함수 앱이 없는 경우 [함수 앱을 먼저 만듭니다](../azure-functions/functions-create-first-azure-function.md). 그런 다음 Azure Portal의 논리 앱 외부에서 또는 논리 앱 디자이너의 [논리 앱 내에서](#create-function-designer) 함수를 만들 수 있습니다.

* 논리 앱을 사용할 때 동일한 요구 사항이 기존 또는 새로운 함수 앱 및 함수에 적용 됩니다.

  * 함수 앱 및 논리 앱은 동일한 Azure 구독을 사용 해야 합니다.

  * 새 함수 앱은 런타임 스택으로 .NET 또는 JavaScript를 사용 해야 합니다. 기존 함수 앱에 새 함수를 추가 하는 경우 C# 또는 JavaScript를 선택할 수 있습니다.

  * 함수는 **HTTP 트리거** 템플릿을 사용 합니다.

    HTTP 트리거 템플릿은 논리 앱의 `application/json` 형식을 갖는 콘텐츠를 수락할 수 있습니다. 논리 앱에 Azure 함수를 추가 하는 경우 논리 앱 디자이너는 Azure 구독 내에서이 템플릿에서 만든 사용자 지정 함수를 표시 합니다.

  * [Openapi 정의](../azure-functions/functions-openapi-definition.md) (이전의 [Swagger 파일](https://swagger.io/))를 정의 하지 않은 경우 함수는 사용자 지정 경로를 사용 하지 않습니다.

  * 함수에 대 한 OpenAPI 정의가 있는 경우 Logic Apps 디자이너에서 함수 매개 변수를 사용할 때 보다 풍부한 환경을 제공 합니다. 논리 앱이 OpenAPI 정의가 포함된 함수를 찾아 액세스할 수 있도록 [먼저 다음 단계를 수행하여 함수 앱을 설정](#function-swagger)합니다.

* 논리 앱의 첫 번째 단계인 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 포함하여 함수를 추가하려는 논리 앱

  함수를 실행 하는 작업을 추가 하기 전에 논리 앱은 트리거로 시작 해야 합니다. 논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>OpenAPI 설명이 있는 함수 찾기

Logic Apps 디자이너에서 함수 매개 변수를 사용할 때 보다 풍부한 환경을 제공 하기 위해 함수에 대해 이전에 [Swagger 파일](https://swagger.io/)이라고 하는 [openapi 정의를 생성](../azure-functions/functions-openapi-definition.md)합니다. 논리 앱이 Swagger 설명이 포함된 함수를 찾아 사용할 수 있도록 함수 앱을 설정하려면 다음 단계를 수행합니다.

1. 함수 앱이 적극적으로 실행 되 고 있는지 확인 합니다.

1. 함수 앱에서 다음 단계를 수행 하 여 모든 원본을 허용 하도록 [CORS (크로스-원본 자원 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 를 설정 합니다.

   1. **함수 앱** 목록에서 함수 앱을 선택 합니다. 오른쪽 창에서 **플랫폼 기능** > **CORS**를 선택 합니다.

      ![함수 앱 > "플랫폼 기능" > "CORS" 선택](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. **CORS**에서 별표 ( **`*`** ) 와일드 카드 문자를 추가 하지만 목록에서 다른 모든 원본을 제거 하 고 **저장**을 선택 합니다.

      !["CORS*를 와일드카드 문자 "*"로 설정](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>HTTP 요청 내에서 속성 값에 액세스

웹후크 함수는 HTTP 요청을 입력으로 수락하고 해당 요청을 다른 함수에 전달할 수 있습니다. 예를 들어, Logic Apps에는 [DateTime 값을 변환하는 함수](../logic-apps/workflow-definition-language-functions-reference.md)가 있지만 이 기본 샘플 JavaScript 함수는 함수에 전달되는 요청 개체 내의 속성에 액세스하고 해당 속성 값에 대해 작업을 수행하는 방법을 보여 줍니다. 개체 내의 속성에 액세스하기 위해 이 예제에서는 [점(.) 연산자](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)를 사용합니다.

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

이 함수 내에서 수행되는 작업은 다음과 같습니다.

1. 이 함수는 `data` 변수를 만들고 `body` 개체 내의 `request` 개체를 해당 변수에 할당합니다. 이 함수는 점(.) 연산자를 사용하여 `body` 개체 내의 `request` 개체를 참조합니다.

   ```javascript
   var data = request.body;
   ```

1. 이 함수는 이제 `date` 변수를 통해 `data` 속성에 액세스하고, `ToDateString()` 함수를 호출하여 해당 속성 값을 DateTime 형식에서 DateString 형식으로 변환할 수 있습니다. 또한 이 함수는 다음과 같이 함수의 응답에서 `body` 속성을 통해 결과를 반환합니다.

   ```javascript
   body: data.date.ToDateString();
   ```

지금까지 Azure 함수를 만들었으므로 [논리 앱에 함수를 추가](#add-function-logic-app)하는 방법에 대한 단계를 따르세요.

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>논리 앱 내에서 함수 만들기

논리 앱 디자이너를 사용 하 여 논리 앱 내에서 시작 하는 Azure 함수를 만들려면 먼저 함수의 컨테이너인 Azure 함수 앱이 있어야 합니다. 함수 앱이 없는 경우 함수 앱을 먼저 만듭니다. [Azure Portal에서 첫 번째 함수 만들기](../azure-functions/functions-create-first-azure-function.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 함수를 만들고 추가하려면 시나리오에 적용되는 단계를 따릅니다.

   * 논리 앱 워크플로의 마지막 단계에서 **새 단계**를 선택 합니다.

   * 논리 앱 워크플로의 기존 단계 사이에서 화살표 위로 마우스를 이동 하 고 더하기 (+) 기호를 선택한 다음 **작업 추가**를 선택 합니다.

1. 검색 상자에서 "azure functions"를 필터로 입력합니다. 작업 목록에서 **Azure 함수 선택** 작업을 선택 합니다. 예를 들면 다음과 같습니다.

   !["Azure 함수" 찾기](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 함수 앱 목록에서 함수 앱을 선택합니다. 작업 목록이 열리면 **새 함수 만들기** 작업을 선택 합니다.

   ![함수 앱 선택](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. 함수 정의 편집기에서 함수를 정의합니다.

   1. **함수 이름** 상자에 함수에 대한 이름을 입력합니다.

   1. **코드** 상자에서 함수 실행이 완료 된 후에 논리 앱에 반환 하려는 응답과 페이로드를 포함 하 여 함수 템플릿에 코드를 추가 합니다. 완료되면 **만들기**를 선택합니다.

   다음은 그 예입니다.

   ![함수 정의](./media/logic-apps-azure-functions/add-code-function-definition.png)

   템플릿의 코드에서 *`context` 개체*는 논리 앱이 이후 단계에서 **요청 본문** 필드를 통해 전송하는 메시지를 참조합니다. 함수 내에서 `context` 개체의 속성에 액세스하려면 다음 구문을 사용합니다.

   `context.body.<property-name>`

   예를 들어, `content` 개체 내의 `context` 속성을 참조하려면 다음 구문을 사용합니다.

   `context.body.content`

   또한 템플릿 코드에는 함수가 해당 값에 대해 작업을 수행할 수 있도록 `input` 매개 변수의 값을 저장하는 `data` 변수도 포함되어 있습니다. JavaScript 함수 내에서 `data` 변수는 `context.body`를 빠르게 수행하는 방법이기도 합니다.

   > [!NOTE]
   > 여기서 `body` 속성은 `context` 개체에 적용되며, 함수에도 전달될 수 있는 작업의 출력에서 가져온 **Body** 토큰과 다릅니다.

1. **요청 본문** 상자에 JSON (JavaScript Notation) 개체로 형식이 지정된 함수 입력을 제공합니다.

   이 입력은 *컨텍스트 개체*이거나, 논리 앱이 함수에 보내는 메시지입니다. **요청 본문** 필드를 클릭하면 이전 단계의 출력에 대한 토큰을 선택할 수 있도록 동적 콘텐츠 목록이 나타납니다. 이 예제에서는 컨텍스트 페이로드에 전자 메일 트리거의 **from** 토큰 값을 가진 `content` 라는 속성이 포함 되도록 지정 합니다.

   !["요청 본문" 예제 - 컨텍스트 개체 페이로드](./media/logic-apps-azure-functions/function-request-body-example.png)

   여기서 컨텍스트 개체는 문자열로 캐스팅되지 않으므로 개체의 콘텐츠는 JSON 페이로드에 직접 추가됩니다. 그러나 컨텍스트 개체가 문자열, JSON 개체 또는 JSON 배열을 전달하는 JSON 토큰이 아닌 경우 오류가 발생합니다. 따라서이 예제에서 **받은 시간** 토큰을 대신 사용 하는 경우에는 큰따옴표를 추가 하 여 컨텍스트 개체를 문자열로 캐스팅할 수 있습니다.

   ![개체를 문자열로 캐스팅](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 사용할 메서드, 요청 헤더 또는 쿼리 매개 변수, 인증 등의 기타 세부 정보를 지정 하려면 **새 매개 변수 추가** 목록을 열고 원하는 옵션을 선택 합니다. 인증의 경우 선택한 함수에 따라 옵션이 달라 집니다. [Azure 함수에 인증 사용을](#enable-authentication-functions)참조 하세요.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>논리 앱에 기존 함수 추가

논리 앱에서 기존 Azure 함수를 호출하려면 Logic Apps 디자이너에서 다른 작업처럼 Azure 함수를 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 함수를 추가 하려는 단계 아래에서 **새 단계**를 선택 합니다.

1. **작업 선택**아래의 검색 상자에 "azure 함수"를 필터로 입력 합니다. 작업 목록에서 **Azure 함수 선택** 작업을 선택 합니다.

   !["Azure 함수" 찾기](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 함수 앱 목록에서 함수 앱을 선택합니다. 함수 목록이 표시되면 함수를 선택합니다.

   ![함수 앱 및 Azure 함수 선택](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   API 정의 (Swagger 설명)를 사용 하 고 [논리 앱이 이러한 기능을 찾아서 액세스할 수 있도록 설정](#function-swagger)된 함수의 경우 **Swagger 작업**을 선택할 수 있습니다.

   ![함수 앱, "Swagger 작업" 및 Azure 함수를 선택 합니다.](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. **요청 본문** 상자에 JSON (JavaScript Notation) 개체로 형식이 지정된 함수 입력을 제공합니다.

   이 입력은 *컨텍스트 개체*이거나, 논리 앱이 함수에 보내는 메시지입니다. **요청 본문** 필드를 클릭 하면 이전 단계에서 출력에 대 한 토큰을 선택할 수 있도록 동적 콘텐츠 목록이 표시 됩니다. 이 예제에서는 컨텍스트 페이로드에 전자 메일 트리거의 **from** 토큰 값을 가진 `content` 라는 속성이 포함 되도록 지정 합니다.

   !["요청 본문" 예제 - 컨텍스트 개체 페이로드](./media/logic-apps-azure-functions/function-request-body-example.png)

   여기서 컨텍스트 개체는 문자열로 캐스팅되지 않으므로 개체의 콘텐츠는 JSON 페이로드에 직접 추가됩니다. 그러나 컨텍스트 개체가 문자열, JSON 개체 또는 JSON 배열을 전달하는 JSON 토큰이 아닌 경우 오류가 발생합니다. 따라서 이 예제에서 **Received Time** 토큰을 대신 사용하면 큰따옴표를 추가하여 컨텍스트 개체를 문자열로 캐스팅할 수 있습니다.

   ![개체를 문자열로 캐스팅](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 사용할 방법, 요청 헤더, 쿼리 매개 변수 또는 인증과 같은 기타 세부 정보를 지정 하려면 **새 매개 변수 추가** 목록을 열고 원하는 옵션을 선택 합니다. 인증의 경우 선택한 함수에 따라 옵션이 달라 집니다. [Azure 함수에서 인증 사용을](#enable-authentication-functions)참조 하세요.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Azure 함수에서 논리 앱 호출

Azure 함수 내에서 논리 앱을 트리거하려면 논리 앱이 호출 가능한 엔드포인트를 제공하는 트리거로 시작해야 합니다. 예를 들어, **HTTP**, **Request**, **Azure Queues** 또는 **Event Grid** 트리거로 논리 앱을 시작할 수 있습니다. 함수 내에서 트리거의 URL에 HTTP POST 요청을 전송하고 해당 논리 앱에서 처리할 페이로드를 포함합니다. 자세한 내용은 [논리 앱 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요.

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Azure 함수에 대 한 인증 사용

로그인 하 고 자격 증명이 나 암호를 제공 하지 않고 다른 Azure Active Directory (Azure AD) 테 넌 트의 리소스에 대 한 액세스를 인증 하기 위해 논리 앱은 [관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md) (이전의 관리 서비스 ID 또는 MSI)를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다. [AZURE AD 인증에 대 한 관리 되는 id를 지 원하는 azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)에 대해 자세히 알아보세요.

시스템 할당 id 또는 수동으로 생성 된 사용자 할당 id를 사용 하도록 논리 앱을 설정 하는 경우 논리 앱의 Azure 함수는 인증에 동일한 id를 사용할 수도 있습니다. 논리 앱의 Azure 기능에 대 한 인증 지원에 대 한 자세한 내용은 [아웃 바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조 하세요.

함수를 사용 하 여 관리 되는 id를 설정 하 고 사용 하려면 다음 단계를 수행 합니다.

1. 논리 앱에서 관리 id를 사용 하도록 설정 하 고 대상 리소스에 대 한 id의 액세스를 설정 합니다. [Azure Logic Apps에서 관리 되는 id를 사용 하 여 Azure 리소스에 대 한 액세스 인증을](../logic-apps/create-managed-service-identity.md)참조 하세요.

1. 다음 단계를 수행 하 여 Azure 함수 및 함수 앱에서 인증을 사용 하도록 설정 합니다.

   * [함수에서 익명 인증 설정](#set-authentication-function-app)
   * [함수 앱에서 Azure AD 인증 설정](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>함수에서 익명 인증 설정

Azure 함수에서 논리 앱의 관리 되는 id를 사용 하려면 함수의 인증 수준을 익명으로 설정 해야 합니다. 그렇지 않으면 논리 앱이 "BadRequest" 오류를 throw 합니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱을 찾아 선택 합니다. 이러한 단계에서는 예제 함수 앱으로 "FabrikamFunctionApp"를 사용 합니다.

1. 함수 앱 창에서 **플랫폼 기능**을 선택 합니다. **개발 도구**아래에서 **고급 도구 (Kudu)** 를 선택 합니다.

   ![Kudu에 대 한 고급 도구 열기](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Kudu 웹 사이트의 제목 표시줄에 있는 **디버그 콘솔** 메뉴에서 **CMD**를 선택 합니다.

   ![디버그 콘솔 메뉴에서 "CMD" 옵션을 선택 합니다.](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. 다음 페이지가 표시 되 면 폴더 목록에서 **사이트** > **wwwroot** * > 를*선택 합니다. 이러한 단계에서는 예제 함수로 "FabrikamAzureFunction"를 사용 합니다.

   !["Site" > "wwwroot"를 선택 하 > 함수를 선택 합니다.](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. 편집할 `function.json` 파일을 엽니다.

   !["함수 json" 파일에 대 한 편집을 클릭 합니다.](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. `bindings` 개체에서 `authLevel` 속성이 있는지 여부를 확인 합니다. 속성이 있으면 속성 값을 `anonymous`설정 합니다. 그렇지 않으면 해당 속성을 추가 하 고 값을 설정 합니다.

   !["AuthLevel" 속성을 추가 하 고 "anonymous"로 설정 합니다.](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. 완료 되 면 설정을 저장 하 고 다음 섹션을 계속 진행 합니다.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>함수 앱에 대 한 Azure AD 인증 설정

이 작업을 시작 하기 전에 나중에 사용할 수 있도록이 값을 찾아 둡니다.

* 논리 앱을 나타내는 시스템 할당 id에 대해 생성 된 개체 ID입니다.

  * 이 개체 ID를 생성 하려면 [논리 앱의 시스템 할당 id를 사용 하도록 설정](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)합니다.

  * 그렇지 않고이 개체 ID를 찾으려면 논리 앱 디자이너에서 논리 앱을 엽니다. 논리 앱 메뉴의 **설정**에서 **id** > **시스템이 할당 됨**을 선택 합니다.

* Azure Active Directory (Azure AD)에서 테 넌 트의 디렉터리 ID

  테 넌 트의 디렉터리 ID를 가져오려면 [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) Powershell 명령을 실행할 수 있습니다. 또는 Azure Portal에서 다음 단계를 수행 합니다.

  1. [Azure Portal](https://portal.azure.com)에서 함수 앱을 찾아 선택 합니다.

  1. Azure AD 테 넌 트를 찾아 선택 합니다. 이러한 단계에서는 "Fabrikam"을 예제 테 넌 트로 사용 합니다.

  1. 테 넌 트의 메뉴에 있는 **관리**에서 **속성**을 선택 합니다.

  1. 예를 들어 테 넌 트의 디렉터리 ID를 복사 하 고 나중에 사용 하기 위해 해당 ID를 저장 합니다.

     ![Azure AD 테 넌 트의 디렉터리 ID 찾기 및 복사](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* 액세스 하려는 대상 리소스의 리소스 ID입니다.

  * 이러한 리소스 Id를 찾으려면 [AZURE AD를 지 원하는 azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 검토 합니다.

  > [!IMPORTANT]
  > 이 리소스 ID는 필요한 후행 슬래시를 포함 하 여 Azure AD에 필요한 값과 정확 하 게 일치 해야 합니다.

  이 리소스 ID는 [시스템 할당 id를 사용 하도록 함수 작업을 설정할](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)때 나중에 **대상** 속성에서 사용 하는 것과 동일한 값 이기도 합니다.

이제 함수 앱에 대 한 Azure AD 인증을 설정할 준비가 되었습니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱을 찾아 선택 합니다.

1. 함수 앱 창에서 **플랫폼 기능**을 선택 합니다. **네트워킹**에서 **인증/권한 부여**를 선택 합니다.

   ![인증 및 권한 부여 설정 보기](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. **App Service 인증** 설정을 **켜기**로 변경 합니다. **요청이 인증 되지 않은 경우 수행할 작업** 목록에서 **Azure Active Directory로 로그인**을 선택 합니다. **인증 공급자**에서 **Azure Active Directory**를 선택합니다.

   ![Azure AD를 사용 하 여 인증 설정](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. **Azure Active Directory 설정** 창에서 다음 단계를 수행 합니다.

   1. **관리 모드** 를 **고급**으로 설정 합니다.

   1. **클라이언트 ID** 속성에 논리 앱의 시스템 할당 id에 대 한 개체 id를 입력 합니다.

   1. **발급자 url** 속성에 `https://sts.windows.net/` url을 입력 하 고 Azure AD 테 넌 트의 디렉터리 ID를 추가 합니다.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. **허용 되는 토큰 대상** 속성에 액세스 하려는 대상 리소스의 리소스 ID를 입력 합니다.

      이 리소스 ID는 [시스템 할당 id를 사용 하도록 함수 작업을 설정할](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)때 나중에 **대상** 속성에서 사용 하는 것과 동일한 값입니다.

   이 시점에서 버전은 다음 예제와 유사 합니다.

   ![Azure Active Directory 인증 설정](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. 완료되면 **확인**을 선택합니다.

1. 논리 앱 디자이너로 돌아가서 [관리 되는 id를 사용 하 여 액세스를 인증 하는 단계](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)를 수행 합니다.

## <a name="next-steps"></a>다음 단계

* [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
