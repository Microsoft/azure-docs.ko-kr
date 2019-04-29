---
title: 지리적 재해 복구 - Azure Event Hubs| Microsoft Docs
description: 지리적 지역을 사용하여 장애 조치(Failover)하고 Azure Event Hubs에서 재해 복구를 수행하는 방법
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 56077d018c1ae62809d51fc66d7f5aff93fb4c02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821851"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs - 지리적 재해 복구 

([가용성 영역](../availability-zones/az-overview.md)을 사용하지 않는 경우)전체 Azure 지역 또는 데이터 센터에서 가동 중지 시간이 발생하면 데이터 처리가 다른 지역 또는 데이터 센터에서 계속 작동되는 것이 중요합니다. 따라서 *지리적 재해 복구* 및 *지리적 복제*는 기업에 중요한 기능입니다. Azure Event Hubs는 네임스페이스 수준에서 지리적 재해 복구 및 지리적 복제를 둘 다 지원합니다. 

지역 재해 복구 기능은 Event Hubs 표준 SKU에 전역적으로 사용할 수 있습니다.

## <a name="outages-and-disasters"></a>중단 및 재해

"중단" 및 "재해" 간의 차이점을 참고하는 것이 중요합니다. *중단*은 Azure Event Hubs를 일시적으로 사용할 수 없는 것으로, 메시징 저장소와 같은 서비스의 일부 구성 요소나 전체 데이터 센터에 영향을 줄 수 있습니다. 그렇지만 문제가 해결되면 Event Hubs를 다시 사용할 수 있게 됩니다. 일반적으로 중단으로 인해 메시지나 기타 데이터가 손실되지는 않습니다. 이러한 중단의 예로 데이터 센터의 전원 장애를 들 수 있습니다. 일부 가동 중단은 일시적인 네트워크 문제로 인해 짧은 연결 오류가 발생합니다. 

*재해*는 Event Hubs 클러스터, Azure 지역 또는 데이터 센터의 영구적이거나 장기적인 손실을 의미합니다. 지역 또는 데이터 센터는 다시 사용할 수 있게 될 수도, 그렇지 않을 수도 있고 몇 시간 또는 며칠 동안 중지될 수도 있습니다. 재해의 예로는 화재, 홍수 또는 지진이 있습니다. 영구적 재해는 일부 메시지, 이벤트 또는 기타 데이터의 손실을 발생시킬 수 있습니다. 그러나 대부분의 경우에는 데이터 손실이 없으며 메시지는 데이터 센터를 백업하면 복구할 수 있습니다.

Azure Event Hubs의 지역 재해 복구 기능은 재해 복구 솔루션입니다. 이 문서에 설명된 개념 및 워크플로는 재해 시나리오에 적용되며 일시적이거나 임시적인 중단이 아닙니다. Microsoft Azure의 재해 복구에 대해 자세히 알아보려면 [이 문서](/azure/architecture/resiliency/disaster-recovery-azure-applications)를 참조하세요.

## <a name="basic-concepts-and-terms"></a>기본 개념 및 용어

