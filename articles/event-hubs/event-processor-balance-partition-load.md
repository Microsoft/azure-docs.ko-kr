---
title: 여러 인스턴스에 걸쳐 파티션 로드 균형 조정-Azure Event Hubs | Microsoft Docs
description: 이벤트 프로세서와 Azure Event Hubs SDK를 사용 하 여 응용 프로그램의 여러 인스턴스에 걸쳐 파티션 부하를 분산 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 8bf3f05b823a784f4f3fc2074719ed346f769f5e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933796"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>응용 프로그램의 여러 인스턴스에 걸쳐 파티션 로드 균형 조정
이벤트 처리 응용 프로그램의 크기를 조정 하기 위해 응용 프로그램의 여러 인스턴스를 실행 하 고 부하의 균형을 유지 하도록 할 수 있습니다. 이전 버전에서는 [EventProcessorHost](event-hubs-event-processor-host.md) 를 통해 프로그램의 여러 인스턴스 간에 부하를 분산 하 고 수신 시 검사점 이벤트를 분산할 수 있었습니다. 최신 버전 (5.0 이상)에서는 **EventProcessorClient** (.Net 및 Java) 또는 **EventHubConsumerClient** (Python 및 JavaScript)를 사용 하 여 동일한 작업을 수행할 수 있습니다. 개발 모델은 이벤트를 사용 하 여 더 간단 하 게 만들 수 있습니다. 이벤트 처리기를 등록 하 여 관심 있는 이벤트를 구독할 수 있습니다.

이 문서에서는 여러 인스턴스를 사용 하 여 이벤트 허브에서 이벤트를 읽은 다음 이벤트 프로세서 클라이언트의 기능에 대 한 세부 정보를 제공 하는 샘플 시나리오를 설명 합니다 .이를 통해 여러 파티션에서 한 번에 이벤트를 수신 하 고 동일한 이벤트 허브 및 소비자 그룹을 사용 하는 다른 소비자와 부하를 분산할 수 있습니다.

> [!NOTE]
> Event Hubs의 크기를 조정하는 핵심은 분할된 소비자라는 개념입니다. [경쟁하는 소비자](/previous-versions/msp-n-p/dn568101(v=pandp.10)) 패턴과 달리 분할된 소비자 패턴을 사용하면 경합 병목 상태를 제거하고 종단 간 병렬 처리를 용이하게 하여 대규모 크기 조정이 가능해집니다.

## <a name="example-scenario"></a>예제 시나리오

예제 시나리오로 100,000호의 집을 모니터링하는 보안 회사를 가정합니다. 1 분 마다 각 홈에 설치 된 동작 탐지기, 도어/window 열린 센서, 유리 브레이크 감지기 등의 다양 한 센서에서 데이터를 가져옵니다. 회사는 거주자가 거의 실시간으로 해당 집의 활동을 모니터링하는 웹 사이트를 제공합니다.

각 센서는 데이터를 이벤트 허브로 푸시합니다. 이벤트 허브는 16개의 파티션으로 구성됩니다. 소비 하는 끝에서는 이러한 이벤트를 읽고, 통합 (필터, 집계 등) 하 고, 집계를 저장소 blob에 덤프할 수 있는 메커니즘이 필요 하며,이는 사용자에 게 친숙 한 웹 페이지에 투영 됩니다.

## <a name="write-the-consumer-application"></a>소비자 애플리케이션 작성

분산된 환경에서 소비자를 디자인할 때 시나리오는 다음 요구 사항을 처리해야 합니다.

1. **크기 조정:** 몇몇 Event Hubs 파티션에서 읽기 소유권을 가진 각 소비자를 사용하여 여러 소비자를 만듭니다.
2. **부하 분산:** 소비자를 동적으로 증가시키거나 감소시킵니다. 예를 들어, 새 센서 형식(예: 일산화 탐지기)이 각 집에 추가되면 이벤트 수가 증가합니다. 이 경우에 연산자(사람)는 소비자 인스턴스 수를 증가시킵니다. 그런 다음, 소비자 풀은 소유한 파티션의 수를 다시 조정하여 새로 추가된 소비자와 관련된 부하를 공유할 수 있습니다.
3. **실패 시 원활한 재개:** 소비자 (**소비자 a**)가 실패 하는 경우 (예: 소비자를 호스트 하는 가상 컴퓨터가 갑자기 충돌 하는 경우) 다른 소비자가 **소비자 a** 가 소유한 파티션을 선택 하 고 계속 진행할 수 있습니다. 또한 *검사점* 또는 *오프셋*이라는 연속 지점은 **소비자 A**가 실패한 정확한 지점 또는 약간 앞에 위치해야 합니다.
4. **이벤트 사용:** 이전 세 가지 지점은 소비자의 관리를 처리 하지만 이벤트를 사용 하 고 유용한 작업을 수행 하는 코드가 있어야 합니다. 예를 들어 집계 하 고 blob 저장소에 업로드 합니다.

