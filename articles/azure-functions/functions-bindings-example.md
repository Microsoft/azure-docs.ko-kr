---
title: Azure Functions 트리거 및 바인딩 예제
description: Azure 함수 바인딩을 구성 하는 방법을 알아봅니다
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 6d5f9b171a4efc5e52d281655de143ac9d40d437
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437891"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Functions 트리거 및 바인딩 예제

이 문서에 구성 하는 방법을 보여 줍니다.는 [트리거 및 바인딩을](./functions-triggers-bindings.md) Azure Function에서.

Azure Queue 스토리지에 새 메시지가 나타날 때마다 Azure Table Storage에 새 행을 쓰려는 경우를 가정하겠습니다. 이 시나리오는 Azure Queue 스토리지 트리거 및 Azure Table Storage 출력 바인딩을 사용하여 구현할 수 있습니다. 

다음은 이 시나리오에 대한 *function.json* 파일입니다. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` 배열의 첫 번째 요소는 Queue Storage 트리거입니다. `type` 및 `direction` 속성은 트리거를 식별합니다. `name` 속성은 큐 메시지 콘텐츠를 받는 함수 매개 변수를 식별합니다. 모니터링하는 큐 이름은 `queueName`에 있으며 연결 문자열은 `connection`으로 식별되는 앱 설정에 있습니다.

`bindings` 배열의 두 번째 요소는 Azure Table Storage 출력 바인딩입니다. `type` 및 `direction` 속성은 바인딩을 식별합니다. `name` 속성은 함수가 새 테이블 행을 제공하는 방법을 지정하며 이 경우 함수 반환 값을 사용합니다. 테이블의 이름은 `tableName`에 있으며 연결 문자열은 `connection`으로 식별되는 앱 설정에 있습니다.

Azure Portal에서 *function.json*의 내용을 보고 편집하려면 함수의 **통합** 탭에서 **고급 편집기**를 클릭합니다.

> [!NOTE]
> `connection`의 값은 연결 문자열 자체가 아닌 연결 문자열을 포함하는 앱 설정의 이름입니다. 바인딩은 *function.json*에 서비스 비밀이 포함되지 않은 모범 사례를 실행하기 위해 앱 설정에 저장된 연결 문자열을 사용합니다.

## <a name="c-script-example"></a>C# 스크립트 예제

이 트리거 및 바인딩을 사용하는 C# 스크립트 코드는 다음과 같습니다. 큐 메시지 콘텐츠를 제공하는 매개 변수 이름은 `order`이며 *function.json*의 `name` 속성 값은 `order`이므로 이 이름이 필요합니다. 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript 예제

동일 *function.json* JavaScript 함수를 사용 하 여 파일을 사용할 수 있습니다.

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>클래스 라이브러리 예제

클래스 라이브러리에서 동일한 트리거 및 바인딩 정보(큐 및 테이블 이름, 저장소 계정, 입력 및 출력에 대한 함수 매개 변수)는 function.json 파일 대신 특성에 의해 제공됩니다. 예를 들면 다음과 같습니다.

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

이제 큐에 데이터를 출력 하는 Azure Table storage에 의해 트리거되는 작업 함수입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 바인딩 식 패턴](./functions-bindings-expressions-patterns.md)

