---
title: Azure Service Bus 제한 개요 | Microsoft Docs
description: Service Bus 제한 개요-표준 및 프리미엄 계층.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: 21a3bfd09e83571e489e15e9351e12220a99e563
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301257"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Azure Service Bus에 대 한 제한 작업

클라우드 네이티브 솔루션은 작업으로 확장할 수 있는 무제한 리소스의 개념을 제공 합니다. 이 개념은 온-프레미스 시스템을 사용 하는 것 보다 클라우드에서 더 진정한 사실 이지만 클라우드에는 여전히 제한이 있습니다.

이러한 제한으로 인해이 문서에 설명 된 대로 표준 및 프리미엄 계층에서 클라이언트 응용 프로그램 요청이 제한 될 수 있습니다. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Azure Service Bus 표준 계층에서 제한

Azure Service Bus 표준 계층은 종 량 제 가격 책정 모델을 사용 하 여 다중 테넌트 설정으로 작동 합니다. 여기서는 동일한 클러스터의 여러 네임 스페이스가 할당 된 리소스를 공유 합니다. 표준 계층은 낮은 처리량 프로덕션 시스템과 함께 개발자, 테스트 및 QA 환경에 권장 되는 선택 사항입니다.

과거에는 Azure Service Bus 리소스 사용률에 엄격 하 게 의존 하는 제한 사항이 있습니다. 그러나 제한 논리를 구체화 하 고 이러한 리소스를 공유 하는 모든 네임 스페이스에 예측 가능한 조정 동작을 제공할 수 있습니다.

동일한 리소스를 사용 하는 모든 Azure Service Bus 표준 네임 스페이스에서 리소스를 공평 하 게 사용 하 고 배포 하기 위해 조정 논리가 신용 기반으로 수정 되었습니다.

> [!NOTE]
> Azure Service Bus 또는 클라우드 네이티브 ***서비스에 대 한 제한은*** 제한이 없다는 점에 유의 해야 합니다.
>
> 신용 기반 조정은 다양 한 네임 스페이스가 다중 테넌트 표준 계층 환경에서 리소스를 공유 하는 방식을 구체화 하 여 리소스를 공유 하는 모든 네임 스페이스의 공평 한 사용을 가능 하 게 합니다.

### <a name="what-is-credit-based-throttling"></a>신용 기반 조정 이란?

신용 기반 제한은 특정 기간에 지정 된 네임 스페이스에 대해 수행할 수 있는 작업의 수를 제한 합니다. 

신용 기반 조정에 대 한 워크플로는 다음과 같습니다. 

  * 각 기간이 시작 될 때 각 네임 스페이스에 특정 개수의 크레딧을 제공 합니다.
  * 보낸 사람 또는 받는 사람 클라이언트 응용 프로그램에서 수행 하는 모든 작업은 이러한 크레딧에 대해 계산 되 고 사용 가능한 크레딧에서 뺍니다.
  * 크레딧이 소진 된 경우 다음 기간이 시작 될 때까지 후속 작업이 제한 됩니다.
  * 크레딧이 다음 기간의 시작 부분에서 보충 됩니다.

### <a name="what-are-the-credit-limits"></a>신용 한도는 무엇 인가요?

신용 한도는 현재 매 초 마다 ' 1000 ' 크레딧을 설정 합니다 (네임 스페이스 당).

모든 작업이 동일 하 게 생성 되는 것은 아닙니다. 각 작업의 신용 비용은 다음과 같습니다. 

| 연산 | 신용 비용|
|-----------|-----------|
| 데이터 작업 (Send, SendAsync, Receive, ReceiveAsync, Peek) |메시지당 1 개 크레딧 |
| 관리 작업 (큐, 항목, 구독, 필터에 대 한 만들기, 읽기, 업데이트, 삭제) | 크레딧을 10 개 |

### <a name="how-will-i-know-that-im-being-throttled"></a>제한 되는 것을 어떻게 알 수 있나요?

클라이언트 응용 프로그램 요청이 제한 되는 경우 아래 서버 응답이 응용 프로그램에서 수신 되어 기록 됩니다.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>정체를 방지 하려면 어떻게 해야 하나요?

공유 리소스를 사용 하는 경우 해당 리소스를 공유 하는 다양 한 Service Bus 네임 스페이스에서 일종의 공평 한 사용을 적용 하는 것이 중요 합니다. 제한을 사용 하면 단일 작업의 스파이크로 인해 같은 리소스에 대 한 다른 작업이 제한 되지 않습니다.

이 문서의 뒷부분에서 설명한 대로 클라이언트 Sdk (및 기타 Azure PaaS 제품)에 기본 재시도 정책이 기본 제공 되므로 제한 될 위험이 없습니다. 제한 된 요청은 지 수 백오프를 사용 하 여 다시 시도 되며 크레딧이 보충 될 때 결국 진행 됩니다.

당연히 일부 응용 프로그램은 제한 된 것으로 인식 될 수 있습니다. 이 경우 [현재 Service Bus 표준 네임 스페이스를 Premium으로 마이그레이션하](service-bus-migrate-standard-premium.md)는 것이 좋습니다. 