## <a name="event-processor-or-consumer-client"></a>이벤트 프로세서 또는 소비자 클라이언트

이러한 요구 사항을 충족 하기 위해 솔루션을 직접 빌드할 필요는 없습니다. Azure Event Hubs Sdk는이 기능을 제공 합니다. .NET 또는 Java Sdk에서는 이벤트 프로세서 클라이언트 (EventProcessorClient)를 사용 하 고 Python 및 JavaScript Sdk에서는 EventHubConsumerClient을 사용 합니다. 이전 버전의 SDK에서는 이러한 기능을 지 원하는 이벤트 프로세서 호스트 (EventProcessorHost) 였습니다.

대부분의 프로덕션 시나리오에서는 이벤트 프로세서 클라이언트를 사용 하 여 이벤트를 읽고 처리 하는 것이 좋습니다. 프로세서 클라이언트는 진행 상태를 검사 하는 수단을 제공 하는 동시에 성능 및 내결함성 방식으로 이벤트 허브의 모든 파티션에서 이벤트를 처리 하기 위한 강력한 환경을 제공 하기 위한 것입니다. 또한 이벤트 프로세서 클라이언트는 지정 된 이벤트 허브에 대 한 소비자 그룹의 컨텍스트 내에서 협조적으로 작업할 수 있습니다. 클라이언트는 그룹에 대 한 인스턴스를 사용할 수 있거나 사용할 수 없게 되 면 작업의 분산 및 분산을 자동으로 관리 합니다.

## <a name="partition-ownership-tracking"></a>파티션 소유권 추적

일반적으로 이벤트 프로세서 인스턴스는 하나 이상의 파티션에서 이벤트를 소유 하 고 처리 합니다. 파티션의 소유권은 event hub 및 소비자 그룹 조합과 연결 된 모든 활성 이벤트 프로세서 인스턴스 간에 균등 하 게 분산 됩니다. 

각 이벤트 프로세서에는 검사점 저장소에서 항목을 추가 하거나 업데이트 하 여 고유한 식별자와 파티션에 대 한 클레임 소유권이 제공 됩니다. 모든 이벤트 프로세서 인스턴스는이 저장소와 정기적으로 통신 하 여 자체 처리 상태를 업데이트 하 고 다른 활성 인스턴스에 대해 알아봅니다. 그런 다음이 데이터를 사용 하 여 활성 프로세서 간에 부하를 분산 합니다. 새 인스턴스는 처리 풀에 조인 하 여 확장할 수 있습니다. 오류가 발생 하거나 규모를 축소 하 여 인스턴스가 중단 되 면 파티션 소유권이 다른 활성 프로세서로 정상적으로 전송 됩니다.

검사점 저장소의 파티션 소유권 레코드 Event Hubs 네임 스페이스, 이벤트 허브 이름, 소비자 그룹, 이벤트 프로세서 식별자 (소유자 라고도 함), 파티션 ID 및 마지막으로 수정한 시간을 추적 합니다.



| Event Hubs 네임스페이스               | 이벤트 허브 이름 | **소비자 그룹** | 소유자                                | Partition ID | 마지막으로 수정한 시간  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

