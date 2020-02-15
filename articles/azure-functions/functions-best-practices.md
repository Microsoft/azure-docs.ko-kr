---
title: Azure Functions에 대한 모범 사례
description: Azure Functions에 대한 모범 사례 및 패턴에 알아봅니다.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd52c4cc5cd9d4c01ddb355bdfb28d0952e65759
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210262"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Azure Functions의 성능 및 안정성 최적화

이 문서에서는 [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 함수 앱의 성능 및 안정성을 개선하기 위한 지침을 제공합니다.  

## <a name="general-best-practices"></a>일반 모범 사례

Azure Functions를 사용하여 서버가 없는 솔루션을 빌드하고 설계하는 방법의 모범 사례는 다음과 같습니다.

### <a name="avoid-long-running-functions"></a>장기 실행 함수 방지

큰 장기 실행 함수는 예기치 않은 시간 초과 문제를 발생시킬 수 있습니다. 지정 된 호스팅 계획에 대 한 시간 제한에 대해 자세히 알아보려면 [함수 앱 시간 제한 기간](functions-scale.md#timeout)을 참조 하세요. 

함수는 많은 node.js 종속성으로 인해 커질 수 있습니다. 또한 종속성을 가져올 때 로드 시간이 증가하여 예기치 않은 시간 초과가 발생할 수 있습니다. 종속성은 명시적 및 암시적으로 로드됩니다. 코드를 통해 로드되는 단일 모듈은 자체 추가 모듈을 로드할 수 있습니다. 

큰 함수를 더 작은 함수 집합으로 리팩터링할 때마다 함께 작동하고 빠른 응답을 반환합니다. 예를 들어 webhook 또는 HTTP 트리거 함수는 특정 시간 제한 내에서 승인 응답이 필요할 수 있습니다. 웹 후크가 즉각적인 응답을 요구 하는 것이 일반적입니다. HTTP 트리거 페이로드를 큐 트리거 함수에 의해 처리되도록 큐에 전달할 수 있습니다. 이 방법을 사용 하면 실제 작업을 지연 하 고 즉각적인 응답을 반환할 수 있습니다.


### <a name="cross-function-communication"></a>함수 통신 교차

[지속형 함수](durable/durable-functions-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)는 여러 함수 간에 상태 전환 및 통신을 관리하도록 빌드됩니다.

Durable Functions 또는 Logic Apps를 사용 하 여 여러 함수와 통합 하지 않는 경우 함수 간 통신에 저장소 큐를 사용 하는 것이 가장 좋습니다. 주된 이유는 저장소 큐가 다른 저장소 옵션 보다 더 저렴 하 고 더 쉽게 프로 비전 하는 것입니다. 

스토리지 큐에 있는 개별 메시지 크기는 64KB로 제한됩니다. 함수 간에 더 큰 메시지를 전달해야 하는 경우 Azure Service Bus 큐를 사용하면 표준 계층에서는 최대 256KB, 프리미엄 계층에서는 최대 1MB의 메시지를 지원할 수 있습니다.

Service Bus 토픽은 메시지를 처리하기 전에 필터링해야 하는 경우에 유용합니다.

이벤트 허브는 고용량 통신을 지원하는 데 유용합니다.


### <a name="write-functions-to-be-stateless"></a>상태 비저장 함수 작성 

함수는 가능하면 상태 비저장이며 idempotent여야 합니다. 필요한 모든 상태 정보를 사용자 데이터에 연결합니다. 예를 들어 처리할 주문에 연결된 `state` 멤버가 있을 수 있습니다. 함수는 주문을 해당 상태에 따라 처리하며 함수 자체는 비저장 상태로 남아 있을 수 있습니다. 

Idempotent 함수는 특히 타이머 트리거 사용이 권장됩니다. 예를 들어, 하루에 한 번 실행 해야 하는 항목이 있는 경우 동일한 결과를 사용 하 여 하루 중에 언제 든 지 실행할 수 있도록 작성 합니다. 특정 날짜에 대 한 작업이 없으면 함수를 종료할 수 있습니다. 또한 이전 실행이 완료되지 못한 경우 다음 실행은 중단되었던 부분으로 돌아가야 합니다.


### <a name="write-defensive-functions"></a>방어적 함수 작성

함수에는 언제든지 예외가 발생할 수 있다고 가정합니다. 다음 실행 동안 이전 실패 지점에서 계속할 수 있는 기능을 넣어 함수를 설계합니다. 다음과 같은 작업이 필요한 시나리오를 고려하세요.

1. 데이터베이스에서 1만 행을 쿼리 합니다.
2. 앞으로 처리할 각 행에 대한 큐 메시지를 만듭니다.
 
시스템의 복잡성에 따라 다음과 같은 작업을 수행 해야 합니다. 관련 된 다운스트림 서비스가 잘못 동작 하거나, 네트워킹 중단이 발생 하거나, 할당량 제한에 도달 했을 수 있습니다. 이러한 모든 것은 언제 든 지 함수에 영향을 줄 수 있습니다. 함수가 이에 대비할 수 있도록 설계해야 합니다.

이러한 항목 중 5,000개를 처리를 위해 큐에 삽입한 후 오류가 발생한다면 코드는 어떻게 반응할까요? 사용자가 완료한 집합에서 항목을 추적합니다. 다른 방법으로 다음 번에 해당 항목을 삽입할 수도 있습니다. 이러한 이중 삽입은 작업 흐름에 심각한 영향을 줄 수 있으므로 함수를 [idempotent](functions-idempotent.md)합니다. 

큐 항목을 이미 처리한 경우 함수는 수행되지 않습니다.

Azure Functions 플랫폼에서 사용하는 구성 요소를 위해 이미 제공된 방어 수단을 활용하세요. 예를 들어 **Azure Storage 큐 트리거 및 바인딩**을 위한 설명서에서 [포이즌 큐 메시지 처리](functions-bindings-storage-queue.md#trigger---poison-messages)를 참조하세요. 

## <a name="scalability-best-practices"></a>확장성 모범 사례

함수 앱의 인스턴스가 확장 되는 방법에 영향을 주는 여러 요인이 있습니다. 자세한 내용은 [함수 크기 조정](functions-scale.md)의 설명서에서 제공됩니다.  함수 앱에 최적의 확장성을 보장하는 몇 가지 모범 사례는 다음과 같습니다.

### <a name="share-and-manage-connections"></a>연결 공유 및 관리

가능 하면 외부 리소스에 대 한 연결을 다시 사용 합니다. [Azure Functions에서 연결을 관리하는 방법](./manage-connections.md)을 참조하세요.

### <a name="avoid-sharing-storage-accounts"></a>저장소 계정 공유 방지

함수 앱을 만들 때이를 저장소 계정과 연결 해야 합니다. 저장소 계정 연결은 [Azurewebjobsstorage 응용 프로그램 설정](./functions-app-settings.md#azurewebjobsstorage)에서 유지 관리 됩니다. 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>동일한 함수 앱에서 테스트와 프로덕션 코드의 혼합 금지

함수 앱 공유 리소스 내에서 작용합니다. 예를 들어 메모리는 공유됩니다. 프로덕션 환경에서 함수 앱을 사용하는 경우 테스트 관련 함수 및 리소스를 추가하지 마세요. 프로덕션 코드를 실행하는 동안 예기치 않은 오버 헤드가 발생할 수 있습니다.

프로덕션 함수 앱에서 로드하는 것에 주의하세요. 메모리는 앱에서 각 함수가 사용하는 것의 평균으로 계산됩니다.

여러 .NET 함수에서 공유 어셈블리를 참조 하는 경우에는 공통 공유 폴더에 저장 합니다. 그렇지 않으면 함수 간에 다르게 동작 하는 동일한 이진 파일의 여러 버전을 실수로 배포할 수 있습니다.

성능에 부정적인 영향을 주는 프로덕션 코드에서 자세한 정보 로깅을 사용 하지 마세요.

### <a name="use-async-code-but-avoid-blocking-calls"></a>비동기 코드는 사용, 호출 차단 방지

비동기 프로그래밍은 특히 i/o 작업을 차단 하는 경우 권장 되는 모범 사례입니다.

에서 C#항상 `Result` 속성을 참조 하거나 `Task` 인스턴스에서 `Wait` 메서드를 호출 하지 않도록 합니다. 이 방법은 스레드를 소진시킬 수 있습니다.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>여러 작업자 프로세스 사용

기본적으로 함수에 대 한 모든 호스트 인스턴스는 단일 작업자 프로세스를 사용 합니다. 특히 Python과 같은 단일 스레드 런타임을 사용 하 여 성능을 향상 시키려면 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 를 사용 하 여 호스트 당 작업자 프로세스 수를 늘립니다 (최대 10 개). 그런 다음 Azure Functions는 이러한 작업자 간에 동시 함수 호출을 균등 하 게 분산 하려고 시도 합니다. 

FUNCTIONS_WORKER_PROCESS_COUNT는 요구를 충족 하도록 응용 프로그램을 확장할 때 함수가 만드는 각 호스트에 적용 됩니다. 

### <a name="receive-messages-in-batch-whenever-possible"></a>가능하면 항상 일괄 처리로 메시지를 수신합니다.

Event Hub와 같은 일부 트리거는 단일 호출에서 일괄 처리 메시지를 수신할 수 있습니다.  메시지를 일괄 처리하면 성능이 향상됩니다.  `host.json`host.json 참조 설명서[에 설명된 대로 ](functions-host-json.md) 파일에서 최대 일괄 처리 크기를 구성할 수 있습니다.

함수의 C# 경우 형식을 강력한 형식의 배열로 변경할 수 있습니다.  예를 들어 메서드 서명은 `EventData sensorEvent` 대신 `EventData[] sensorEvent`일 수 있습니다.  다른 언어의 경우 [다음과 같이](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)일괄 처리를 사용 하도록 설정 하려면 `function.json`의 카디널리티 속성을 `many`로 명시적으로 설정 해야 합니다.

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>동시성을 처리하도록 호스트 동작 구성

호스트 런타임 및 트리거 동작의 구성에 함수 앱의 `host.json` 파일을 사용할 수 있습니다.  동작을 일괄 처리하는 것 외에도 여러 트리거에 대한 동시성을 관리할 수 있습니다. 이러한 옵션의 값을 조정하면 호출된 함수의 요구에 맞게 각 인스턴스의 크기를 조정할 수 있습니다.

호스트 json 파일의 설정은 함수의 *단일 인스턴스* 내에서 앱 내의 모든 함수에 적용 됩니다. 예를 들어 두 개의 HTTP 함수를 사용 하 고 [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) 요청을 25로 설정 하는 함수 앱을 사용 하는 경우, http 트리거 중 하나에 대 한 요청은 공유 25 개의 동시 요청에 계산 됩니다.  해당 함수 앱의 크기가 10 개의 인스턴스로 조정 된 경우 두 함수는 250 동시 요청을 효과적으로 허용 합니다 (10 개 인스턴스 * 인스턴스당 동시 요청 25 개). 

다른 호스트 구성 옵션은 [host. json 구성 문서](functions-host-json.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에서 연결을 관리하는 방법](manage-connections.md)
* [Azure App Service 모범 사례](../app-service/app-service-best-practices.md)
