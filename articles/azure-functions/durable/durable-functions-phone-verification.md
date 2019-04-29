---
title: 지속성 함수의 인간 상호 작용 및 시간 제한 - Azure
description: Azure Functions의 지속성 함수 확장에서 인간 상호 작용 및 시간 제한을 처리하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: cf43e29e967ee6f920eb38feb9c73d70f9621ea4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123317"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>지속성 함수의 인간 상호 작용 - 전화 확인 샘플

이 샘플에서는 인간 상호 작용이 포함된 [지속성 함수](durable-functions-overview.md) 오케스트레이션을 작성하는 방법을 보여 줍니다. 실제 사람이 자동화된 프로세스에 참여할 때마다 프로세스에서 당사자에게 알림을 보내고 응답을 비동기적으로 받을 수 있어야 합니다. 사람이 사용할 수 없는 가능성도 허용해야 합니다. (이 마지막 부분은 시간 제한이 중요한 부분입니다.)

이 샘플에서는 SMS 기반 전화 확인 시스템을 구현합니다. 이러한 유형의 흐름은 고객의 전화 번호를 확인할 때 또는 MFA(다단계 인증)를 위해 자주 사용됩니다. 이는 전체 구현이 몇 가지 작은 함수를 사용하여 수행되므로 강력한 예제입니다. 데이터베이스와 같은 외부 데이터 저장소는 필요하지 않습니다.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>시나리오 개요

전화 확인은 애플리케이션의 최종 사용자가 스패머가 아니고 자신이 언급되는 당사자인지 확인하는 데 사용됩니다. 다단계 인증은 해커로부터 사용자 계정을 보호하기 위한 일반적인 사용 사례입니다. 사용자 고유의 전화 확인을 구현하는 챌린지에는 사람과의 **상태 저장 상호 작용**이 필요하다는 것입니다. 일반적으로 최종 사용자에게는 일부 코드(예: 4자리 숫자)가 제공되며 **적절한 시간 내에** 응답해야 합니다.

일반적인 Azure Functions는 다른 플랫폼의 다른 많은 클라우드 엔드포인트와 마찬가지로 상태 비저장이므로, 이러한 유형의 상호 작용에는 데이터베이스 또는 일부 다른 영구 저장소에서 외부적으로 상태를 관리하는 작업이 명시적으로 포함됩니다. 또한 상호 작용은 함께 조정될 수 있는 여러 함수로 분할되어야 합니다. 예를 들어 코드를 결정하고, 어딘가에 유지하고, 사용자의 전화로 보내는 함수가 하나 이상 필요합니다. 또한 사용자로부터 응답을 받기 위해 다른 함수가 하나 이상 필요하며, 코드 유효성 검사를 수행하기 위해 어떤 방식으로든 원래 함수 호출에 다시 매핑해야 합니다. 시간 제한은 보안을 유지하는 중요한 요소이기도 하지만, 신속하게 상당히 복잡해질 수 있습니다.

지속성 함수를 사용하면 이 시나리오의 복잡성이 크게 줄어듭니다. 이 샘플에서 볼 수 있듯이 오케스트레이터 함수는 외부 데이터 저장소를 포함하지 않고 상태 저장 상호 작용을 쉽게 관리할 수 있습니다. 오케스트레이터 함수는 *지속적*이므로 이러한 대화형 흐름도 매우 안정적입니다.

## <a name="configuring-twilio-integration"></a>Twilio 통합 구성

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>함수

이 문서에서는 샘플 앱의 다음 함수에 대해 설명합니다.

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

다음 섹션에서는 C# 스크립팅 및 JavaScript에 사용되는 구성 및 코드에 대해 설명합니다. Visual Studio 개발을 위한 코드는 이 문서의 끝 부분에 나와 있습니다.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>SMS 확인 오케스트레이션(Visual Studio Code 및 Azure Portal 샘플 코드)

**E4_SmsPhoneVerification** 함수는 오케스트레이터 함수에 표준 *function.json*을 사용합니다.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

다음은 이 함수를 구현하는 코드입니다.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

시작되면 이 오케스트레이터 함수에서 다음을 수행합니다.

1. SMS 알림을 *보낼* 전화 번호를 가져옵니다.
2. **E4_SendSmsChallenge**를 호출하여 사용자에게 SMS 메시지를 보내고 예상되는 4자리 챌린지 코드를 다시 반환합니다.
3. 현재 시간에서 90초를 트리거하는 지속성 타이머를 만듭니다.
4. 타이머와 병행하여 사용자로부터 **SmsChallengeResponse** 이벤트를 기다립니다.