재해 복구 기능은 메타데이터 재해 복구를 구현하며 주 및 보조 재해 복구 네임스페이스를 사용합니다. 지역 재해 복구 기능은 [표준 SKU](https://azure.microsoft.com/pricing/details/event-hubs/)에만 사용할 수 있습니다. 별칭을 통해 연결이 수행되므로 연결 문자열을 변경할 필요가 없습니다.

이 문서에서는 다음 용어가 사용됩니다.

-  *별칭*: 설정하는 재해 복구 구성의 이름입니다. 별칭은 하나의 안정적인 FQDN(정규화된 도메인 이름) 연결 문자열을 제공합니다. 애플리케이션은 이 별칭 연결 문자열을 사용하여 네임스페이스에 연결합니다. 

-  *주/보조 네임스페이스*: 별칭에 해당하는 네임스페이스입니다. 기본 네임스페이스는 "활성"이며 메시지를 수신합니다(기존 또는 새 네임스페이스일 수 있음). 보조 네임스페이스는 "수동"이며 메시지를 수신하지 않습니다. 둘 간의 메타데이터가 동기화되므로 둘 다 애플리케이션 코드 또는 연결 문자열을 변경하지 않고 메시지를 원활하게 수락할 수 있습니다. 활성 네임스페이스만 메시지를 수신하는지 확인하려면 별칭을 사용해야 합니다. 

-  *메타데이터*: Event Hubs 및 소비자 그룹과 같은 엔터티 및 네임스페이스와 연결된 서비스의 해당 속성입니다. 엔터티 및 해당 설정만이 자동으로 복제됩니다. 메시지 및 이벤트는 복제되지 않습니다. 

-  *장애 조치(Failover)*: 보조 네임스페이스를 활성화하는 프로세스입니다.

## <a name="setup-and-failover-flow"></a>흐름 설정 및 장애 조치

다음 섹션은 장애 조치 프로세스의 개요이며 초기 장애 조치를 설정하는 방법을 설명합니다. 

![1][]

### <a name="setup"></a>설정

먼저 기존의 기본 네임스페이스 및 새로운 보조 네임스페이스를 만들거나 사용한 다음 둘을 쌍으로 연결합니다. 이 페어링은 연결하는 데 사용할 수 있는 별칭을 제공합니다. 별칭을 사용하므로 연결 문자열을 변경할 필요가 없습니다. 새 네임스페이스에만 장애 조치(Failover) 페어링에 추가할 수 있습니다. 마지막으로 장애 조치가 필요한 경우 감지할 몇 가지 모니터링을 추가해야 합니다. 대부분의 경우에 서비스는 큰 에코시스템의 일부입니다. 따라서 장애 조치가 주로 나머지 하위 시스템 또는 인프라와 동기화되어 수행되어야 하므로 자동 장애 조치는 거의 불가능합니다.

### <a name="example"></a>예

이 시나리오의 예로 메시지 또는 이벤트를 내보내는 POS(Point of Sale) 솔루션을 고려합니다. Event Hubs는 일부 매핑 또는 다시 포맷 솔루션에 해당 이벤트를 전달합니다. 그런 다음 추가 처리를 위해 다른 시스템에 매핑된 데이터를 전달합니다. 해당 시점에 이러한 시스템은 모두 같은 Azure 지역에서 호스팅될 수 있습니다. 장애 조치할 시기 및 부분에 대한 결정은 인프라에 있는 데이터 흐름에 따라 달라집니다. 

모니터링 시스템 또는 사용자 빌드 모니터링 솔루션을 사용하여 장애 조치를 자동화할 수 있습니다. 그러나 이러한 자동화에는 추가 계획 및 작업이 필요합니다. 이 부분은 이 문서에서 다루지 않습니다.

### <a name="failover-flow"></a>흐름 장애 조치(Failover)

장애 조치를 시작하는 경우 다음과 같은 두 단계가 필요합니다.

1. 작동 중단이 발생하면 다시 장애 조치(Failover)할 수 있어야 합니다. 따라서 다른 수동 네임스페이스를 설정하고 페어링을 업데이트합니다. 

2. 사용할 수 있으면 이전 기본 네임스페이스에서 메시지를 끌어옵니다. 그 후에 지역 복구 설정의 외부에서 일반 메시징에 해당 네임스페이스를 사용하거나 이전 기본 네임스페이스를 삭제합니다.

> [!NOTE]
> 실패 전달 의미 체계만이 지원됩니다. 이 시나리오에서는 새 네임스페이스를 사용하여 장애 조치하고 다시 페어링합니다. 예를 들어 SQL 클러스터에서 장애 복구는 지원되지 않습니다. 

![2][]

## <a name="management"></a>관리

예를 들어 초기 설정 작업 중에 잘못된 지역을 페어링하는 실수가 발생한 경우 언제든지 두 네임스페이스의 페어링을 해제할 수 있습니다. 일반 네임스페이스와 페어링된 네임스페이스를 사용하려는 경우 별칭을 삭제합니다.

## <a name="samples"></a>샘플

[GitHub의 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient)에서는 장애 조치를 설정하고 시작하는 방법을 보여줍니다. 이 샘플은 다음과 같은 개념을 보여줍니다.

- Event Hubs에서 Azure Resource Manager를 사용하는 데 필요한 Azure Active Directory의 설정 
- 샘플 코드를 실행하는 데 필요한 단계 
- 현재 기본 네임스페이스에서 전달 및 수신 

## <a name="considerations"></a>고려 사항

이 릴리스에서 고려할 다음과 같은 고려 사항에 유의하세요.

1. 장애 조치 계획에서 시간 요소를 고려해야 합니다. 예를 들어 15~20분이 넘게 연결이 손실된 경우 장애 조치를 시작하기로 결정할 수 있습니다. 
 
2. 데이터가 복제되지 않으면 현재 활성 세션이 복제되지 않습니다. 또한 중복 검색 및 예약된 메시지가 작동하지 않을 수 있습니다. 새 세션, 예약된 메시지 및 새 중복이 작동합니다. 

3. 복잡한 분산 인프라를 장애 조치하려면 한 번 이상 [예행 연습](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation)을 수행해야 합니다. 

4. 엔터티를 동기화하는 데 분당 약 50~100개의 엔터티를 처리하므로 다소 시간이 걸릴 수 있습니다.

## <a name="availability-zones"></a>가용성 영역 

Event Hubs 표준 SKU는 Azure 지역 내에서 오류가 없는 위치를 제공하는 [가용성 영역](../availability-zones/az-overview.md)을 지원합니다. 

> [!NOTE]
> Azure Event Hubs 표준에 대한 가용성 영역 지원은 가용성 영역이 있는 [Azure 지역](../availability-zones/az-overview.md#services-support-by-region)에서만 사용할 수 있습니다.

Azure Portal을 사용하여 새로운 네임스페이스에서만 가용성 영역을 사용하도록 설정할 수 있습니다. Event Hubs에서는 기존 네임스페이스의 마이그레이션을 지원하지 않습니다. 네임스페이스를 사용하도록 설정한 후에는 영역 중복성을 사용하지 않도록 설정할 수 없습니다.

![3][]

## <a name="next-steps"></a>다음 단계

* [GitHub의 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient)은 지리적 페어링을 만들고 재해 복구 시나리오에 대한 장애 조치(Failover)를 시작하는 간단한 워크플로를 진행합니다.
* [REST API 참조](/rest/api/eventhub/disasterrecoveryconfigs)에서는 지리적 재해 복구 구성을 수행하기 위한 API에 대해 설명합니다.

Event Hubs에 대한 자세한 내용은 다음 링크를 방문하세요.

* [Event Hubs 자습서](event-hubs-dotnet-standard-getstarted-send.md) 시작
* [Event Hubs FAQ](event-hubs-faq.md)
* [Event Hubs를 사용하는 샘플 애플리케이션](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png