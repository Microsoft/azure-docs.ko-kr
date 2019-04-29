---
title: Azure Functions에 대한 JavaScript 개발자 참조 | Microsoft Docs
description: JavaScript를 사용하여 함수를 개발하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure 함수, 함수, 이벤트 처리, webhook, 동적 계산, 서버리스 아키텍처
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 9ef7dd7603b93f6b15988cc4cca089f0486eb3b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032436"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 개발자 가이드

이 가이드에는 JavaScript로 Azure Functions를 작성하는 복잡성에 대한 정보가 포함되어 있습니다.

JavaScript 함수는 트리거될 때 실행되는 내보낸 `function`입니다([트리거는 function.json에서 구성됨](functions-triggers-bindings.md)). 모든 함수에 전달 된 첫 번째 인수는 `context` 수신 및 송신 데이터 바인딩, 로깅 및 런타임과 통신에 사용 되는 개체입니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. 첫 번째를 만드는 함수 빠른 시작을 완료 함수를 사용 하 여 [Visual Studio Code](functions-create-first-function-vs-code.md) 하거나 [포털에서](functions-create-first-azure-function.md)합니다.

이 문서에서는 [TypeScript 앱 개발](#typescript)합니다.

## <a name="folder-structure"></a>폴더 구조

JavaScript 프로젝트에 필요한 폴더 구조는 다음과 같습니다. 이 기본값은 변경 가능합니다. 자세한 내용은 아래의 [scriptFile](#using-scriptfile) 섹션을 참조하세요.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

프로젝트 루트에는 함수 앱을 구성하는 데 사용할 수 있는 공유 [host.json](functions-host-json.md) 파일이 있습니다. 각 함수에는 자체 코드 파일(.js)과 바인딩 구성 파일(function.json)이 있는 폴더가 있습니다. `function.json`의 부모 디렉터리 이름은 항상 함수의 이름입니다.

Functions 런타임의 [버전 2.x](functions-versions.md)에 필요한 바인딩 확장은 `extensions.csproj` 파일에 정의되어 있고 실제 라이브러리 파일은 `bin` 폴더에 있습니다. 로컬에서 개발할 때는 [바인딩 확장을 등록](./functions-bindings-register.md#local-development-azure-functions-core-tools)해야 합니다. Azure Portal에서 함수를 개발할 때 이 등록이 자동으로 수행됩니다.

## <a name="exporting-a-function"></a>함수 내보내기

JavaScript 함수는 [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports)(또는 [`exports`](https://nodejs.org/api/modules.html#modules_exports))를 통해 내보내야 합니다. 내보낸 함수는 트리거될 때 실행되는 JavaScript 함수여야 합니다.

기본적으로 Functions 런타임은 `index.js`에서 함수를 찾습니다. 여기서 `index.js`는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유합니다. 기본적인 경우 내보낸 함수는 `run` 또는 `index`라는 해당 파일 또는 내보내기의 유일한 내보내기여야 합니다. 함수의 파일 위치 및 내보내기 이름을 구성하려면 아래에서 [함수의 진입점 구성](functions-reference-node.md#configure-function-entry-point)에 대한 내용을 읽어보세요.

내보낸 함수는 실행에서 인수의 수로 전달됩니다. 사용하는 첫 번째 인수는 항상 `context` 개체입니다. 함수가 동기화되면(Promise를 반환하지 않음) 올바른 사용을 위해 `context.done`을 호출해야 할 때 `context` 개체를 전달해야 합니다.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>비동기 함수 내보내기
Functions 런타임 2.x 버전에서 [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 선언 또는 일반 JavaScript [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 사용할 때 함수가 완료되었음을 나타내는 [`context.done`](#contextdone-method) 콜백을 명시적으로 호출할 필요가 없습니다. 내보낸 비동기 함수/Promise가 완료되면 함수가 완료됩니다. 버전 1.x 런타임을 대상으로 하는 함수의 경우 코드 실행이 완료되면 [`context.done`](#contextdone-method)을 호출해야 합니다.

다음 예제는 트리거되었으며 즉시 실행을 완료한다고 기록하는 간단한 함수입니다.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

비동기 함수를 내보낼 때는 `return` 값을 사용하도록 출력 바인딩을 구성할 수도 있습니다. 하나의 출력 바인딩이 있는 경우에 권장됩니다.

`return`을 사용하여 출력을 할당하려면 `function.json`에서 `name` 속성을 `$return`으로 변경합니다.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

이 경우에 함수는 다음 예제와 유사합니다.

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>바인딩 
JavaScript에서 [바인딩](functions-triggers-bindings.md)은 함수의 function.json에서 구성되고 정의됩니다. Functions는 다양한 방법으로 바인딩과 상호 작용합니다.

### <a name="inputs"></a>입력
입력은 Azure Functions에서 두 가지 범주로 나뉩니다. 즉 하나는 트리거 입력이고, 다른 하나는 추가 입력입니다. 트리거 및 기타 입력 바인딩(`direction === "in"`의 바인딩)은 다음과 같은 세 가지 방법으로 함수에서 읽을 수 있습니다.
 - **_[권장]_  함수에 전달된 매개 변수입니다.** 이러한 항목은 *function.json*에 정의된 순서대로 함수에 전달됩니다. 합니다 `name` 에 정의 된 속성 *function.json* 해야 하지만, 매개 변수의 이름과 일치 하도록 필요 하지 않습니다.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **[`context.bindings`](#contextbindings-property) 개체의 모든 멤버입니다.** 각 멤버는 *function.json*에서 정의된 `name` 속성으로 이름이 지정됩니다.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **입력으로 JavaScript[`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) 개체를 사용합니다.** 그러면 기본적으로 입력 매개 변수로 전달하는 것과 동일하지만 동적으로 입력을 처리할 수 있습니다.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>outputs
출력(`direction === "out"`의 바인딩)은 다양한 방법으로 함수에서 작성될 수 있습니다. 모든 경우에 *function.json*에 정의된 대로 바인딩의 `name` 속성은 함수에서 작성된 개체 멤버의 이름에 해당합니다. 

(이러한 메서드를 결합 하지 마세요) 다음 방법 중 하나로 출력 바인딩에 데이터를 할당할 수 있습니다.

- **_[여러 출력에 대한 권장]_ 개체를 반환합니다.** 함수를 반환 하는 비동기/프라미스를 사용 하는 경우에 할당 된 출력 데이터를 사용 하 여 개체를 반환할 수 있습니다. 아래 예제에서 출력 바인딩의 이름은 *function.json*에서 "httpResponse" 및 "queueOutput"으로 지정됩니다.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  동기 함수를 사용하는 경우 [`context.done`](#contextdone-method)을 사용하여 이 개체를 반환할 수 있습니다(예제 참조).
- **_[단일 출력에 대한 권장]_ 직접 값을 반환하고 $return 바인딩 이름을 사용합니다.** 함수를 반환하는 비동기/Promise에서 작동합니다. [비동기 함수 내보내기](#exporting-an-async-function)에서 예제를 참조하세요. 
- **`context.bindings`에 대한 값을 할당합니다.** context.bindings에 직접 값을 할당할 수 있습니다.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>바인딩 데이터 형식

입력 바인딩에 대한 데이터 형식을 정의하려면 바인딩 정의에서 `dataType` 속성을 사용합니다. 예를 들어 이진 형식의 HTTP 요청 내용을 읽으려면 `binary` 형식을 사용합니다.

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType`에 대한 옵션은 `binary`, `stream` 및 `string`입니다.

## <a name="context-object"></a>context 개체
런타임은 함수로 데이터를 전달하거나 전달받으며 사용자가 런타임과 통신할 수 있도록 하는 `context` 개체를 사용합니다. 내보낸 함수가 동기화된 경우 컨텍스트 개체는 바인딩에서 데이터를 읽고 설정하고, 로그를 작성하고, `context.done` 콜백을 사용하는 데 사용될 수 있습니다.

`context` 개체는 항상 함수의 첫 번째 매개 변수입니다. `context.done` 및 `context.log`와 같은 중요한 메서드가 있으므로 포함되어야 합니다. 원하는 개체 이름(예: `ctx` 또는 `c`)을 지정할 수 있습니다.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings 속성

```js
context.bindings
```

모든 입력 및 출력 데이터를 포함하는 명명된 개체를 반환합니다. 예를 들어 function.json의 다음 바인딩 정의를 통해 `context.bindings.myInput`에서 큐의 콘텐츠에 액세스하고 `context.bindings.myOutput`을 사용하여 출력을 큐에 할당할 수 있습니다.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

`context.binding` 개체 대신 `context.done` 메서드를 사용하여 출력 바인딩 데이터를 정의하도록 선택할 수 있습니다(아래 참조).

### <a name="contextbindingdata-property"></a>context.bindingData property

```js
context.bindingData
```

트리거 메타데이터 및 함수 호출 데이터(`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`)를 포함하는 명명된 개체를 반환합니다. 트리거 메타데이터의 예제는 [event hubs example](functions-bindings-event-hubs.md#trigger---javascript-example)을 참조하세요.

### <a name="contextdone-method"></a>context.done 메서드

```js
context.done([err],[propertyBag])
```

런타임에서는 코드가 완료되었음을 알 수 있습니다. 함수가 [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 선언을 사용하는 경우 `context.done()`을 사용할 필요가 없습니다. `context.done` 콜백을 암시적으로 호출합니다. 비동기 함수는 Node 8 이상 버전에서 지원되며 Functions 런타임의 2.x 버전이 필요합니다.

함수가 비동기 함수가 아닌 경우 함수가 완료됐음을 런타임에 알리려면 `context.done`을 **반드시 호출해야** 합니다. 실행이 누락된 경우 시간 초과가 발생합니다.

`context.done` 메서드를 사용하면 출력 바인딩 데이터가 포함된 JSON 개체와 런타임에 사용자 정의 오류를 다시 전달할 수 있습니다. `context.done`에 전달된 속성은 `context.bindings` 개체에 설정된 내용을 덮어씁니다.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log 메서드  

```js
context.log(message)
```

기본 추적 수준에서 스트리밍 함수 로그에 기록할 수 있습니다. `context.log`에 다른 추적 수준에서 함수 로그를 작성할 수 있는 추가 로깅 메서드가 제공됩니다.


| 방법                 | 설명                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | 오류 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다.   |
| **warn(_message_)**    | 경고 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다. |
| **info(_message_)**    | 정보 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다.    |
| **verbose(_message_)** | 자세한 정보 표시 수준 로깅에 씁니다.           |

다음 예제는 경고 추적 수준에서 로그를 기록합니다.

```javascript
context.log.warn("Something has happened."); 
```

host.json 파일에 [로깅에 대한 추적 수준 임계값을 구성](#configure-the-trace-level-for-console-logging)할 수 있습니다. 로그 작성에 대한 자세한 내용은 아래에서 [추적 출력 작성](#writing-trace-output-to-the-console)을 참조하세요.

함수 로그 보기 및 쿼리에 대해 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 읽어보세요.

## <a name="writing-trace-output-to-the-console"></a>콘솔에 추적 출력 작성 

Functions에서 `context.log` 메서드를 사용하여 추적 출력을 콘솔에 씁니다. Functions v2.x에서 `console.log`를 사용하는 추적 출력은 Function App 수준에서 캡처됩니다. 즉, `console.log`의 출력은 특정 함수 호출에 연결되지 않으며 따라서 특정 함수의 로그에 표시되지 않습니다. 하지만 Application Insights로 전파됩니다. Functions v1.x에서는 `console.log`를 사용하여 콘솔에 쓸 수 없습니다.

`context.log()`를 호출하면 메시지를 _정보_ 추적 수준인 기본 추적 수준에서 콘솔에 씁니다. 다음 코드는 정보 추적 수준에서 콘솔에 씁니다.

```javascript
context.log({hello: 'world'});  
```

이 코드는 위의 코드와 같습니다.

```javascript
context.log.info({hello: 'world'});  
```

이 코드는 오류 수준에서 콘솔에 기록합니다.

```javascript
context.log.error("An error has occurred.");  
```

_error_(오류)가 가장 높은 추적 수준이므로 로깅이 활성화되어 있는 한 이 추적은 모든 추적 수준에서 출력에 씁니다.

모든 `context.log` 메서드는 Node.js [util.format 메서드](https://nodejs.org/api/util.html#util_util_format_format)에서 지원하는 것과 동일한 매개 변수 형식을 지원합니다. 기본 추적 수준을 사용하여 함수 로그를 작성하는 다음 코드를 살펴보세요.

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

또한 동일한 코드를 다음과 같은 형식으로 작성할 수도 있습니다.

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>콘솔 로깅에 대한 추적 수준 구성

Functions 1.x에서는 콘솔에 작성할 임계값 추적 수준을 정의할 수 있으므로 추적이 함수에서 콘솔에 작성되는 방식을 쉽게 제어할 수 있습니다. 콘솔에 기록되는 모든 추적에 대한 임계값을 설정하려면 host.json 파일의 `tracing.consoleLevel` 속성을 사용합니다. 이 설정은 함수 앱의 모든 함수에 적용됩니다. 다음 예제에서는 추적 임계값을 설정하여 자세한 정보 표시 로깅을 사용하도록 설정합니다.

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

**consoleLevel**의 값은 `context.log` 메서드의 이름에 해당합니다. 콘솔에 대한 모든 추적 로깅을 사용하지 않으려면 **consoleLevel**을 _off_로 설정합니다. 자세한 내용은 [host.json 참조](functions-host-json-v1.md)를 참조하세요.

## <a name="http-triggers-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP, 웹후크 트리거 및 HTTP 출력 바인딩은 요청 및 응답 개체를 사용하여 HTTP 메시지를 나타냅니다.  

### <a name="request-object"></a>요청 개체

`context.req`(요청) 개체의 속성은 다음과 같습니다.

| 자산      | 설명                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 요청의 본문을 포함하는 개체입니다.               |
| _headers_     | 요청 헤더를 포함하는 개체입니다.                   |
| _method_      | 요청의 HTTP 메서드입니다.                                |
| _originalUrl_ | 요청의 URL입니다.                                        |
| _params_      | 요청의 라우팅 매개 변수를 포함하는 개체입니다. |
| _query_       | 쿼리 매개 변수를 포함하는 개체입니다.                  |
| _rawBody_     | 문자열 형식의 메시지 본문입니다.                           |


### <a name="response-object"></a>응답 개체

`context.res`(응답) 개체의 속성은 다음과 같습니다.

| 자산  | 설명                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 응답의 본문을 포함하는 개체입니다.         |
| _headers_ | 응답 헤더를 포함하는 개체입니다.             |
| _isRaw_   | 응답에 대한 서식 지정을 건너뜀을 나타냅니다.    |
| _status_  | 응답의 HTTP 상태 코드입니다.                     |

### <a name="accessing-the-request-and-response"></a>요청 및 응답 액세스 

HTTP 트리거로 작업할 때 여러 가지 방법으로 HTTP 요청 및 응답 개체에 액세스할 수 있습니다.

+ **`context` 개체의 `req` 및 `res` 속성에서.** 이러한 방식으로 전체 `context.bindings.name` 패턴을 사용하지 않고 대신 기존 패턴을 사용하여 context 개체에서 HTTP 데이터에 액세스할 수 있습니다. 다음 예제에서는 `context`의 `req` 및 `res` 개체에 액세스하는 방법을 보여 줍니다.

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **명명된 입출력 바인딩에서.** 이러한 방식으로 HTTP 트리거와 바인딩은 다른 바인딩과 동일하게 작동합니다. 다음 예제에서는 명명된 `response` 바인딩을 사용하여 응답 개체를 설정합니다. 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[응답 전용]_ `context.res.send(body?: any)`를 호출합니다.** HTTP 응답은 입력 `body`를 응답 본문으로 작성합니다. `context.done()`은 암시적으로 호출됩니다.

+ **_[응답 전용]_ `context.done()`를 호출합니다.** `context.done()` 메서드에 전달되는 응답을 반환하는 특별한 종류의 HTTP 바인딩이 있습니다. 다음 HTTP 출력 바인딩은 `$return` 출력 매개 변수를 정의합니다.

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>노드 버전

다음 표에서는 주 버전의 Functions 런타임 각각에서 사용되는 Node.js 버전을 보여 줍니다.

| Functions 버전 | Node.js 버전 | 
|---|---|
| 1.x | 6.11.2(런타임에 의해 잠김) |
| 2.x  | _활성 LTS_ 및 짝수 번호의 _현재_ Node.js 버전(8.11.1 및 10.14.1 권장). WEBSITE_NODE_DEFAULT_VERSION [앱 설정](functions-how-to-use-azure-function-app-settings.md#settings)을 사용하여 버전을 설정합니다.|

위의 앱 설정을 확인하거나 함수에서 `process.version`을 인쇄하여 런타임에 사용 중인 현재 버전을 확인할 수 있습니다.

## <a name="dependency-management"></a>종속성 관리
아래 예제와 같이 JavaScript 코드에서 커뮤니티 라이브러리를 사용하려면, Azure의 Function App에 모든 종속성이 설치되어 있는지 확인해야 합니다.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Function App의 루트에 `package.json` 파일을 정의해야 합니다. 파일을 정의하면 앱의 모든 함수에서 동일한 캐시된 패키지를 공유할 수 있으므로 최상의 성능을 제공합니다. 버전 충돌이 발생하는 경우 특정 함수의 폴더에 `package.json` 파일을 추가하여 이 충돌을 해결할 수 있습니다.  

원본 제어에서 Function App을 배포할 때 리포지토리에 있는 모든 `package.json` 파일이 배포 중에 폴더의 `npm install`을 트리거합니다. 그러나 포털 또는 CLI를 통해 배포할 때는 수동으로 패키지를 설치해야 합니다.

Function App에 패키지를 설치하는 방법에는 두 가지가 있습니다. 

### <a name="deploying-with-dependencies"></a>종속성을 사용하여 배포
1. `npm install`을 실행하여 모든 필수 패키지를 로컬에 설치합니다.

2. 코드를 배포하고 `node_modules` 폴더가 배포에 포함되어 있는지 확인합니다. 


### <a name="using-kudu"></a>Kudu 사용
1. `https://<function_app_name>.scm.azurewebsites.net`로 이동합니다.

2. **디버그 콘솔** > **CMD**를 클릭합니다.

3. `D:\home\site\wwwroot`로 이동한 다음 package.json 파일을 페이지 위쪽의 **wwwroot** 폴더로 끌어갑니다.  
    다른 방법으로 함수 앱에 파일을 업로드할 수도 있습니다. 자세한 내용은 [함수 앱 파일을 업데이트하는 방법](functions-reference.md#fileupdate)을 참조하세요. 

4. package.json 파일을 업로드한 후 **Kudu 원격 실행 콘솔**에서 `npm install` 명령을 실행합니다.  
    이 작업은 package.json 파일에 표시된 패키지를 다운로드하고 함수 앱을 다시 시작합니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. `GetEnvironmentVariable` 함수에 표시된 대로 `process.env`를 사용하여 이러한 설정에 액세스할 수 있습니다.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

로컬로 실행하는 경우 앱 설정은 [local.settings.json](functions-run-local.md#local-settings-file) 프로젝트 파일에서 읽습니다.

## <a name="configure-function-entry-point"></a>함수 진입점 구성

`function.json` 속성과 `scriptFile` 및 `entryPoint`는 내보낸 함수의 위치와 이름을 구성하는 데 사용할 수 있습니다. 이러한 속성은 JavaScript가 트랜스파일된 경우에 중요할 수 있습니다.

### <a name="using-scriptfile"></a>`scriptFile` 사용

기본적으로 JavaScript 함수는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유하는 `index.js` 파일에서 실행됩니다.

`scriptFile`은 다음 예제와 같은 폴더 구조를 가져오는 데 사용할 수 있습니다.

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`myNodeFunction`의 `function.json`에는 실행할 내보낸 함수가 있는 파일을 가리키는 `scriptFile` 속성이 포함되어야 합니다.

```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>`entryPoint` 사용

`scriptFile`(또는 `index.js`)에서 함수를 찾아서 실행하려면 `module.exports`를 사용하여 함수를 내보내야 합니다. 기본적으로 트리거되면 실행되는 함수는 해당 파일의 내보내기, `run`이라는 이름의 내보내기 또는 `index`라고 명명된 내보내기입니다.

다음 예제와 같이 `function.json`에서 `entryPoint`를 사용하여 구성할 수 있습니다.

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

사용자 함수에서 `this` 매개 변수를 지원하는 Functions v2.x에서 함수 코드는 다음 예제와 같을 수 있습니다.

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

이 예제에서는 해당 것에 유의 해야 개체를 내보낸 있지만 실행 간에 상태를 유지 하는 것에 대 한 보장 되지 않습니다.

## <a name="local-debugging"></a>로컬 디버깅

시작 하는 경우는 `--inspect` 매개 변수를 지정된 된 포트에서 디버깅 클라이언트에 대 한 Node.js 프로세스를 수신 대기 합니다. Azure Functions에서 2.x의 경우 환경 변수 또는 앱 설정을 추가 하 여 코드를 실행 하는 Node.js 프로세스에 전달할 인수를 지정할 수 있습니다 `languageWorkers:node:arguments = <args>`합니다. 

로컬로 디버그 하려면 추가 `"languageWorkers:node:arguments": "--inspect=5858"` 아래에서 `Values` 에서 프로그램 [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) 파일과 5858 포트로 디버거를 연결 합니다.

VS Code를 사용 하 여 디버깅 하는 경우는 `--inspect` 매개 변수를 사용 하 여 자동으로 추가 됩니다는 `port` 프로젝트의 launch.json 파일의 값입니다.

버전에서 1.x에서 설정 `languageWorkers:node:arguments` 작동 하지 것입니다. 디버그 포트를 사용 하 여 선택할 수 있습니다 합니다 [ `--nodeDebugPort` ](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) Azure Functions 핵심 도구에 대 한 매개 변수입니다.

## <a name="typescript"></a>TypeScript

버전 대상 지정의 Functions 런타임 2.x 둘 다 [Visual Studio Code에 대 한 Azure Functions](functions-create-first-function-vs-code.md) 하며 [Azure Functions 핵심 도구](functions-run-local.md) 지원 템플릿을 사용 하 여 함수 앱을 만들 수 있습니다 TypeScript 함수 앱 프로젝트입니다. 서식 파일 생성 `package.json` 고 `tsconfig.json` 트랜스 파일을 쉽게 하는 프로젝트 파일을 실행 하 고 이러한 도구를 사용 하 여 TypeScript 코드에서 JavaScript 함수를 게시 합니다.

생성 된 `.funcignore` 파일 프로젝트를 Azure에 게시 되 면 파일 제외 됩니다 나타내는 데 사용 됩니다.  

TypeScript 파일 (.ts)은 트랜스 파일 된 JavaScript (.js) 파일에는 `dist` 출력 디렉터리입니다. TypeScript 템플릿을 사용 합니다 [ `scriptFile` 매개 변수](#using-scriptfile) 에서 `function.json` 해당.js 파일의 위치를 나타내는 `dist` 폴더. 출력 위치를 사용 하 여 템플릿에 의해 설정 됩니다 `outDir` 의 매개 변수는 `tsconfig.json` 파일입니다. 이 설정을 사용 하거나 폴더의 이름을 변경 하면 런타임이 실행 하는 코드를 찾을 수 없는 경우.

> [!NOTE]
> TypeScript에 대 한 실험적 지원 있는 버전의 Functions 런타임 1.x 합니다. 실험 버전 transpiles 함수를 호출 하는 경우 JavaScript 파일로 TypeScript 파일입니다. 버전에서 2.x의 경우이 실험적 지원을 호스트 초기화 되기 전에 소스 간 컴파일을 수행 하는 도구 기반 메서드 및 배포 프로세스 중 대체 되었습니다.

로컬로 개발 하 고 TypeScript 프로젝트에서 배포 하는 방식을 개발 도구에 따라 달라 집니다.

### <a name="visual-studio-code"></a>Visual Studio Code

합니다 [Visual Studio Code에 대 한 Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 확장을 사용 하면 TypeScript를 사용 하 여 함수를 개발할 수 있습니다. 핵심 도구는 Azure Functions 확장의 요구 사항입니다.

Visual Studio Code에는 TypeScript 함수 앱을 만들려면 선택 하기만 하면 `TypeScript` 을 함수 앱을 만들고 언어를 선택 하 라는 메시지가 표시 됩니다.

누르면 **F5** 앱을 실행 하려면 로컬에서 소스 간 컴파일을 수행 됩니다 (func.exe) 호스트 초기화 되기 전에 합니다. 

사용 하 여 Azure 함수 앱을 배포할 때의 **함수 앱에 배포 하는 중...**  단추, Azure Functions 확장 TypeScript 소스 파일에서 먼저 JavaScript 파일의 프로덕션 준비 빌드를 생성 합니다.

### <a name="azure-functions-core-tools"></a>Azure Functions 핵심 도구

핵심 도구를 사용 하는 TypeScript 함수 앱 프로젝트를 만들려면 함수 앱을 만들 때 typescript 언어 옵션을 지정 해야 합니다. 다음 방법 중 하나에서이 수행할 수 있습니다.

- 실행 합니다 `func init` 명령을 선택 `node` 언어 스택 및 선택 `typescript`합니다.

- `func init --worker-runtime typescript` 명령을 실행합니다.

핵심 도구를 사용 하 여 로컬로 함수 앱 코드를 실행 하려면 사용 합니다 `npm start` 대신 명령을 `func host start`합니다. `npm start` 명령은 다음 명령과 같습니다.

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

사용 하기 전에 합니다 [ `func azure functionapp publish` ] Azure에 배포 하는 명령, 먼저 실행 해야 합니다는 `npm run build:production` 명령입니다. 이 명령을 사용 하 여 배포할 수 있는 TypeScript 소스 파일에서 JavaScript 파일의 프로덕션 준비 빌드를 만듭니다 [ `func azure functionapp publish` ]합니다.

## <a name="considerations-for-javascript-functions"></a>JavaScript 함수에 대한 고려 사항

JavaScript 함수로 작업하는 경우 다음 섹션의 고려 사항에 유의해야 합니다.

### <a name="choose-single-vcpu-app-service-plans"></a>단일 vCPU App Service 계획 선택

App Service 계획을 사용하는 함수 앱을 만들 때 여러 vCPU가 있는 계획보다는 단일 vCPU 계획을 선택하는 것이 좋습니다. 현재 Functions는 단일 vCPU VM에서 JavaScript 함수를 더 효율적으로 실행합니다. 더 큰 VM을 사용해도 예상된 성능 향상을 보여 주지 않습니다. 필요한 경우 더 많은 단일 vCPU VM 인스턴스를 추가 하 여 수동으로 확장 수 또는 자동 크기 조정을 설정할 수 있습니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json)을 참조하세요.

### <a name="cold-start"></a>콜드 부팅

서버리스 호스팅 모델에서 Azure Functions를 개발하는 경우 콜드 부팅이 현실입니다. *콜드 부팅*이란 일정 기간 동안 비활성이었다가 처음으로 함수 앱을 시작하면 시작하는 데 더 오래 걸린다는 사실을 의미합니다. 특히 종속성 트리가 큰 JavaScript 함수의 경우 콜드 부팅은 중요할 수 있습니다. 콜드 부팅 프로세스의 속도를 높이려면 가능한 경우 [함수를 패키지 파일로 실행](run-functions-from-deployment-package.md)합니다. 여러 배포 방법은 기본적으로 패키지 모델에서 실행을 사용하지만 대규모 콜드 부팅이 발생하고 이러한 방식으로 실행하지 않는 경우 이 변경 내용으로 인해 크게 개선될 수 있습니다.

### <a name="connection-limits"></a>연결 제한

Azure Functions 응용 프로그램에서 서비스별 클라이언트를 사용 하는 경우 모든 함수 호출을 사용 하 여 새 클라이언트를 만들지 마세요. 대신, 전역 범위에서 정적 클라이언트를 만듭니다. 자세한 내용은 [Azure Functions에서 연결 관리](manage-connections.md)합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

+ [Azure Functions에 대한 모범 사례](functions-best-practices.md)
+ [Azure Functions 개발자 참조](functions-reference.md)
+ [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

[`func azure functionapp publish`]: functions-run-local.md#project-file-deployment