사용자는 4자리 코드가 있는 SMS 메시지를 받습니다. 확인 프로세스를 완료하기 위해 동일한 4자리 코드를 오케스트레이터 함수 인스턴스로 다시 보내는 데 90초가 걸립니다. 잘못된 코드를 제출하면 동일한 90초 간격으로 3번의 시도를 추가로 얻을 수 있습니다.

> [!NOTE]
> 처음에는 명확하지 않을 수도 있지만 이 오케스트레이터 함수는 완전히 결정적입니다. 이는 `CurrentUtcDateTime`(.NET) 및 `currentUtcDateTime`(JavaScript) 속성이 타이머 만료 시간을 계산하는 데 사용되며, 이러한 속성이 오케스트레이터 코드의 이 시점에 있는 모든 재생에서 동일한 값을 반환하기 때문입니다. 이 경우 `Task.WhenAny`(.NET) 또는 `context.df.Task.any`(JavaScript)를 반복적으로 호출할 때마다 동일한 `winner`가 발생하도록 하는 것이 중요합니다.

> [!WARNING]
> 챌린지 응답이 수락되고 타이머가 더 이상 만료될 필요가 없으면 위의 예제와 같이 [타이머를 취소](durable-functions-timers.md)하는 것이 중요합니다.

## <a name="send-the-sms-message"></a>SMS 메시지 전송

**E4_SendSmsChallenge** 함수는 Twilio 바인딩을 사용하여 4자리 코드가 있는 SMS 메시지를 최종 사용자에게 보냅니다. *function.json*은 다음과 같이 정의됩니다.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

다음은 4자리 챌린지 코드를 생성하고 SMS 메시지를 보내는 코드입니다.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

이 **E4_SendSmsChallenge** 함수는 프로세스가 충돌하거나 재생되는 경우에도 한 번만 호출됩니다. 이는 최종 사용자가 여러 개의 SMS 메시지를 받지 않으려고 하기 때문에 좋습니다. `challengeCode` 반환 값은 자동으로 유지되므로 오케스트레이터 함수는 항상 올바른 코드를 인식하고 있습니다.

## <a name="run-the-sample"></a>샘플 실행

샘플에 포함된 HTTP 트리거 함수를 사용하여 다음 HTTP POST 요청을 전송함으로써 오케스트레이션을 시작할 수 있습니다.

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

오케스트레이터 함수는 제공된 전화 번호를 받는 즉시 임의로 생성된 4자리 확인 코드&mdash;(예: *2168*)가 있는 SMS 메시지를 보냅니다. 그런 다음 이 함수는 90초 동안 응답을 기다립니다.

코드를 사용하여 회신하려면 다른 함수 내에서 [`RaiseEventAsync`(.NET) 또는 `raiseEvent`(JavaScript)](durable-functions-instance-management.md)를 사용하거나 위의 202 응답에서 참조된 **sendEventUrl** HTTP POST 웹후크를 호출하여 `{eventName}`을 `SmsChallengeResponse` 이벤트 이름으로 바꾸면 됩니다.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

타이머가 만료되기 전에 보내면 오케스트레이션이 완료되고 `output` 필드가 `true`로 설정되어 성공적인 확인을 나타냅니다.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

타이머가 만료되도록 하거나 잘못된 코드를 4번 입력하면 상태를 쿼리하고 전화 확인에 실패했음을 나타내는 `false` 오케스트레이션 함수 출력을 확인할 수 있습니다.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio 샘플 코드

다음은 Visual Studio 프로젝트의 단일 C# 파일로서의 오케스트레이션입니다.

> [!NOTE]
> 아래의 샘플 코드를 실행하려면 `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget 패키지를 설치해야 합니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>다음 단계

이 예제는 지속성 함수의 고급 기능, 특히 `WaitForExternalEvent` 및 `CreateTimer`에 대해 보여주었습니다. 이러한 기능을 `Task.WaitAny`와 결합하여 신뢰할 수 있는 시간 제한 시스템을 구현하는 방법을 살펴보았습니다. 이 시스템은 종종 실제 사람들과 상호 작용하는 데 유용합니다. 특정 항목을 자세히 다루는 일련의 문서를 읽고 지속성 함수를 사용하는 방법에 대해 자세히 배울 수 있습니다.

> [!div class="nextstepaction"]
> [시리즈의 첫 번째 문서로 이동](durable-functions-bindings.md)
