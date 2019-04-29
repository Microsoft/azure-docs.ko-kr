---
title: 지속성 함수의 모니터 - Azure
description: Azure Functions의 지속성 함수 확장을 사용하여 상태 모니터를 구현하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 9be062ec42f054832225c17a65b06e47dbcbe990
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123487"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>지속성 함수의 모니터 시나리오 - 날씨 관찰 앱 샘플

모니터링 패턴은 워크플로의 유연한 되풀이(예: 특정 조건이 충족될 때까지 폴링) 프로세스를 말합니다. 이 문서에서는 [지속성 함수](durable-functions-overview.md)를 사용하여 모니터링을 구현하는 샘플에 대해 설명합니다.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>시나리오 개요

이 샘플은 특정 위치의 현재 기상 조건을 모니터링하고 하늘이 맑으면 SMS로 사용자에게 알려줍니다. 타이머로 트리거되는 일반 함수를 사용하여 날씨를 확인하고 알림을 보낼 수 있습니다. 단, 이러한 방식의 한 가지 문제점은 **수명 관리**입니다. 알림을 하나만 보내야 하는 경우에는 맑은 날씨가 감지된 후 모니터를 비활성화해야 합니다. 모니터링 패턴은 다른 이점과 함께 자체 실행을 종료 할 수 있습니다.

* 모니터는 간격을 두고 실행되며 일정에 따라 실행되는 것이 아닙니다. 타이머 트리거는 1시간마다 실행(*run*)되고 모니터는 작업 사이에 한 시간을 대기합니다(*wait*). 모니터의 작업은 지정되지 않는 한 겹치지 않습니다. 이는 장기 실행되는 작업에 중요합니다.
* 모니터에 동적 간격을 설정할 수 있으며, 대기 시간은 조건에 따라 변경될 수 있습니다.
* 모니터는 조건이 충족되면 종료되거나 다른 프로세스에 의해 종료될 수 있습니다.
* 모니터에 매개 변수를 사용할 수 있습니다. 이 샘플에서는 요청 받은 위치와 전화 번호에 동일한 기상 모니터링 프로세스를 적용하는 방법을 보여줍니다.
* 모니터는 확장성이 있습니다. 각 모니터는 오케스트레이션 인스턴스이기 때문에 새 함수를 만들거나 코드를 더 정의하지 않고도 다수의 모니터를 만들 수 있습니다.
* 모니터는 보다 큰 워크플로에 쉽게 통합됩니다. 모니터는 더 복잡한 오케스트레이션 함수 또는 [ 하위 오케스트레이션](durable-functions-sub-orchestrations.md)의 한 섹션이 될 수 있습니다.

## <a name="configuring-twilio-integration"></a>Twilio 통합 구성

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Weather Underground 통합 구성

이 샘플에는 Weather Underground API를 사용하여 특정 위치의 현재 기상 조건을 확인하는 작업이 포함됩니다.