마이그레이션 시에는 Service Bus 네임 스페이스에 전용 리소스를 할당 하 고, 워크 로드에 급증 하는 경우 리소스를 적절 하 게 확장 하 고, 제한 될 가능성을 줄일 수 있습니다. 또한 워크 로드가 일반 수준으로 감소 하는 경우 네임 스페이스에 할당 된 리소스를 축소할 수 있습니다.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Azure Service Bus 프리미엄 계층에서 제한

[Azure Service Bus 프리미엄](service-bus-premium-messaging.md) 계층은 메시징 단위를 기준으로 고객의 각 네임 스페이스 설정에 전용 리소스를 할당 합니다. 이러한 전용 리소스는 예측 가능한 처리량과 대기 시간을 제공 하며 높은 처리량 또는 중요 한 프로덕션 등급 시스템에 권장 됩니다.

또한 프리미엄 계층을 사용 하면 고객이 워크 로드의 급증을 경험 하는 경우 처리량 용량을 확장할 수 있습니다.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Service Bus 프리미엄에서 제한은 어떻게 작동 하나요?

Service Bus 프리미엄에 대 한 배타적 리소스 할당을 사용 하는 경우 제한은 전적으로 네임 스페이스에 할당 된 리소스의 제한에 따라 결정 됩니다.

요청 수가 현재 리소스에서 서비스를 지원할 수 있는 것 보다 많은 경우 요청이 제한 됩니다.

### <a name="how-will-i-know-that-im-being-throttled"></a>제한 되는 것을 어떻게 알 수 있나요?

Azure Service Bus 프리미엄에서 제한을 식별 하는 다양 한 방법이 있습니다. 
  * **제한 된 요청** 은 제한 된 요청 수를 식별 하기 위해 [Azure Monitor 요청 메트릭에](service-bus-metrics-azure-monitor.md#request-metrics) 표시 됩니다.
  * 높은 **CPU 사용량** 은 현재 리소스 할당이 높고 현재 워크 로드가 감소 하지 않는 경우 요청이 정체 될 수 있음을 나타냅니다.
  * 높은 **메모리 사용량** 은 현재 리소스 할당이 높고 현재 작업 부하가 감소 하지 않는 경우 요청이 정체 될 수 있음을 나타냅니다.

### <a name="how-can-i-avoid-being-throttled"></a>정체를 방지 하려면 어떻게 해야 하나요?

Service Bus Premium 네임 스페이스에는 이미 전용 리소스가 있으므로 작업에서 스파이크를 발생 시킬 때 (또는 예측) 네임 스페이스에 할당 된 메시징 단위의 수를 확장 하 여 제한 될 가능성을 줄일 수 있습니다.

위의 메트릭을 변경 하 여 트리거할 수 있는 [runbook](../automation/automation-create-alert-triggered-runbook.md) 을 만들어 확장/축소 하는 작업을 수행할 수 있습니다.

## <a name="faqs"></a>FAQ

### <a name="how-does-throttling-affect-my-application"></a>제한이 응용 프로그램에 어떤 영향을 미칩니까?

요청을 제한 하는 경우 리소스에서 허용 하는 것 보다 많은 요청을 처리 하므로 서비스가 사용 중임을 의미 합니다. 잠시 후 동일한 작업을 다시 시도 하는 경우 서비스에서 현재 작업을 수행한 후 요청이 적용 될 수 있습니다.

제한이 클라우드 네이티브 서비스의 예상 되는 동작 이므로 Azure Service Bus SDK 자체에 다시 시도 논리를 구축 했습니다. 기본값은 지 수 백오프를 사용 하 여 자동 재시도로 설정 되어 매번 같은 요청을 제한 하지 않도록 합니다.

기본 재시도 논리는 모든 작업에 적용 됩니다.

### <a name="does-throttling-result-in-data-loss"></a>조정 시 데이터 손실이 발생 하나요?

Azure Service Bus 지 속성에 맞게 최적화 되어 있으므로 서비스에서 요청 성공 여부를 확인 하기 전에 Service Bus에 전송 된 모든 데이터가 저장소에 커밋됩니다.

Service Bus에 의해 요청이 성공적으로 ' ACK ' (승인) 되 면 Service Bus에서 요청을 성공적으로 처리 한 것입니다. Service Bus에서 ' NACK ' (실패)를 반환 하는 경우 Service Bus에서 요청을 처리할 수 없어 클라이언트 응용 프로그램에서 요청을 다시 시도해 야 함을 의미 합니다.

그러나 요청이 제한 되 면 서비스는 리소스 제한으로 인해 현재 요청을 수락 하 고 처리할 수 없다는 것을 의미 합니다. 이 **는** Service Bus 단지 요청을 보지 않았기 때문에 어떤 종류의 데이터 손실이 암시 하지 않습니다. 이 경우 Service Bus SDK의 기본 다시 시도 정책을 사용 하 여 요청이 최종적으로 처리 되도록 합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징을 사용하는 방법에 대한 자세한 내용 및 예제는 다음에 나오는 고급 항목을 참조하세요.

* [Service Bus 메시징 개요](service-bus-messaging-overview.md)
* [빠른 시작: Azure Portal 및 .NET을 사용하여 메시지 보내기 및 받기](service-bus-quickstart-portal.md)
* [자습서: Azure Portal 및 토픽/구독을 사용하여 재고 업데이트](service-bus-tutorial-topics-subscriptions-portal.md)

