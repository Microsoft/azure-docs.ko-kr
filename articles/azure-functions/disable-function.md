---
title: Azure Functions에서 함수를 사용하지 않도록 설정하는 방법
description: Azure Functions 1.x 및 2.x에서 함수를 사용하지 않도록 설정하고 사용하도록 설정하는 방법을 알아봅니다.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
origin.date: 07/24/2018
ms.date: 08/31/2018
ms.author: v-junlch
ms.openlocfilehash: a32b4815a2716428ceeec034ddc5589e3aa062e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710582"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Azure Functions에서 함수를 사용하지 않도록 설정하는 방법

이 문서에서는 Azure Functions에서 함수를 사용하지 않도록 설정하는 방법을 설명합니다. 함수를 *사용하지 안도록 설정*하는 것은 함수에 대해 정의된 자동 트리거를 런타임에서 무시하도록 하는 것입니다. 이렇게 하는 방식은 런타임 버전 및 프로그래밍 언어에 따라 다릅니다.

- Functions 1.x
  - 스크립팅 언어
  - C# 클래스 라이브러리
- Functions 2.x
  - 모든 언어에 대한 한 가지 방법
  - C# 클래스 라이브러리에 대한 선택적 방법

## <a name="functions-1x---scripting-languages"></a>Functions 1.x - 스크립팅 언어

C# 스크립트 및 JavaScript와 같은 스크립팅 언어의 경우 *function.json* 파일의 `disabled` 속성을 사용하여 함수를 트리거하지 않도록 런타임에 알립니다. 이 속성은 `true` 또는 앱 설정 이름으로 설정할 수 있습니다.

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
또는 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

두 번째 예제에서는 IS_DISABLED라고 명명되고 `true` 또는 1로 설정된 앱 설정이 있는 경우 함수가 사용하지 않도록 설정됩니다.

Azure Portal에서 파일을 편집하거나 함수의 **관리** 탭에서 **함수 상태** 스위치를 사용할 수 있습니다. 포털 스위치는 *function.json* 파일을 변경하면 작동됩니다.

![함수 상태 스위치](./media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1.x - C# 클래스 라이브러리

Functions 1.x 클래스 라이브러리에서 함수가 트리거되지 못하게 하려면 `Disable` 특성을 사용합니다. 다음 예제에 표시된 것처럼 생성자 매개 변수 없이도 이 특성을 사용할 수 있습니다.

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

생성자 매개 변수 없는 특성은 함수의 사용 안 함 상태를 변경하려면 프로젝트를 다시 컴파일하고 다시 배포해야 합니다. 특성을 사용하는 보다 유연한 방법은 다음 예제에 표시된 대로 부울 앱 설정을 참조하는 생성자 매개 변수를 포함하는 것입니다.

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

이 메서드를 사용하면 다시 컴파일하거나 다시 배포하지 않고 앱 설정을 변경하여 함수를 사용하지 않도록 설정할 수 있습니다. 앱 설정을 바꾸면 사용 안 함 상태 변경을 즉시 인식할 수 있도록 함수 앱이 다시 시작하게 됩니다.

> [!IMPORTANT]
> `Disabled` 특성은 클래스 라이브러리 함수를 사용하지 않도록 설정하는 유일한 방법입니다. 클래스 라이브러리 함수에 대해 생성된 *function.json* 파일은 직접 편집할 수 없게 되어 있습니다. 해당 파일을 편집하는 경우 무엇을 하든지 `disabled` 속성에 영향을 주지 않습니다.
>
> **함수 상태** 스위치는 *function.json* 파일을 변경하여 작동하므로 **관리** 탭의 해당 스위치에도 동일하게 적용됩니다.
>
> 또한 포털은 함수가 사용 안 함이 아닌 경우 사용 안 함으로 나타낼 수 있습니다.



## <a name="functions-2x---all-languages"></a>Functions 2.x - 모든 언어

Functions 2.x에서 앱 설정을 사용하여 함수를 사용하지 않도록 설정합니다. 예를 들어 `QueueTrigger`라는 함수를 사용하지 않도록 설정하려면 `AzureWebJobs.QueueTrigger.Disabled`라는 앱 설정을 만들어 `true`로 설정합니다. 함수를 사용하도록 설정하려면 앱 설정을 `false`로 설정합니다. 함수의 **관리** 탭에서 **함수 상태** 스위치를 사용할 수도 있습니다. 스위치는 `AzureWebJobs.<functionname>.Disabled` 앱 설정을 만들고 삭제함으로써 작동됩니다.

![함수 상태 스위치](./media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2.x - C# 클래스 라이브러리

Functions 2.x 클래스 라이브러리에서 모든 언어에 대해 작동하는 메서드를 사용하는 것이 좋습니다. 그러나 원한다면 [Functions 1.x에서처럼 사용 안 함 특성을 사용](#functions-1x---c-class-libraries)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 자동 트리거를 사용하지 않도록 설정하는 방법을 설명합니다. 트리거에 대한 자세한 내용은 [트리거 및 바인딩](functions-triggers-bindings.md)을 참조하세요.

