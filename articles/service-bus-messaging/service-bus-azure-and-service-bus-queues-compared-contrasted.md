---
title: Azure Storage 및 Service Bus 큐 비교 및 대조 | Microsoft Docs
description: Azure에서 제공하는 두 가지 유형의 큐 사이의 차이점과 유사점을 분석합니다.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 2086813b01de6cd06f3714477e56864b36196382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714577"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Azure 큐 및 Service Bus 큐 - 비교 및 대조
이 문서에서는 현재 Microsoft Azure에서 제공하는 두 가지 유형의 큐인 Storage 큐와 Service Bus 큐 사이의 차이점과 유사점을 분석합니다. 이 정보를 사용하여 각각의 기술을 비교 및 대조하고 요구에 가장 적합한 솔루션이 어떤 것인지 더 합리적으로 결정할 수 있습니다.

## <a name="introduction"></a>소개
Azure에서는 두 가지 유형의 큐 메커니즘 **Storage 큐**와 **Service Bus 큐**를 지원합니다.

**Storage 큐**는 [Azure Storage](https://azure.microsoft.com/services/storage/) 인프라의 일부이며, 간단한 REST 기반 GET/PUT/PEEK 인터페이스를 통해 서비스 내부와 서비스 사이에 안정적이고 지속적인 메시징을 제공합니다.

**Service Bus 큐**는 더 폭넓은 [Azure 메시징](https://azure.microsoft.com/services/service-bus/) 인프라의 일부이며, 큐뿐 아니라 게시/구독과 여러 고급 통합 패턴도 지원합니다. Service Bus 큐/항목/구독에 대한 자세한 내용은 [Service Bus 개요](service-bus-messaging-overview.md)를 참조하세요.

두 큐 기술이 동시에 존재하고 있지만, Storage 큐가 Azure Storage 서비스를 기반으로 구축된 전용 큐 스토리지 메커니즘으로 먼저 소개되었습니다. Service Bus 큐는 여러 통신 프로토콜, 데이터 계약, 신뢰 도메인 및/또는 네트워크 환경에 걸쳐 존재할 수 있는 애플리케이션 또는 애플리케이션 구성 요소를 통합하도록 설계된 더 폭넓은 메시징 인프라를 기반으로 구축되었습니다.

## <a name="technology-selection-considerations"></a>기술 선택 시 고려 사항
Storage 큐와 Service Bus 큐는 모두 현재 Microsoft Azure에서 제공하는 메시지 큐 서비스의 구현입니다. 각각은 약간씩 다른 기능 집합을 가지고 있으며, 이는 특정 솔루션의 요구 또는 해결하려는 비즈니스/기술 문제에 따라 하나 또는 다른 것 또는 둘 모두를 선택할 수 있음을 의미합니다.

주어진 솔루션의 목적에 어떤 큐 기술이 적합한지 결정할 때, 솔루션 설계자와 개발자는 이러한 권장 사항을 고려해야 합니다. 자세한 내용은 다음 섹션을 참조하세요.

솔루션 설계자/개발자라면 다음과 같은 경우 **Storage 큐의 사용을 고려해야** 합니다.

* 애플리케이션은 80GB보다 많은 메시지는 큐에 저장해야 합니다.
* 애플리케이션에서 큐 내부의 메시지 처리 진행률을 추적하려는 경우. 이는 작업자가 메시지 충돌을 처리하는 경우에 유용합니다. 그러면 후속 작업자가 해당 정보를 사용하여 이전 작업자가 중단한 부분부터 작업을 계속할 수 있습니다.
* 큐에 대해 실행된 모든 트랜잭션의 서버 측 로그가 필요합니다.

솔루션 설계자/개발자라면 다음과 같은 경우 **Service Bus 큐를 사용하는 것을 고려해야** 합니다.

* 솔루션이 큐를 폴링하지 않고도 메시지를 수신할 수 있어야 하는 경우. Service Bus의 경우 Service Bus에서 지원하는 TCP 기반 프로토콜을 통해 장기 폴링 수신 작업을 사용하여 이를 달성할 수 있습니다.
* 솔루션에서 큐가 보장된 FIFO(선입선출) 순차적 전달을 제공해야 하는 경우.
* 솔루션이 자동 중복 검색을 지원할 수 있어야 하는 경우.
* 애플리케이션이 메시지를 병렬 장기 실행 스트림(메시지의 [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) 속성을 사용하여 메시지가 스트림과 연결됨)으로 처리하는 것이 좋습니다. 이 모델에서는 소비 애플리케이션의 각 노드가 메시지가 아니라 스트림에 대해 경쟁합니다. 소비 노드에 스트림이 전달되면 해당 노드는 트랜잭션을 사용하여 애플리케이션 스트림 상태를 검사할 수 있습니다.
* 큐에서 여러 메시지를 송신 또는 수신할 경우 솔루션에 트랜잭션 동작 및 원자성이 필요합니다.
* 애플리케이션은 64KB를 초과하지만 256KB의 한도에 접근할 가능성은 없는 메시지를 처리합니다.
* 큐에 대한 역할 기반 액세스 모델, 보낸 사람과 받는 사람에 대해 서로 다른 권한을 제공해야 하는 조건을 처리해야 합니다.
* 큐 크기는 80GB보다 크게 증가하지 않습니다.
* AMQP 1.0 표준 기반 메시징 프로토콜을 사용하는 것이 좋습니다. AMQP에 대한 자세한 내용은 [Service Bus AMQP 개요](service-bus-amqp-overview.md)를 참조하세요.
* 궁극적으로는 큐 기반의 지점 간 통신에서 큐에 전송된 일부 또는 전체 메시지의 개별 복사본을 수신하는 추가 수신자(구독자)의 원활한 통합을 지원하는 메시지 교환 패턴으로 마이그레이션하는 것도 기대할 수 있습니다. 후자는 Service Bus에서 기본적으로 제공하는 게시/구독 기능입니다.
* 메시징 솔루션은 사용자가 추가 인프라 구성 요소를 구축하지 않고도 “최대 1회(At-Most-Once)” 전달 보장을 지원할 수 있어야 합니다.
* 일괄 처리 메시지를 게시하고 소비할 수 있기를 바랄 것입니다.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Storage 큐와 Service Bus 큐 비교
다음 섹션의 표에서는 큐 기능의 논리적 그룹화가 제공되며, Azure Storage 큐와 Service Bus 큐 모두에서 사용할 수 있는 기능을 한눈에 비교할 수 있습니다.

## <a name="foundational-capabilities"></a>기본 기능
이 섹션에서는 Storage 큐와 Service Bus 큐가 제공하는 일부 기본 큐 기능을 비교합니다.

| 비교 기준 | Storage 큐 | Service Bus 큐 |
| --- | --- | --- |
| 순서 보장 |**아니요** <br/><br>자세한 내용은 “추가 정보” 섹션의 첫 번째 참고를 참조하세요.</br> |**예 - 선입선출(FIFO)**<br/><br>(메시징 세션의 사용을 통해) |
| 전달 보장 |**최소 1회(At-Least-Once)** |**최소 1회(At-Least-Once)**<br/><br/>**최대 1회(At-Most-Once)** |
| 원자성 작업 지원 |**아니요** |**예**<br/><br/> |
| 수신 동작 |**비중단**<br/><br/>(새 메시지가 없을 경우 즉시 완료) |**시간 제한 있는/없는 차단**<br/><br/>(장기 폴링 또는 ["Comet 기술"](https://go.microsoft.com/fwlink/?LinkId=613759) 제공)<br/><br/>**비중단**<br/><br/>(.NET 관리 API만을 사용하여) |
| 푸시 스타일 API |**아니요** |**예**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) 및 **OnMessage** 세션 .NET API |
| 수신 모드 |**보기 및 임대** |**보기 및 잠금**<br/><br/>**수신 및 삭제** |
| 단독 액세스 모드 |**임대 기반** |**잠금 기반** |
| 임대/잠금 기간 |**30초(기본값)**<br/><br/>**7일(최대값)**([UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API를 사용하여 메시지 임대를 갱신하거나 해제할 수 있음) |**60초(기본값)**<br/><br/>[RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API를 사용하여 메시지 잠금을 갱신할 수 있습니다. |
| 임대/잠금 정밀도 |**메시지 수준**<br/><br/>(각 메시지에 서로 다른 시간 제한 값을 지정할 수 있으며, 그 다음 [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) API를 사용하여 메시지를 처리하는 동안 필요에 따라 이를 업데이트할 수 있음) |**큐 수준**<br/><br/>(각 큐에 모든 메시지에 적용되는 잠금 정밀도가 있지만 [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API를 사용하여 잠금을 갱신할 수 있음) |
| 일괄 수신 |**예**<br/><br/>(메시지를 수신할 때 메시지 개수를 명시적으로 지정, 메시지 수 최대 32개) |**예**<br/><br/>(암시적으로 또는 트랜잭션 사용을 통해 명시적으로 프리페치 속성 사용) |
| 일괄 송신 |**아니요** |**예**<br/><br/>(트랜잭션 또는 클라이언트 측 일괄 처리의 사용을 통해) |

### <a name="additional-information"></a>추가 정보
* Storage 큐에 포함된 메시지는 일반적으로 선입선출되지만 예를 들어, 메시지의 가시성 시간 제한 기간이 만료되는 경우(예: 처리 도중 클라이언트 애플리케이션의 충돌)와 같이 때에 따라 순서가 바뀔 수 있습니다. 가시성 시간 제한이 만료되면 다른 작업자가 큐에서 제거할 수 있도록 메시지가 큐에 다시 표시됩니다. 이 시점에서 새로 표시되는 메시지는 원래 그 뒤에 큐에 삽입된 메시지 다음으로 큐에 배치될 수 있습니다(다시 큐에서 제거되기 위해).
* Service Bus 큐의 보장된 FIFO 패턴에는 메시징 세션을 사용해야 합니다. **보기 및 잠금** 모드에서 수신된 메시지를 처리하는 도중 애플리케이션이 충돌하는 경우, 다음에 큐 수신기가 메시징 세션을 수락할 때 TTL(Time-to-Live) 기간이 만료된 후 실패한 메시지에서 시작합니다.
* Storage 큐는 확장성과 오류에 대한 허용 오차, 부하 평준화, 빌드 프로세스 워크플로를 향상시키도록 애플리케이션 구성 요소를 분리하는 등과 같은 표준 큐 시나리오를 지원하도록 설계되었습니다.
* Service Bus 큐는 *최소 1회(At-Least-Once)* 전달 보장을 지원합니다. 
* 세션의 메시지 시퀀스, 처리의 진행 상태를 기준으로 응용 프로그램의 상태를 저장 하려면 세션 상태를 사용 하 고 트랜잭션을 사용 하 여 Service Bus 세션의 컨텍스트에서 메시지 처리와 관련 하 여 불일치를 방지할 수 있습니다. 확인 메시지 및 세션 상태 업데이트를 받았습니다. 이 유형의 일관성 기능 이라고 때로는 *정확 하 게-한 번 처리* 다른 공급 업체의 제품 에서만 트랜잭션 오류 메시지를 다시 배달 하면 분명 하 고 따라서 라는 용어는 정확 하 게 적절 한 합니다.
* Storage 큐는 개발자와 작업 팀 모두에게 큐, 테이블, BLOB에 걸쳐 균일하고 일관적인 프로그래밍 모델을 제공합니다.
* Service Bus 큐는 단일 큐의 컨텍스트에서 로컬 트랜잭션에 대한 지원을 제공합니다.
* Service Bus에서 지원하는 **수신 및 삭제** 모드는 전달 보장이 줄어드는 대신 메시징 작업 수(및 관련 비용)를 절감할 수 있는 기능을 제공합니다.
* Storage 큐는 메시지에 대한 임대를 연장할 수 있는 기능이 포함된 임대 기능을 제공합니다. 이를 통해 작업자는 메시지에 대한 단기 임대를 유지 관리할 수 있습니다. 따라서 작업자가 충돌할 경우 다른 작업자가 메시지를 신속하게 처리할 수 있습니다. 또한 현재 임대 시간보다 긴 처리가 필요할 경우 작업자가 메시지에 대한 임대를 연장할 수 있습니다.
* Storage 큐는 메시지를 큐에 삽입하거나 큐에서 제거할 때 설정할 수 있는 가시성 시간 제한을 제공합니다. 또한 런타임 동안 임대 값이 서로 다른 메시지를 업데이트하거나, 동일한 큐에 포함된 메시지 전체에 서로 다른 값을 업데이트할 수 있습니다. Service Bus 잠금 시간 제한은 큐 메타데이터에 정의되지만, [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) 메서드를 호출하여 잠금을 갱신할 수 있습니다.
* Service Bus 큐에서 수신 차단 작업에 대한 최대 시간 제한은 24일입니다. 하지만 REST 기반 시간 제한의 경우 최대값이 55초입니다.
* Service Bus에서 제공하는 클라이언트 측 일괄 처리 기능을 활용하면 큐 클라이언트가 여러 메시지를 단일 전송 작업으로 일괄 처리할 수 있습니다. 일괄 처리는 비동기 전송 작업에만 사용 가능합니다.
* Storage 큐는 200TB 최대 한도(계정을 가상화할 경우 그 이상도 가능)와 무제한 큐와 같은 기능을 제공하므로 SaaS 공급자에게 이상적인 플랫폼입니다.
* Storage 큐는 유연하고 성능이 위임된 액세스 제어 메커니즘을 제공합니다.

## <a name="advanced-capabilities"></a>고급 기능
이 섹션에서는 Storage 큐와 Service Bus 큐에서 제공하는 고급 기능을 비교합니다.

| 비교 기준 | Storage 큐 | Service Bus 큐 |
| --- | --- | --- |
| 예약 배달 |**예** |**예** |
| 배달 못한 메시지 자동 처리 |**아니요** |**예** |
| 큐 TTL(Time-to-Live) 값 증가 |**예**<br/><br/>(표시 시간 제한의 전체 업데이트를 통해) |**예**<br/><br/>(전용 API 함수를 통해 제공됨) |
| 포이즌 메시지 지원 |**예** |**예** |
| 전체 업데이트 |**예** |**예** |
| 서버 측 트랜잭션 로그 |**예** |**아니요** |
| Storage 메트릭 |**예**<br/><br/>**순간 메트릭**: 가용성, TPS, API 호출 횟수, 오류 수 등에 대한 실시간 메트릭을 모두 실시간으로 제공합니다(분 단위로 집계되며 프로덕션에서 방금 발생한 일이 몇 분 안에 보고됨). 자세한 내용은 [저장소 분석 메트릭 정보](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics)를 참조하세요. |**예**<br/><br/>([GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)를 호출하여 대량 쿼리) |
| 상태 관리 |**아니요** |**예**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| 메시지 자동 전달 |**아니요** |**예** |
| 큐 삭제 기능 |**예** |**아니요** |
| 메시지 그룹 |**아니요** |**예**<br/><br/>(메시징 세션의 사용을 통해) |
| 메시지 그룹당 애플리케이션 상태 |**아니요** |**예** |
| 중복 검색 |**아니요** |**예**<br/><br/>(보낸 사람 쪽에서 구성 가능) |
| 메시지 그룹 찾아보기 |**아니요** |**예** |
| ID별로 메시지 세션 가져오기 |**아니요** |**예** |

### <a name="additional-information"></a>추가 정보
* 두 큐 기술 모두 메시지를 나중에 배달하도록 예약하는 기능을 제공합니다.
* 큐 자동 전달 기능을 통해 수천 개의 큐에서 메시지를 단일 큐로 전달하고, 수신 애플리케이션이 단일 큐에서 메시지를 소비할 수 있습니다. 이 메커니즘을 사용하여 보안, 흐름 제어를 달성하고 각 메시지 게시자 사이에 저장소를 격리할 수 있습니다.
* Storage 큐는 메시지 콘텐츠의 업데이트를 지원합니다. 이 기능을 사용하여 메시지에 상태 정보와 증분 진행률 업데이트를 유지함으로써 작업을 처음부터 시작하는 대신 최종적으로 알려진 검사점부터 처리할 수 있습니다. Service Bus 큐의 경우 메시지 세션을 사용하여 동일한 시나리오를 활용할 수 있습니다. 세션을 통해 애플리케이션 처리 상태를 저장하고 가져올 수 있습니다([SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 및 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState) 사용).
* Service Bus 큐에서만 지원하는 [배달 못 한 편지 처리](service-bus-dead-letter-queues.md)는 수신 애플리케이션에서 정상적으로 처리할 수 없는 메시지를 격리하거나, 만료된 TTL(Time-to-Live) 속성으로 인해 메시지가 대상에 도달할 수 없는 경우에 유용할 수 있습니다. TTL 값은 메시지가 큐에 남아 있는 기간을 지정합니다. Service Bus의 경우 TTL 기간이 만료되면 메시지가 $DeadLetterQueue라는 특별한 큐로 이동됩니다.
* 메시지를 큐에서 제거할 때 Storage 큐에 포함된 "포이즌" 메시지를 찾기 위해 애플리케이션에서 메시지의 [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) 속성을 검사합니다. **DequeueCount**가 지정된 임계값을 초과하는 경우, 애플리케이션은 해당 메시지를 애플리케이션에서 정의한 “배달 못 한 편지” 큐로 이동시킵니다.
* Storage 큐의 경우 큐에 대해 실행되는 모든 트랜잭션의 상세 로그와 더불어 집계된 메트릭을 입수할 수 있습니다. 이 옵션은 모두 디버깅과 애플리케이션이 Storage 큐를 어떻게 사용하는지 이해하는 데 유용합니다. 이는 또한 애플리케이션의 성능을 튜닝하고 큐 사용 비용을 절감하는 데에도 유용합니다.
* Service Bus에서 지원하는 “메시지 세션” 개념을 활용하면 특정 논리 그룹에 속한 메시지를 지정된 수신자와 연결하고, 해당 수신자가 메시지와 각각의 수신자 사이에 세션과 유사한 선호도를 만들 수 있습니다. 메시지에서 [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) 속성을 설정하면 Service Bus의 이 고급 기능을 사용할 수 있습니다. 수신자가 특정 세션 ID를 수신 대기하고 지정된 세션 식별자를 공유하는 메시지를 수신할 수 있습니다.
* Service Bus 큐에서 지원하는 중복 검색 기능은 [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) 속성의 값을 기준으로 큐 또는 토픽으로 발송된 중복 메시지를 자동으로 제거합니다.

## <a name="capacity-and-quotas"></a>용량 및 할당량
이 섹션에서는 적용할 수 있는 [용량과 할당량](service-bus-quotas.md)의 관점에서 Storage 큐와 Service Bus 큐를 비교합니다.

| 비교 기준 | Storage 큐 | Service Bus 큐 |
| --- | --- | --- |
| 최대 큐 크기 |**500TB**<br/><br/>([단일 저장소 계정 용량](../storage/common/storage-introduction.md#queue-storage)으로 제한됨) |**1GB-80GB**<br/><br/>(큐 생성 및 [분할 사용](service-bus-partitioning.md) 시에 정의됨 – “추가 정보” 섹션 참조) |
| 최대 메시지 크기 |**64KB**<br/><br/>(**Base64** 인코딩을 사용할 때 48KB)<br/><br/>Azure는 큐 및 BLOB 결합을 통해 더 큰 메시지를 지원하며, 단일 항목에 대해 최대 200GB까지 큐에 삽입할 수 있습니다. |**256KB** 또는 **1MB**<br/><br/>(헤더 및 본문 포함, 최대 헤더 크기: 64KB).<br/><br/>[서비스 계층](service-bus-premium-messaging.md)에 따라 달라 집니다. |
| 최대 메시지 TTL |**Infinite**(api-version 2017-07-27부터) |**TimeSpan.Max** |
| 최대 큐 수 |**무제한** |**10,000**<br/><br/>(서비스 네임스페이스당) |
| 최대 동시 클라이언트 수 |**무제한** |**무제한**<br/><br/>(TCP 프로토콜 기반의 통신에 한해 100개의 동시 연결 제한이 적용됨) |

### <a name="additional-information"></a>추가 정보
* Service Bus의 경우 큐 크기 제한이 강제 적용됩니다. 최대 큐 크기는 큐 생성 시에 지정되며, 1GB ~ 80GB 사이의 값이 될 수 있습니다. 큐 생성 시에 설정된 큐 크기 값에 도달하면, 추가로 수신되는 메시지가 거부되며 호출 코드에서 예외를 수신합니다. Service Bus의 할당량에 대한 자세한 내용은 [Service Bus 할당량](service-bus-quotas.md)을 참조하세요.
* 분할은 [프리미엄 계층](service-bus-premium-messaging.md)에서 지원되지 않습니다. 표준 계층에서 Service Bus 큐는 1, 2, 3, 4 또는 5GB 크기로 만들 수 있습니다(기본값은 1GB). 표준 계층에서 분할을 사용하는 경우(기본값) Service Bus는 사용자가 지정한 각 GB마다 16개의 파티션을 만듭니다. 따라서 크기가 5GB인 큐를 만들 경우 16개의 파티션에서 최대 큐 크기는 (5 * 16) = 80GB가 됩니다. [Azure Portal][Azure portal]에서 해당 항목을 보면 분할된 큐 또는 토픽의 최대 크기를 확인할 수 있습니다.
* Storage 큐의 경우 메시지 콘텐츠가 XML-safe가 아니라면 **Base64**로 인코딩되어야 합니다. 메시지를 **Base64**로 인코딩하면 사용자 페이로드가 64KB가 아니라 최대 48KB가 될 수 있습니다.
* Service Bus 큐의 경우 큐에 저장된 각 메시지가 헤더와 본문의 두 부분으로 구성됩니다. 메시지의 총 크기는 서비스 계층에서 지원하는 최대 메시지 크기를 초과할 수 없습니다.
* 클라이언트가 TCP 프로토콜을 통해 Service Bus와 통신할 때, 단일 Service Bus 큐에 대한 최대 동시 연결 수가 100으로 제한됩니다. 이 숫자는 보낸 사람과 받는 사람 사이에 공유됩니다. 이 할당량에 도달하면 후속 추가 연결 요청이 거부되며 호출 코드에서 예외를 수신합니다. REST 기반 API를 사용하여 큐에 연결하는 클라이언트에게는 이 제한이 적용되지 않습니다.
* 단일 Service Bus 네임스페이스에 10,000개 이상의 큐가 필요할 경우, Azure 지원 팀에 문의하여 확장을 요청할 수 있습니다. 또한 Service Bus에서 큐를 10,000개 이상으로 확장하려면 [Azure Portall][Azure portal]을 사용하여 추가 네임스페이스를 만들어야 합니다.

## <a name="management-and-operations"></a>관리 및 운영
이 섹션에서는 Storage 큐와 Service Bus 큐에서 제공하는 관리 기능을 비교합니다.

| 비교 기준 | Storage 큐 | Service Bus 큐 |
| --- | --- | --- |
| 관리 프로토콜 |**HTTP/HTTPS를 통한 REST** |**HTTPS를 통한 REST** |
| 런타임 프로토콜 |**HTTP/HTTPS를 통한 REST** |**HTTPS를 통한 REST**<br/><br/>**AMQP 1.0 표준(TCP 및 TLS)** |
| .NET API |**예**<br/><br/>(.NET Storage 클라이언트 API) |**예**<br/><br/>(.NET Service Bus API) |
| 네이티브 C++ |**예** |**예** |
| Java API |**예** |**예** |
| PHP API |**예** |**예** |
| Node.js API |**예** |**예** |
| 임의 메타데이터 지원 |**예** |**아니요** |
| 큐 명명 규칙 |**최대 길이 63자**<br/><br/>(큐 이름의 문자는 소문자여야 함) |**최대 길이 260자**<br/><br/>(큐 경로 및 이름은 대/소문자가 구분되지 않음) |
| 큐 길이 가져오기 함수 |**예**<br/><br/>(메시지가 삭제되지 않고 TTL을 초과하여 만료되는 경우 근사값) |**예**<br/><br/>(정확한 지정 시간 값) |
| 엿보기 기능 |**예** |**예** |

### <a name="additional-information"></a>추가 정보
* Storage 큐는 이름/값 쌍의 형식으로 큐 설명에 적용할 수 있는 임의 특성을 지원합니다.
* 두 큐 기술 모두 메시지를 잠그지 않고도 엿볼 수 있는 기능을 제공하며, 이는 큐 탐색기/브라우저 도구를 구현할 때 유용할 수 있습니다.
* Service Bus의 .NET 조정된 메시징 API는 HTTP를 통한 REST에 비해 향상된 성능을 제공하는 전이중 TCP 연결을 활용하며, AMQP 1.0 표준 프로토콜을 지원합니다.
* Storage 큐 이름은 3 ~ 63자로 지정할 수 있으며, 소문자, 숫자, 하이픈이 포함될 수 있습니다. 자세한 내용은 [큐 및 메타데이터 명명](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata)을 참조하세요.
* Service Bus 큐 이름은 최대 260자로 지정할 수 있으며 명명 규칙이 덜 제한적입니다. Service Bus 큐 이름에는 문자, 숫자, 마침표, 하이픈, 밑줄이 포함될 수 있습니다.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여
이 섹션에서는 Storage 큐와 Service Bus 큐에서 지원하는 인증 및 권한 부여 기능에 대해 설명합니다.

| 비교 기준 | Storage 큐 | Service Bus 큐 |
| --- | --- | --- |
| Authentication |**대칭 키** |**대칭 키** |
| 보안 모델 |SAS 토큰을 통해 위임된 액세스. |SAS |
| ID 공급자 페더레이션 |**아니요** |**예** |

### <a name="additional-information"></a>추가 정보
* 각 큐 기술에 대한 모든 요청은 인증되어야 합니다. 익명 액세스 가능한 공개 큐는 지원되지 않습니다. [SAS](service-bus-sas.md)를 사용하면 쓰기 전용 SAS, 읽기 전용 SAS 또는 모든 권한 SAS를 게시하여 이러한 시나리오에 대응할 수 있습니다.
* Storage 큐에서 제공하는 인증 체계에는 SHA-256 알고리즘으로 계산되고 **Base64** 문자열로 인코딩된 HMAC(해시 기반 메시지 인증 코드)인 대칭 키가 사용됩니다. 각 프로토콜에 대한 자세한 내용은 [Azure Storage 서비스에 대한 인증](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services)을 참조하세요. Service Bus 큐도 대칭 키를 사용하는 유사한 모델을 지원합니다. 자세한 내용은 [Service Bus에서 공유 액세스 서명 인증](service-bus-sas.md)을 참조하세요.

## <a name="conclusion"></a>결론
두 기술에 대한 심층적인 이해를 통해 어떤 큐 기술을 언제 사용할 것인지에 대해 더 합리적인 결정을 내릴 수 있게 될 것입니다. Storage 큐 또는 Service Bus 기술을 어느 경우에 사용할 것인지에 대한 결정은 분명히 다양한 요인에 따라 달라집니다. 이러한 요인은 사용 중인 애플리케이션 및 아키텍처의 개별적 요구 사항에 따라 크게 달라질 수 있습니다. 애플리케이션이 이미 Microsoft Azure의 핵심 기능을 사용하고 있고, 특히 서비스 간의 기본적인 통신 및 메시징이 필요하거나 크기가 80GB 이상이 될 수 있는 큐가 필요하다면 Storage 큐를 선택하는 것이 바람직할 수 있습니다.

Service Bus 큐의 경우 세션, 트랜잭션, 중복 검색, 배달 못한 메시지 자동 처리, 지속성 높은 게시/구독 기능 등과 같은 다수의 고급 기능을 제공하므로, 하이브리드 애플리케이션을 개발하는 중이거나 애플리케이션에서 달리 이러한 기능을 요구할 때 바람직한 선택이 될 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음은 Storage 큐 또는 Service Bus 큐에 대해 추가적인 지침과 정보를 제공하는 문서입니다.

* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Queue Storage 서비스를 사용하는 방법](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Service Bus 조정된 메시징을 사용한 성능 향상의 모범 사례](service-bus-performance-improvements.md)
* [Azure Service Bus의 큐 및 토픽 소개(블로그 게시물)](https://www.code-magazine.com/article.aspx?quickid=1112041)
* [Service Bus에 대한 개발자 가이드](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Azure에서 Queuing Service 사용하기](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

