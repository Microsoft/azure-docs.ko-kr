---
title: Azure Functions 지역 재해 복구 및 고가용성
description: 지리적 지역을 사용 하 여 중복성을 Azure Functions 하 고 장애 조치 (failover)를 수행 하는 방법입니다.
author: wesmc7777
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: jehollan
ms.openlocfilehash: bdeff0194bda620250481a215c145b1ec3b2207e
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920788"
---
# <a name="azure-functions-geo-disaster-recovery"></a>지역 재해 복구 Azure Functions

전체 Azure 지역 또는 데이터 센터에서 가동 중지 시간이 발생 하는 경우 계산을 다른 지역에서 계속 처리 하는 것이 중요 합니다.  이 문서에서는 재해 복구를 허용 하는 기능을 배포 하는 데 사용할 수 있는 몇 가지 전략을 설명 합니다.

## <a name="basic-concepts"></a>기본 개념

특정 지역에서 실행 Azure Functions.  더 높은 가용성을 얻기 위해 동일한 기능을 여러 지역에 배포할 수 있습니다.  여러 지역에 있는 경우 *활성/활성* 패턴 또는 *활성/수동* 패턴에서 함수를 실행할 수 있습니다.  

* 활성/활성. 두 지역은 모두 활성 상태 이며 이벤트를 수신 합니다 (중복 또는 rotationally). 활성/활성은 Azure Front 문과 함께 HTTPS 함수에 권장 됩니다.
* 능동/수동. 한 지역은 활성 상태이 고 이벤트를 수신 하며 보조 복제본은 유휴 상태입니다.  장애 조치 (failover)가 필요한 경우 보조 지역은 활성화 되 고 처리를 수행 합니다.  Service Bus 및 Event Hubs와 같은 HTTP가 아닌 함수에 권장 됩니다.

다중 지역 배포에 대한 자세한 내용은 [여러 지역에서 앱을 실행](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) 하는 방법을 참조 하세요.

## <a name="activeactive-for-https-functions"></a>HTTPS 함수에 대해 활성/활성

함수에 대한 활성/활성 배포를 위해서는 두 지역 간에 이벤트를 조정할 수 있는 일부 구성 요소가 필요 합니다.  HTTPS 함수의 경우 [Azure Front 도어](../frontdoor/front-door-overview.md)를 사용 하 여 이러한 조정을 수행 합니다.  Azure 전면 도어는 여러 지역 함수 간에 HTTPS 요청을 라우팅하고 라운드 로빈 할 수 있습니다.  또한 각 엔드포인트의 상태를 정기적으로 확인 합니다.  지역 함수에서 상태 검사에 대한 응답을 중지 하는 경우 Azure Front 도어는 회전을 수행 하지 않으며 정상 기능으로 트래픽을 전달 합니다.  

![Azure Front 도어 및 함수에 대한 아키텍처](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>HTTPS가 아닌 함수에 대해 활성/활성

HTTPS가 아닌 함수에 대해 활성/활성 배포를 달성할 수 있습니다.  그러나 두 지역이 상호 작용 하는 방법을 고려해 야 합니다.  동일한 Service Bus 큐에서 각각 트리거되는 동일한 함수 앱을 두 지역에 배포 하는 경우 큐에서 큐에서 제거 하는 경쟁 소비자 역할을 수행 합니다.  즉, 각 메시지가 인스턴스 중 하나 에서만 처리 되는 것을 의미 하는 반면 단일 Service Bus에 대한 단일 실패 지점이 여전히 있음을 의미 합니다.  두 개의 Service Bus 큐 (주 지역에 하나, 보조 지역에 하나)를 배포 하는 경우 두 개의 함수 앱이 지역 큐를 가리키는 것은 이제 두 지역 간에 큐 메시지를 분산 하는 방법에 대한 과제가 발생 합니다.  이는 일반적으로 각 게시자가 *두* 지역에 메시지를 게시 하려고 시도 하 고, 각 메시지가 활성 함수 앱에서 처리 되는 것을 의미 합니다.  이는 활성/활성 패턴을 만들지만 계산의 중복 및 데이터 통합 시기 또는 방법에 대한 다른 문제를 만듭니다.  이러한 이유로 HTTPS가 아닌 트리거에서 활성/수동 패턴을 사용 하는 것이 좋습니다.

## <a name="activepassive-for-non-https-functions"></a>HTTPS가 아닌 함수를 위한 능동/수동

활성/수동은 단일 함수에서 각 메시지를 처리 하는 방법을 제공 하지만 재해가 발생 한 경우 보조 지역으로 장애 조치 (failover) 하는 메커니즘을 제공 합니다.  Azure Functions는 [지역 복구](../service-bus-messaging/service-bus-geo-dr.md) 및 [Azure Event Hubs 지역에서 복구](../event-hubs/event-hubs-geo-dr.md)Azure Service Bus와 함께 작동 합니다.

예를 들어 Azure Event Hubs 트리거를 사용 하는 경우 활성/수동 패턴에는 다음이 포함 됩니다.

* 기본 및 보조 지역에 모두 배포 되는 Azure Event Hub입니다.
* 지역 재해를 사용 하도록 설정 하 여 기본 및 보조 이벤트 허브를 페어링 합니다.  또한 event hubs에 연결 하 고 연결 정보를 변경 하지 않고 기본에서 보조로 전환 하는 데 사용할 수 있는 "별칭"을 만듭니다.
* 함수 앱은 기본 및 보조 지역 모두에 배포 됩니다.
* 함수 앱은 해당 이벤트 허브에 대한 *직접* (비 별칭) 연결 문자열을 트리거합니다. 
* 이벤트 허브에 대한 게시자는 별칭 연결 문자열에 게시 해야 합니다. 

![능동-수동 예제 아키텍처](media/functions-geo-dr/active-passive.png)

장애 조치 (failover) 전에 공유 별칭으로 보내는 게시자는 기본 이벤트 허브로 라우팅됩니다.  기본 함수 앱은 기본 이벤트 허브로만 수신 대기 합니다.  보조 함수 앱은 수동 및 유휴 상태가 됩니다.  장애 조치 (failover)가 시작 되는 즉시 공유 별칭으로 보내는 게시자는 보조 이벤트 허브로 라우팅됩니다.  이제 보조 함수 앱이 활성 상태가 되 고 자동으로 트리거를 시작 합니다.  보조 지역에 대한 효과적인 장애 조치 (failover)는 이벤트 허브에서 전적으로 구동 될 수 있으며, 함수는 해당 이벤트 허브가 활성 상태인 경우에만 활성화 됩니다.

자세한 내용은 [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) 및 [event hubs](../event-hubs/event-hubs-geo-dr.md)로 장애 조치 (failover)에 대한 정보 및 고려 사항을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Front 도어 만들기](../frontdoor/quickstart-create-front-door.md)
* [Event Hubs 장애 조치 고려 사항](../event-hubs/event-hubs-geo-dr.md#considerations)
