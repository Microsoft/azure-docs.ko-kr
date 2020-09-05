---
title: Azure Functions에 대 한 JavaScript 개발자 참조
description: JavaScript를 사용하여 함수를 개발하는 방법을 알아봅니다.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 6ff56ba6dc85901c8cdc7a9b06fbc261feb8792d
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055331"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 개발자 가이드

이 가이드는 JavaScript를 사용 하 여 Azure Functions를 개발 하는 데 도움이 되는 자세한 정보를 포함 합니다.

Express.js, Node.js 또는 JavaScript developer로 Azure Functions를 처음 접하는 경우 먼저 다음 문서 중 하나를 읽어 보세요.

| 시작 | 개념| 단계별 학습 |
| -- | -- | -- | 
| <ul><li>[ Visual Studio Code를 사용 하Node.js 함수](./functions-create-first-function-vs-code.md?pivots=programming-language-javascript)</li><li>[ 터미널/명령 프롬프트를 사용 하Node.js 함수](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-javascript)</li></ul> | <ul><li>[개발자 가이드](functions-reference.md)</li><li>[호스팅 옵션](functions-scale.md)</li><li>[TypeScript 함수](#typescript)</li><li>[성능 &nbsp; 고려 사항](functions-best-practices.md)</li></ul> | <ul><li>[서버리스 애플리케이션 만들기](/learn/paths/create-serverless-applications/)</li><li>[서버를 사용 하지 않는 Api에 Node.js 및 Express Api 리팩터링](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>JavaScript 함수 기본 사항

JavaScript (Node.js) 함수는 `function` 트리거될 때 실행 되는 내보낸입니다.[트리거는 function.js에서 구성](functions-triggers-bindings.md)됩니다. 모든 함수에 전달 되는 첫 번째 인수는 개체 이며,이 `context` 개체는 바인딩 데이터를 보내고 보내고 보내고, 로깅 하 고, 런타임과 통신 하는 데 사용 됩니다.

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

Functions 런타임의 [버전 2.x](functions-versions.md)에 필요한 바인딩 확장은 `extensions.csproj` 파일에 정의되어 있고 실제 라이브러리 파일은 `bin` 폴더에 있습니다. 로컬에서 개발할 때는 [바인딩 확장을 등록](./functions-bindings-register.md#extension-bundles)해야 합니다. Azure Portal에서 함수를 개발할 때 이 등록이 자동으로 수행됩니다.

## <a name="exporting-a-function"></a>함수 내보내기

JavaScript 함수는 (또는)를 통해 내보내야 합니다 [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) [`exports`](https://nodejs.org/api/modules.html#modules_exports) . 내보낸 함수는 트리거될 때 실행되는 JavaScript 함수여야 합니다.

기본적으로 Functions 런타임은 `index.js`에서 함수를 찾습니다. 여기서 `index.js`는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유합니다. 기본적인 경우 내보낸 함수는 `run` 또는 `index`라는 해당 파일 또는 내보내기의 유일한 내보내기여야 합니다. 함수의 파일 위치 및 내보내기 이름을 구성하려면 아래에서 [함수의 진입점 구성](functions-reference-node.md#configure-function-entry-point)에 대한 내용을 읽어보세요.

내보낸 함수는 실행에서 인수의 수로 전달됩니다. 사용하는 첫 번째 인수는 항상 `context` 개체입니다. 함수가 동기 (약속을 반환 하지 않음) 인 경우 올바른 사용을 위해를 호출 해야 하므로 개체를 전달 해야 합니다 `context` `context.done` .

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>비동기 함수 내보내기
[`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function)함수 런타임의 버전 2.x에서 선언 또는 일반 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 을 사용 하는 경우 [`context.done`](#contextdone-method) 함수가 완료 되었다는 신호를 보내기 위해 명시적으로 콜백을 호출할 필요가 없습니다. 내보낸 비동기 함수/Promise가 완료되면 함수가 완료됩니다. 버전 1.x 런타임을 대상으로 하는 함수의 경우 [`context.done`](#contextdone-method) 코드 실행이 완료 될 때도 호출 해야 합니다.

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
 - **_[권장] _ 함수에 전달된 매개 변수입니다.** 이러한 항목은 *function.json*에 정의된 순서대로 함수에 전달됩니다. `name` *function.js* 에 정의 된 속성은 매개 변수의 이름과 일치 하지 않아도 되지만 반드시 일치 해야 하는 것은 아닙니다.
 
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
   
 - **입력으로 JavaScript[`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) 개체를 사용합니다.** 그러면 기본적으로 입력 매개 변수로 전달하는 것과 동일하지만 동적으로 입력을 처리할 수 있습니다.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>출력
출력(`direction === "out"`의 바인딩)은 다양한 방법으로 함수에서 작성될 수 있습니다. 모든 경우에 *function.json*에 정의된 대로 바인딩의 `name` 속성은 함수에서 작성된 개체 멤버의 이름에 해당합니다. 

다음 방법 중 하나로 출력 바인딩에 데이터를 할당할 수 있습니다 (이러한 메서드를 결합 하지 않음).

- **_[여러 출력에 대한 권장]_ 개체를 반환합니다.** 비동기/약속 반환 함수를 사용 하는 경우 출력 데이터가 할당 된 개체를 반환할 수 있습니다. 아래 예제에서 출력 바인딩의 이름은 *function.json*에서 "httpResponse" 및 "queueOutput"으로 지정됩니다.

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

  동기 함수를 사용 하는 경우 [`context.done`](#contextdone-method) (예제 참조)를 사용 하 여이 개체를 반환할 수 있습니다.
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

런타임은 개체를 사용 하 여 `context` 함수와 런타임에 데이터를 전달 합니다. 바인딩에서 데이터를 읽고 설정 하 고 로그에 쓰는 데 사용 되는 `context` 개체는 항상 함수에 전달 되는 첫 번째 매개 변수입니다.

동기 코드를 포함 하는 함수의 경우 컨텍스트 개체는 `done` 함수 처리가 완료 될 때 호출 하는 콜백을 포함 합니다. `done`비동기 코드를 작성할 때 명시적 호출은 필요 하지 않습니다. `done` 콜백은 암시적으로 호출 됩니다.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

함수로 전달 되는 컨텍스트는 `executionContext` 속성을 노출 합니다. 속성은 다음 속성을 포함 하는 개체입니다.

| 속성 이름  | Type  | Description |
|---------|---------|---------|
| `invocationId` | String | 특정 함수 호출에 대 한 고유 식별자를 제공 합니다. |
| `functionName` | 문자열 | 실행 중인 함수의 이름을 제공 합니다. |
| `functionDirectory` | 문자열 | 함수 앱 디렉터리를 제공 합니다. |

다음 예제에서는를 반환 하는 방법을 보여 줍니다 `invocationId` .

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>context.bindings 속성

```js
context.bindings
```

바인딩 데이터를 읽거나 할당 하는 데 사용 되는 명명 된 개체를 반환 합니다. 입력 및 트리거 바인딩 데이터는의 속성을 읽어 액세스할 수 있습니다 `context.bindings` . 출력 바인딩 데이터는에 데이터를 추가 하 여 할당할 수 있습니다. `context.bindings`

예를 들어 function.json의 다음 바인딩 정의를 통해 `context.bindings.myInput`에서 큐의 콘텐츠에 액세스하고 `context.bindings.myOutput`을 사용하여 출력을 큐에 할당할 수 있습니다.

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

트리거 메타데이터 및 함수 호출 데이터(`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`)를 포함하는 명명된 개체를 반환합니다. 트리거 메타데이터의 예제는 [event hubs example](functions-bindings-event-hubs-trigger.md)을 참조하세요.

### <a name="contextdone-method"></a>context.done 메서드

```js
context.done([err],[propertyBag])
```

런타임에서는 코드가 완료되었음을 알 수 있습니다. 함수에서 선언을 사용 하는 경우를 [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 사용할 필요가 없습니다 `context.done()` . `context.done` 콜백을 암시적으로 호출합니다. 비동기 함수는 Node 8 이상 버전에서 지원되며 Functions 런타임의 2.x 버전이 필요합니다.

함수가 비동기 함수가 아닌 경우를 **호출** `context.done` 하 여 함수가 완료 되었음을 런타임에 알려야 합니다. 실행이 누락된 경우 시간 초과가 발생합니다.

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


| 메서드                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **오류 (_메시지_)**   | 오류 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다.   |
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

Functions에서 `context.log` 메서드를 사용하여 추적 출력을 콘솔에 씁니다. Functions v2.x에서 `console.log`를 사용하는 추적 출력은 함수 앱 수준에서 캡처됩니다. 즉,의 출력 `console.log` 은 특정 함수 호출에 연결 되지 않으며 특정 함수의 로그에 표시 되지 않습니다. 하지만 Application Insights로 전파됩니다. Functions v1.x에서는 `console.log`를 사용하여 콘솔에 쓸 수 없습니다.

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

| 속성      | Description                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 요청의 본문을 포함하는 개체입니다.               |
| _머리글과_     | 요청 헤더를 포함하는 개체입니다.                   |
| _방법이_      | 요청의 HTTP 메서드입니다.                                |
| _originalUrl_ | 요청의 URL입니다.                                        |
| _params_      | 요청의 라우팅 매개 변수를 포함하는 개체입니다. |
| _쿼리_       | 쿼리 매개 변수를 포함하는 개체입니다.                  |
| _rawBody_     | 문자열 형식의 메시지 본문입니다.                           |


### <a name="response-object"></a>응답 개체

`context.res`(응답) 개체의 속성은 다음과 같습니다.

| 속성  | Description                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 응답의 본문을 포함하는 개체입니다.         |
| _머리글과_ | 응답 헤더를 포함하는 개체입니다.             |
| _isRaw_   | 응답에 대한 서식 지정을 건너뜀을 나타냅니다.    |
| _status_  | 응답의 HTTP 상태 코드입니다.                     |
| _쿠키_ | 응답에 설정 된 HTTP 쿠키 개체의 배열입니다. HTTP 쿠키 개체에는 `name` , `value` 및와 같은 기타 쿠키 속성이 있습니다 `maxAge` `sameSite` . |

### <a name="accessing-the-request-and-response"></a>요청 및 응답 액세스 

HTTP 트리거로 작업할 때 여러 가지 방법으로 HTTP 요청 및 응답 개체에 액세스할 수 있습니다.

+ **`context` 개체의 `req` 및 `res` 속성에서.** 이러한 방식으로 전체 `context.bindings.name` 패턴을 사용하지 않고 대신 기존 패턴을 사용하여 context 개체에서 HTTP 데이터에 액세스할 수 있습니다. 다음 예제에서는 `context`의 `req` 및 `res` 개체에 액세스하는 방법을 보여 줍니다.

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
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
+ **_[응답 전용]_`context.res.send(body?: any)`를 호출합니다.** HTTP 응답은 입력 `body`를 응답 본문으로 작성합니다. `context.done()`은 암시적으로 호출됩니다.

+ **_[응답 전용]_`context.done()`를 호출합니다.** 특수 한 형식의 HTTP 바인딩은 메서드에 전달 되는 응답을 반환 합니다 `context.done()` . 다음 HTTP 출력 바인딩은 `$return` 출력 매개 변수를 정의합니다.

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

## <a name="scaling-and-concurrency"></a>스케일링 및 동시성

기본적으로 Azure Functions는 응용 프로그램의 부하를 자동으로 모니터링 하 고 필요에 따라 Node.js에 대 한 추가 호스트 인스턴스를 만듭니다. Functions는 QueueTrigger에 대한 메시지 보존 기간, 큐 크기 등의 기본 제공(사용자 구성 불가능) 임계값을 여러 트리거 유형에 사용하여 인스턴스를 추가할 시기를 결정합니다. 자세한 내용은 [사용 계획 및 프리미엄 계획의 작동 방식](functions-scale.md#how-the-consumption-and-premium-plans-work)을 참조하세요.

이러한 크기 조정 동작은 많은 Node.js 응용 프로그램에서 충분 합니다. CPU 바인딩된 응용 프로그램의 경우 여러 언어 작업자 프로세스를 사용 하 여 성능을 향상 시킬 수 있습니다.

기본적으로 모든 Functions 호스트 인스턴스에는 언어 작업자 프로세스가 하나만 있습니다. [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 애플리케이션 설정을 사용하여 호스트당 작업자 프로세스 수를 10개까지 늘릴 수 있습니다. 그러면 Azure Functions는 이러한 작업자 사이에 동시 함수 호출을 균등하게 분산하려고 시도합니다. 

요구 사항을 충족하기 위해 애플리케이션을 스케일 아웃할 때 Functions가 만드는 각 호스트에 FUNCTIONS_WORKER_PROCESS_COUNT가 적용됩니다. 

## <a name="node-version"></a>노드 버전

다음 표에서는 운영 체제별로 함수 런타임의 각 주 버전에 대해 지원 되는 현재 Node.js 버전을 보여 줍니다.

| Functions 버전 | 노드 버전 (Windows) | 노드 버전 (Linux) |
|---|---| --- |
| 1.x | 6.11.2(런타임에 의해 잠김) | 해당 없음 |
| 2.x  | ~ 8<br/>~ 10 (권장)<br/>~ 12<sup>*</sup> | ~ 8 (권장)<br/>~ 10  |
| 3.x | ~ 10<br/>~ 12 (권장)  | ~ 10<br/>~ 12 (권장) |

<sup>*</sup>노드 ~ 12는 현재 함수 런타임의 버전 2.x에서 허용 됩니다. 그러나 최상의 성능을 위해 노드 ~ 12에서 함수 런타임 버전 3(sp3)을 사용 하는 것이 좋습니다. 

위의 앱 설정을 확인하거나 함수에서 `process.version`을 인쇄하여 런타임에 사용 중인 현재 버전을 확인할 수 있습니다. WEBSITE_NODE_DEFAULT_VERSION [앱 설정을](functions-how-to-use-azure-function-app-settings.md#settings) 와 같은 지원 되는 lts 버전으로 설정 하 여 Azure의 버전을 대상으로 `~10` 합니다.

## <a name="dependency-management"></a>종속성 관리
아래 예제와 같이 JavaScript 코드에서 커뮤니티 라이브러리를 사용하려면, Azure의 함수 앱에 모든 종속성이 설치되어 있는지 확인해야 합니다.

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
> 함수 앱의 루트에 `package.json` 파일을 정의해야 합니다. 파일을 정의하면 앱의 모든 함수에서 동일한 캐시된 패키지를 공유할 수 있으므로 최상의 성능을 제공합니다. 버전 충돌이 발생하는 경우 특정 함수의 폴더에 `package.json` 파일을 추가하여 이 충돌을 해결할 수 있습니다.  

원본 제어에서 함수 앱을 배포할 때 리포지토리에 있는 모든 `package.json` 파일이 배포 중에 폴더의 `npm install`을 트리거합니다. 그러나 포털 또는 CLI를 통해 배포할 때는 수동으로 패키지를 설치해야 합니다.

함수 앱에 패키지를 설치하는 방법에는 두 가지가 있습니다. 

### <a name="deploying-with-dependencies"></a>종속성을 사용하여 배포
1. `npm install`을 실행하여 모든 필수 패키지를 로컬에 설치합니다.

2. 코드를 배포하고 `node_modules` 폴더가 배포에 포함되어 있는지 확인합니다. 


### <a name="using-kudu"></a>Kudu 사용
1. `https://<function_app_name>.scm.azurewebsites.net`로 이동합니다.

2. **디버그 콘솔**  >  **CMD**를 클릭 합니다.

3. `D:\home\site\wwwroot`로 이동한 다음 package.json 파일을 페이지 위쪽의 **wwwroot** 폴더로 끌어갑니다.  
    다른 방법으로 함수 앱에 파일을 업로드할 수도 있습니다. 자세한 내용은 [함수 앱 파일을 업데이트하는 방법](functions-reference.md#fileupdate)을 참조하세요. 

4. package.json 파일을 업로드한 후 **Kudu 원격 실행 콘솔**에서 `npm install` 명령을 실행합니다.  
    이 작업은 package.json 파일에 표시된 패키지를 다운로드하고 함수 앱을 다시 시작합니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. `process.env` `context.log()` `AzureWebJobsStorage` 및 환경 변수를 기록 하는에 대 한 두 번째 및 세 번째 호출에서 여기에 표시 된 대로를 사용 하 여 이러한 설정에 액세스할 수 있습니다 `WEBSITE_SITE_NAME` .

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
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
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`myNodeFunction`의 `function.json`에는 실행할 내보낸 함수가 있는 파일을 가리키는 `scriptFile` 속성이 포함되어야 합니다.

```json
{
  "scriptFile": "../lib/sayHello.js",
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

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

이 예제에서 개체를 내보내는 경우에도 실행 간의 상태를 유지 하는 것은 보장 되지 않는다는 점에 유의 해야 합니다.

## <a name="local-debugging"></a>로컬 디버깅

매개 변수를 사용 하 여 시작 하면 `--inspect` Node.js 프로세스가 지정 된 포트에서 디버깅 클라이언트를 수신 대기 합니다. Azure Functions 2.x에서는 환경 변수 또는 앱 설정을 추가 하 여 코드를 실행 하는 Node.js 프로세스에 전달할 인수를 지정할 수 있습니다 `languageWorkers:node:arguments = <args>` . 

로컬로 디버그 하려면 `"languageWorkers:node:arguments": "--inspect=5858"` `Values` 파일 [ 의local.settings.js](./functions-run-local.md#local-settings-file) 에를 추가 하 고 포트 5858에 디버거를 연결 합니다.

VS Code를 사용 하 여 디버깅 하는 경우 `--inspect` `port` 파일의 프로젝트 launch.js에 있는 값을 사용 하 여 매개 변수가 자동으로 추가 됩니다.

버전 1.x에서는 설정이 `languageWorkers:node:arguments` 작동 하지 않습니다. Azure Functions Core Tools에서 매개 변수를 사용 하 여 디버그 포트를 선택할 수 있습니다 [`--nodeDebugPort`](./functions-run-local.md#start) .

## <a name="typescript"></a>TypeScript

함수 런타임의 버전 2.x를 대상으로 지정 하는 경우 Visual Studio Code 및 [Azure Functions Core Tools](functions-run-local.md) [에 대 한 Azure Functions](functions-create-first-function-vs-code.md) 를 모두 사용 하 여 TypeScript 함수 앱 프로젝트를 지 원하는 템플릿을 사용 하 여 함수 앱을 만들 수 있습니다. 템플릿에서는 `package.json` `tsconfig.json` 이러한 도구를 사용 하 여 TypeScript 코드에서 JavaScript 함수를 더 쉽게 트랜스 파일 실행 하 고 게시할 수 있도록 하는 및 프로젝트 파일을 생성 합니다.

생성 된 `.funcignore` 파일은 프로젝트가 Azure에 게시 될 때 제외 되는 파일을 나타내는 데 사용 됩니다.  

TypeScript 파일 (.ts)은 출력 디렉터리에서 JavaScript 파일 (.js)로 트랜스 파일 된 됩니다 `dist` . TypeScript 템플릿에서는의 [ `scriptFile` 매개 변수](#using-scriptfile) 를 사용 `function.json` 하 여 폴더에 있는 해당 .js 파일의 위치를 표시 합니다 `dist` . 출력 위치는 `outDir` 파일에서 매개 변수를 사용 하 여 템플릿에 의해 설정 됩니다 `tsconfig.json` . 이 설정 또는 폴더의 이름을 변경 하면 런타임에서 실행할 코드를 찾을 수 없습니다.

TypeScript 프로젝트에서 로컬로 개발 하 고 배포 하는 방법은 개발 도구에 따라 달라 집니다.

### <a name="visual-studio-code"></a>Visual Studio Code

[Visual Studio Code 확장에 대 한 Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 를 사용 하면 TypeScript를 사용 하 여 함수를 개발할 수 있습니다. 핵심 도구는 Azure Functions 확장의 요구 사항입니다.

Visual Studio Code에서 TypeScript 함수 앱을 만들려면 `TypeScript` 함수 앱을 만들 때 해당 언어로를 선택 합니다.

**F5** 키를 눌러 응용 프로그램을 로컬로 실행 하는 경우로는 호스트 (func.exe)가 초기화 되기 전에 수행 됩니다. 

**함수 앱에 배포** ... 단추를 사용 하 여 함수 앱을 Azure에 배포 하는 경우 Azure Functions 확장은 먼저 TypeScript 소스 파일에서 프로덕션이 준비 된 JavaScript 파일 빌드를 생성 합니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

핵심 도구를 사용 하는 경우 TypeScript 프로젝트가 JavaScript 프로젝트와 다른 몇 가지 방법이 있습니다.

#### <a name="create-project"></a>프로젝트 만들기

핵심 도구를 사용 하 여 TypeScript 함수 앱 프로젝트를 만들려면 함수 앱을 만들 때 TypeScript 언어 옵션을 지정 해야 합니다. 다음 방법 중 하나를 수행 하 여이 작업을 수행할 수 있습니다.

- 명령을 실행 `func init` 하 고를 `node` 언어 스택으로 선택한 다음를 선택 `typescript` 합니다.

- `func init --worker-runtime typescript` 명령을 실행합니다.

#### <a name="run-local"></a>로컬 실행

핵심 도구를 사용 하 여 함수 앱 코드를 로컬로 실행 하려면 대신 다음 명령을 사용 합니다 `func host start` . 

```command
npm install
npm start
```

`npm start`명령은 다음 명령과 동일 합니다.

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Azure에 게시

명령을 사용 하 여 [`func azure functionapp publish`] Azure에 배포 하기 전에 TypeScript 원본 파일에서 프로덕션 준비가 된 JavaScript 파일 빌드를 만듭니다. 

다음 명령은 핵심 도구를 사용 하 여 TypeScript 프로젝트를 준비 하 고 게시 합니다. 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

이 명령에서을 `<APP_NAME>` 함수 앱의 이름으로 바꿉니다.

## <a name="considerations-for-javascript-functions"></a>JavaScript 함수에 대한 고려 사항

JavaScript 함수로 작업하는 경우 다음 섹션의 고려 사항에 유의해야 합니다.

### <a name="choose-single-vcpu-app-service-plans"></a>단일 vCPU App Service 계획 선택

App Service 계획을 사용하는 함수 앱을 만들 때 여러 vCPU가 있는 계획보다는 단일 vCPU 계획을 선택하는 것이 좋습니다. 현재 Functions는 단일 vCPU VM에서 JavaScript 함수를 더 효율적으로 실행합니다. 더 큰 VM을 사용해도 예상된 성능 향상을 보여 주지 않습니다. 필요한 경우 더 많은 단일 vCPU VM 인스턴스를 추가 하 여 수동으로 확장 하거나 자동 크기 조정을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json)을 참조하세요.

### <a name="cold-start"></a>콜드 부팅

서버리스 호스팅 모델에서 Azure Functions를 개발하는 경우 콜드 부팅이 현실입니다. *콜드 부팅*이란 일정 기간 동안 비활성이었다가 처음으로 함수 앱을 시작하면 시작하는 데 더 오래 걸린다는 사실을 의미합니다. 특히 종속성 트리가 큰 JavaScript 함수의 경우 콜드 부팅은 중요할 수 있습니다. 콜드 부팅 프로세스의 속도를 높이려면 가능한 경우 [함수를 패키지 파일로 실행](run-functions-from-deployment-package.md)합니다. 여러 배포 방법은 기본적으로 패키지 모델에서 실행을 사용하지만 대규모 콜드 부팅이 발생하고 이러한 방식으로 실행하지 않는 경우 이 변경 내용으로 인해 크게 개선될 수 있습니다.

### <a name="connection-limits"></a>연결 제한

Azure Functions 응용 프로그램에서 서비스별 클라이언트를 사용 하는 경우 모든 함수 호출을 사용 하 여 새 클라이언트를 만들지 마세요. 대신 전역 범위에서 단일 정적 클라이언트를 만듭니다. 자세한 내용은 [Azure Functions에서 연결 관리](manage-connections.md)를 참조 하세요.

### <a name="use-async-and-await"></a>`async`및 사용`await`

JavaScript에서 Azure Functions를 작성 하는 경우 및 키워드를 사용 하 여 코드를 작성 해야 합니다 `async` `await` . 콜백이 나를 사용 하는 대신 및를 사용 하 여 코드를 작성 `async` `await` `.then` `.catch` 하면 두 가지 일반적인 문제를 방지할 수 있습니다.
 - [Node.js 프로세스를 중단](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)하는 catch 되지 않은 예외를 throw 하 여 다른 함수 실행에 영향을 줄 수 있습니다.
 - 올바르게 대기 않는 비동기 호출로 인해 발생 한 잘못 된 동작 (예: 컨텍스트별의 로그 누락)입니다.

아래 예제에서는 `fs.readFile` 두 번째 매개 변수로 오류 중심 콜백 함수를 사용 하 여 비동기 메서드를 호출 합니다. 이 코드는 위에서 언급 한 문제를 모두 발생 시킵니다. 올바른 범위에서 명시적으로 catch 되지 않은 예외는 전체 프로세스 (문제 #1)를 중단 합니다. `context.done()`콜백 함수의 범위 외부에서를 호출 하면 파일을 읽기 전에 함수 호출이 종료 될 수 있습니다 (문제 #2). 이 예에서는를 `context.done()` 너무 일찍 호출 하면로 시작 하는 로그 항목이 누락 `Data from file:` 됩니다.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

`async`및 키워드를 사용 `await` 하면 이러한 오류를 방지할 수 있습니다. Node.js 유틸리티 함수를 사용 하 여 [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) 오류 우선 콜백 스타일 함수를 대기 가능 함수로 설정 해야 합니다.

아래 예제에서 함수 실행 중에 throw 된 처리 되지 않은 예외는 예외를 발생 시킨 개별 호출에만 실패 합니다. `await`키워드는 다음 단계를 `readFileAsync` 완료 한 후에만 실행 되는 단계를 의미 합니다 `readFile` . `async`및를 사용 하 `await` 는 경우에도 콜백을 호출할 필요가 없습니다 `context.done()` .

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

+ [Azure Functions에 대한 모범 사례](functions-best-practices.md)
+ [Azure Functions 개발자 참조](functions-reference.md)
+ [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

[' func azure functionapp 게시 ']: functions-run-local.md#project-file-deployment