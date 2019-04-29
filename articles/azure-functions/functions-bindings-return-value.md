---
title: Azure 함수에서 반환 값 사용
description: Azure Functions에 대 한 반환 값을 관리 하는 방법을 알아봅니다
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 01/14/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437721"
---
# <a name="using-the-azure-function-return-value"></a>Azure 함수 반환 값을 사용 하 여

이 문서에서는 반환 값을 작업 하는 방법을 설명 하는 함수 내에서.

반환 값이 있는 언어에서 함수를 바인딩할 수 있습니다 [출력 바인딩을](./functions-triggers-bindings.md#binding-direction) 반환 값:

- C# 클래스 라이브러리에서 출력 바인딩 특성을 메서드 반환 값에 적용합니다.
- 다른 언어에서 *function.json*의 `name` 속성을 `$return`에 설정합니다.

여러 개의 출력 바인딩이 있으면 둘 중 하나에 대한 반환 값을 사용합니다.

C# 및 C# 스크립트에서 데이터를 출력 바인딩으로 보내는 또 다른 방법은 `out` 매개 변수 및 [수집기 개체](functions-reference-csharp.md#writing-multiple-output-values)입니다.

반환 값 사용 방법을 보여주는 언어별 예제를 참조하세요.

- [C#](#c-example)
- [C# 스크립트(.csx)](#c-script-example)
- [F#](#f-example)
- [JavaScript](#javascript-example)
- [Python](#python-example)

## <a name="c-example"></a>C# 예제

다음은 출력 바인딩에 대한 반환 값을 사용하는 C# 코드이며, 그 뒤에 비동기 예제가 나와 있습니다.

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

## <a name="c-script-example"></a>C# 스크립트 예제

*function.json* 파일의 출력 바인딩은 다음과 같습니다.

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

다음은 C# 스크립트 코드로, 그 뒤에 비동기 예제가 나와 있습니다.

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

## <a name="f-example"></a>F# 예제

*function.json* 파일의 출력 바인딩은 다음과 같습니다.

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

F# 코드는 다음과 같습니다.

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="javascript-example"></a>JavaScript 예제

*function.json* 파일의 출력 바인딩은 다음과 같습니다.

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

JavaScript에서 반환 값은 `context.done`에 대한 두 번째 매개 변수로 이어집니다.

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Python 예제

*function.json* 파일의 출력 바인딩은 다음과 같습니다.

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
다음은 Python 코드입니다.

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 바인딩 오류 처리](./functions-bindings-errors.md)

