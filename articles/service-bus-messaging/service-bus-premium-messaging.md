---
title: Azure Service Bus 프리미엄 및 표준 메시지 가격 책정 계층 개요 | Microsoft Docs
description: Service Bus 프리미엄 및 표준 메시징 계층
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 478446c834f5e6c811d78828868ba12a90e603ac
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125834"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus 프리미엄 및 표준 메시징 계층

큐 및 토픽과 같은 엔터티를 포함하는 Service Bus 메시징은 클라우드 규모로 엔터프라이즈 메시징 기능을 풍부한 게시-구독 의미 체계와 결합합니다. Service Bus 메시징은 정교한 여러 클라우드 솔루션의 통신 백본으로 사용됩니다.

Service Bus 메시징의 *프리미엄* 계층은 중요 업무용 애플리케이션에 대한 확장성, 성능 및 가용성에 관한 일반적인 고객의 요청을 해결합니다. 프리미엄 계층은 프로덕션 시나리오에 사용하는 것이 좋습니다. 기능 집합은 거의 동일하지만 이러한 Service Bus 메시징의 두 계층은 다른 용도로 사용하도록 고안되었습니다.

다음 테이블에는 차이가 자세히 설명되어 있습니다.

| Premium | Standard |
| --- | --- |
| 높은 처리량 |가변 처리량 |
| 예측 가능한 성능 |가변 대기 시간 |
| 고정된 가격 책정 |종량제 가변 가격 |
| 작업을 확장 및 축소하는 기능 |N/A |
| 최대 1MB의 메시지 크기 |최대 256KB의 메시지 크기 |

**Service Bus 프리미엄 메시지**는 각 고객의 워크로드가 따로 실행되도록 CPU 및 메모리 수준에서 리소스 격리를 제공합니다. 이 리소스 컨테이너를 *메시징 단위*라고 합니다. 각 프리미엄 네임스페이스에는 하나 이상의 메시징 단위가 할당됩니다. 각 Service Bus 프리미엄 네임스페이스에 대해 1, 2 또는 4 메시징 단위를 구입할 수 있습니다. 단일 워크 로드 또는 엔터티가 여러 메시징 단위에 걸쳐 수 및 메시징 단위 수를 변경할 수 있습니다. 그 결과, Service Bus 기반 솔루션에 대해 예측 가능하고 반복 가능한 성능이 구현됩니다.

이로 인해 예측 가능성 및 가용성도 높아질 뿐 아니라 속도도 더 빨라집니다. Service Bus 프리미엄 메시징은 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)에 도입된 저장소 엔진에 빌드됩니다. 프리미엄 메시징을 사용할 경우 표준 계층을 사용하는 것보다 최고 성능이 훨씬 더 빠릅니다.

## <a name="premium-messaging-technical-differences"></a>프리미엄 메시징 기술 차이

다음 섹션에서는 프리미엄 및 표준 메시지 계층 간의 몇 가지 차이점을 설명합니다.

### <a name="partitioned-queues-and-topics"></a>분할 큐 및 항목

프리미엄 메시징에서 분할된 큐 및 항목이 지원되지 않습니다. 분할에 대한 자세한 내용은 [분할 큐 및 항목](service-bus-partitioning.md)을 참조하세요.

### <a name="express-entities"></a>Express 엔터티

프리미엄 메시지가 완전히 격리된 런타임 환경에서 실행되므로 프리미엄 메시지는 Express 엔터티가 지원되지 않습니다. Express 기능에 대한 자세한 내용은 [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 속성을 참조하세요.

표준 메시지에서 실행되는 코드가 있고 프리미엄 계층으로 이식하려는 경우 [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 속성이 **false**(기본값)로 설정되어 있는지 확인합니다.

## <a name="premium-messaging-resource-usage"></a>프리미엄 메시징 리소스 사용량
일반적으로 엔터티의 모든 작업에는 CPU 및 메모리 사용량을 발생할 수 있습니다. 다음은 이러한 작업의 일부입니다. 

- CRUD 등의 관리 작업 (만들기, 검색, 업데이트 및 삭제) 작업 큐, 토픽 및 구독에 있습니다.
- 런타임 작업 (메시지 보내기 및 받기)
- 작업 및 경고 모니터링

하지만 추가 CPU 및 메모리 사용량 또한 가격이 하지는. 프리미엄 메시징 계층에 대 한 메시지 단위에 대 한 단일 가격을 있습니다.

CPU 및 메모리 사용량 추적 되며 다음과 같은 이유로 있습니다에 게 표시 됩니다. 

- 시스템 내부에 대 한 투명성을 제공 합니다.
- 구매 하는 리소스의 용량을 이해 합니다.
- 용량 계획 하는 확장/축소를 결정 하도록 도와줍니다.

## <a name="get-started-with-premium-messaging"></a>프리미엄 메시징 시작

프리미엄 메시징 시작은 간단하며 프로세스는 표준 메시징의 프로세스와 비슷합니다. 먼저 [Azure Portal](https://portal.azure.com)에서 [네임스페이스를 만듭니다](service-bus-create-namespace-portal.md). **가격 책정 계층**에서 **프리미엄**을 선택했는지 확인합니다. **전체 가격 책정 세부 정보 보기**를 클릭하여 각 계층에 대해 자세히 알아봅니다.

![create-premium-namespace][create-premium-namespace]

[Azure Resource Manager 템플릿을 사용하여 프리미엄 네임스페이스](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/)를 만들 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시지에 대해 자세히 알아보려면 다음 링크를 참조하세요.

* [Azure Service Bus 프리미엄 메시징 소개(블로그 게시물)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure Service Bus 프리미엄 메시징 소개(Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus 메시징 개요](service-bus-messaging-overview.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