각 이벤트 프로세서 인스턴스는 파티션의 소유권을 획득 하 고 마지막으로 알려진 [검사점](# Checkpointing)에서 파티션 처리를 시작 합니다. 프로세서가 실패 하면 (VM이 종료 됨), 다른 인스턴스는 마지막으로 수정한 시간을 확인 하 여이를 검색 합니다. 다른 인스턴스는 이전에 비활성 인스턴스가 소유 하 고 있는 파티션의 소유권을 가져오려고 시도 하며, 검사점 저장소는 인스턴스 중 하나만 파티션의 소유권을 주장 하는 데 성공 하도록 보장 합니다. 따라서 지정 된 시간에 한 번에 하나의 프로세서만 파티션에서 이벤트를 수신 합니다.

## <a name="receive-messages"></a>메시지 받기

이벤트 프로세서를 만들 때 이벤트와 오류를 처리할 함수를 지정 합니다. 이벤트를 처리 하는 함수에 대 한 각 호출은 특정 파티션에서 단일 이벤트를 전달 합니다. 이 이벤트를 처리 하는 것은 사용자의 책임입니다. 소비자가 모든 메시지를 한 번 이상 처리 하 게 하려면 재시도 논리를 사용 하 여 사용자 고유의 코드를 작성 해야 합니다. 하지만 포이즌 메시지에 대해 주의해야 합니다.

작업을 상대적으로 빠르게 수행 하는 것이 좋습니다. 즉, 가능한 한 적은 처리를 수행 합니다. 저장소에 써야 하 고 일부 라우팅을 수행 해야 하는 경우 두 개의 소비자 그룹을 사용 하 고 두 개의 이벤트 프로세서를 사용 하는 것이 좋습니다.

## <a name="checkpointing"></a>검사점 설정

*검사점* 은 이벤트 프로세서가 파티션 내에서 마지막으로 처리 된 이벤트의 위치를 표시 하거나 커밋하는 프로세스입니다. 검사점 표시는 일반적으로 이벤트를 처리 하 고 소비자 그룹 내에서 파티션당 발생 하는 함수 내에서 수행 됩니다. 

이벤트 프로세서가 파티션에서 연결을 끊으면 다른 인스턴스는 해당 소비자 그룹에서 해당 파티션의 마지막 프로세서에 의해 이전에 커밋된 검사점의 파티션 처리를 다시 시작할 수 있습니다. 프로세서는 연결 될 때 오프셋을 이벤트 허브에 전달 하 여 읽기를 시작할 위치를 지정 합니다. 이러한 방식으로, 다운스트림 응용 프로그램에서 이벤트를 "완료"로 표시 하 고 이벤트 프로세서가 중단 될 때 복원 력을 제공 하는 데 검사점을 사용할 수 있습니다. 이 검사점 프로세스에서 더 낮은 오프셋을 지정하면 이전 데이터로 돌아갈 수 있습니다. 

이벤트를 처리 된 것으로 표시 하기 위해 검사점을 수행 하면 검사점 저장소의 항목이 이벤트의 오프셋 및 시퀀스 번호를 사용 하 여 추가 되거나 업데이트 됩니다. 사용자는 검사점 업데이트 빈도를 결정 해야 합니다. 성공적으로 처리 된 각 이벤트 후 업데이트는 기본 검사점 저장소에 대 한 쓰기 작업을 트리거하는 성능 및 비용 영향을 가질 수 있습니다. 또한 모든 단일 이벤트의 검사점은 Service Bus 큐가 이벤트 허브 보다 더 나은 옵션 일 수 있는 대기 중인 메시징 패턴을 나타냅니다. Event Hubs의 기본 개념은 대규모로 "최소한 한 번" 제공받는다는 것입니다. 다운스트림 시스템 멱등원을 만들어서 쉽게 오류로부터 복구하거나 여러 번 수신되는 동일한 이벤트에서 해당 결과를 다시 시작할 수 있습니다.

> [!NOTE]
> Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Storage Blob SDK를 지 원하는 환경에서 검사점 저장소로 Azure Blob Storage을 사용 하는 경우, 코드를 사용 하 여 저장소 서비스 API 버전을 해당 환경에서 지 원하는 특정 버전으로 변경 해야 합니다. 예를 들어 [Azure Stack 허브 버전 2002에서 Event Hubs](/azure-stack/user/event-hubs-overview)를 실행 하는 경우 저장소 서비스에 사용할 수 있는 가장 높은 버전은 2017-11-09입니다. 이 경우에는 코드를 사용 하 여 저장소 서비스 API 버전을 2017-11-09로 대상으로 해야 합니다. 특정 Storage API 버전을 대상으로 지정 하는 방법에 대 한 예제는 GitHub의 다음 샘플을 참조 하세요. 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) 또는  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>스레드 안전성 및 프로세서 인스턴스

기본적으로 이벤트를 처리 하는 함수는 지정 된 파티션에 대해 순차적으로 호출 됩니다. 이벤트 펌프가 다른 스레드의 백그라운드에서 계속 실행 될 때 동일한 파티션 큐에서이 함수에 대 한 후속 이벤트와 호출은 내부적으로 진행 됩니다. 서로 다른 파티션의 이벤트는 동시에 처리할 수 있으며 파티션 간에 액세스 되는 모든 공유 상태는 동기화 되어야 합니다.

## <a name="next-steps"></a>다음 단계
다음 빠른 시작을 참조 하세요.

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