가장 먼저 필요한 것은 Weather Underground 계정입니다. [https://www.wunderground.com/signup](https://www.wunderground.com/signup)에서 무료로 만들 수 있습니다. 계정이 있으면 API 키를 확보해야 합니다. [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api)에 방문하여 Key Settings(키 설정)를 선택하면 합니다. Stratus Developer 계획은 무료이며 이 샘플을 실행하기에 충분합니다.

API 키가 확보되면 함수 앱에 다음 **앱 설정**을 추가합니다.

| 앱 설정 이름 | 값 설명 |
| - | - |
| **WeatherUndergroundApiKey**  | Weather Underground API 키입니다. |

## <a name="the-functions"></a>함수

이 문서에서는 샘플 앱의 다음 함수에 대해 설명합니다.

* `E3_Monitor`: `E3_GetIsClear`를 주기적으로 호출하는 오케스트레이터 함수입니다. `E3_GetIsClear`가 true를 반환하면 `E3_SendGoodWeatherAlert`를 호출합니다.
* `E3_GetIsClear`: 특정 위치의 현재 기상 조건을 확인하는 작업 함수입니다.
* `E3_SendGoodWeatherAlert`: Twilio를 통해 SMS 메시지를 보내는 작업 함수입니다.

다음 섹션에서는 C# 스크립팅 및 JavaScript에 사용되는 구성 및 코드에 대해 설명합니다. Visual Studio 개발을 위한 코드는 이 문서의 끝 부분에 나와 있습니다.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>날씨 모니터링 오케스트레이션(Visual Studio Code 및 Azure Portal 샘플 코드)

**E3_Monitor** 함수는 오케스트레이터 함수에 표준 *function.json*을 사용합니다.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

다음은 이 함수를 구현하는 코드입니다.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

이 오케스트레이터 함수는 다음 작업을 수행합니다.

1. 모니터링할 위치와 SMS 알림을 보낼 전화 번호로 구성된 **MonitorRequest**를 가져옵니다.
2. 모니터의 만료 시간을 결정합니다. 간결함을 위해 이 샘플에서는 하드 코드된 값을 사용합니다.
3. **E3_GetIsClear**를 호출하여 요청 받은 위치에 하늘이 맑은지 확인합니다.
4. 날씨가 맑으면 **E3_SendGoodWeatherAlert**를 호출하여 요청 받은 전화 번호로 SMS 알림을 보냅니다.
5. 다음 폴링 간격에서 오케스트레이션을 다시 시작하도록 지속성 타이머를 만듭니다. 간결함을 위해 이 샘플에서는 하드 코드된 값을 사용합니다.
6. [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime)(C#) 또는 `currentUtcDateTime`(JavaScript)이 모니터의 만료 시간을 지나거나 SMS 알림이 전송될 때까지 계속 실행합니다.

**MonitorRequests**를 여러 개 보내면 오케스트레이터 인스턴스를 동시에 여러 개 실행할 수 있습니다. 모니터링할 위치와 SMS 알림을 보낼 전화 번호를 지정할 수 있습니다.

## <a name="strongly-typed-data-transfer-net-only"></a>강력한 형식의 데이터 전송(.NET만 해당)

오케스트레이터에는 여러 개의 데이터가 필요하므로 C# 및 C# 스크립트에서 강력한 형식의 데이터 전송에는 [공유 POCO 개체](../functions-reference-csharp.md#reusing-csx-code)가 사용됩니다. [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

JavaScript 샘플은 매개 변수로 기본 JSON 개체를 사용합니다.

## <a name="helper-activity-functions"></a>도우미 작업 함수

다른 샘플과 마찬가지로 도우미 작업 함수는 `activityTrigger` 트리거 바인딩을 사용하는 일반 함수입니다. **E3_GetIsClear** 함수는 Weather Underground API를 사용하여 현재 기상 조건을 가져와서 하늘이 맑은지를 판단합니다. *function.json*은 다음과 같이 정의됩니다.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

그리고 구현은 다음과 같습니다. 데이터 전송에 사용되는 POCO와 마찬가지로 API 호출을 처리하고 응답 JSON을 구문 분석하는 논리는 C#에서 공유 클래스로 추상화됩니다. [Visual Studio 샘플 코드](#run-the-sample)의 일부로 찾을 수 있습니다.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

**E3_SendGoodWeatherAlert** 함수는 Twilio 바인딩을 사용하여 최종 사용자에게 걷기 좋은 시간임을 알리는 SMS 메시지를 보냅니다 *function.json*은 간단합니다.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

SMS 메시지를 보내는 코드는 다음과 같습니다.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>샘플 실행

샘플에 포함된 HTTP 트리거 함수를 사용하여 다음 HTTP POST 요청을 전송함으로써 오케스트레이션을 시작할 수 있습니다.

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** 인스턴스가 시작되어 요청 받은 위치의 현재 기상 조건을 쿼리합니다. 날씨가 맑으면 알림을 보낼 작업 함수를 호출하고 그렇지 않으면 타이머를 설정합니다. 타이머가 만료되면 오케스트레이션이 다시 시작됩니다.

Azure Functions 포털의 함수 로그를 검토하여 오케스트레이션의 작업을 볼 수 있습니다.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

오케스트레이션은 시간 제한에 도달하거나 맑은 하늘이 감지되면 [종료](durable-functions-instance-management.md)됩니다. 또 다른 함수 내의 `TerminateAsync`(.NET) 또는 `terminate`(JavaScript)를 사용하거나 위의 202 응답에서 참조된 **terminatePostUri** HTTP POST 웹후크를 호출하여 `{text}`를 종료 이유로 대체할 수도 있습니다.

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio 샘플 코드

다음은 Visual Studio 프로젝트의 단일 C# 파일로서의 오케스트레이션입니다.

> [!NOTE]
> 아래의 샘플 코드를 실행하려면 `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget 패키지를 설치해야 합니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>다음 단계

이 샘플은 지속성 함수가 [지속성 타이머](durable-functions-timers.md)와 조건부 논리를 사용하여 외부 소스의 상태를 모니터링하는 방법을 보여줍니다. 다음 샘플에서는 외부 이벤트 및 [지속형 타이머](durable-functions-timers.md)를 사용하여 인간 상호 작용을 처리하는 방법을 보여 줍니다.

> [!div class="nextstepaction"]
> [인간 상호 작용 샘플 실행](durable-functions-phone-verification.md)
