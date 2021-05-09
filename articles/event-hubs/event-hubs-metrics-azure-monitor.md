---
title: Azure Monitor의 메트릭 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Monitoring을 사용하여 Azure Event Hubs를 모니터링하는 방법을 설명합니다.
ms.topic: article
ms.date: 02/25/2021
ms.openlocfilehash: 52ab66fe264b85be117eb8e2e21cb89f38d0fcae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715584"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Monitor의 Azure Event Hubs 메트릭

Event Hubs 메트릭은 Azure 구독에서 Event Hubs 리소스의 상태를 제공합니다. 풍부한 메트릭 데이터 집합을 사용하여 네임스페이스 수준뿐만 아니라 엔터티 수준에서 Event Hubs의 전반적인 상태를 평가할 수 있습니다. 이러한 통계는 Event Hubs의 상태를 모니터링하는 데 도움을 주므로 중요할 수 있습니다. Azure 지원에 문의할 필요 없이 메트릭을 통해 근본 원인 문제를 해결할 수도 있습니다.

Azure Monitor는 다양한 Azure 서비스를 모니터링하기 위한 통합된 사용자 인터페이스를 제공합니다. 자세한 내용은 GitHub의 [Microsoft Azure에서 모니터링](../azure-monitor/overview.md) 및 [.NET을 사용하여 Azure Monitor 메트릭 검색](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) 샘플을 참조하세요.

## <a name="access-metrics"></a>메트릭에 액세스

Azure Monitor는 메트릭에 액세스하는 여러 가지 방법을 제공합니다. [Azure Portal](https://portal.azure.com)을 통해 메트릭에 액세스하거나 Azure Monitor API(REST 및 .NET) 및 Log Analytics 및 Event Hubs 같은 분석 솔루션을 사용할 수 있습니다. 자세한 내용은 [Azure Monitor에서 수집된 데이터 모니터링](../azure-monitor/data-platform.md)을 참조하세요.

메트릭은 기본적으로 활성화되며 최근 30일분 데이터에 액세스할 수 있습니다. 더 오랜 기간에 대한 데이터를 보존해야 하는 경우 메트릭 데이터를 Azure Storage 계정에 보관할 수 있습니다. 이 설정은 Azure Monitor의 [진단 설정](../azure-monitor/essentials/diagnostic-settings.md)에서 구성할 수 있습니다.


## <a name="access-metrics-in-the-portal"></a>포털에서 메트릭에 액세스

[Azure Portal](https://portal.azure.com)에서 시간 경과에 따른 메트릭을 모니터링할 수 있습니다. 다음 예제에서는 성공한 요청 및 계정 수준에서 들어오는 요청을 확인하는 방법을 보여 줍니다.

![성공 메트릭 보기][1]

네임스페이스를 통해 메트릭에 직접 액세스할 수도 있습니다. 이렇게 하려면 네임스페이스를 선택한 다음, **메트릭** 을 선택합니다. 이벤트 허브 범위로 필터링된 메트릭을 표시하려면 이벤트 허브를 선택한 다음 **메트릭** 을 선택합니다.

다음 예제와 같이 차원을 지원하는 메트릭의 경우 원하는 차원 값을 사용하여 필터링해야 합니다.

![차원 값으로 필터링][2]

## <a name="billing"></a>결제

Azure Monitor에서 메트릭 사용은 현재 무료입니다. 그러나 메트릭 데이터를 수집하는 다른 솔루션을 사용하는 경우 해당 솔루션에서 요금을 청구할 수 있습니다. 예를 들어 메트릭 데이터를 Azure Storage 계정에 보관하는 경우 Azure Storage에서 요금을 청구합니다. 고급 분석을 위해 Azure Monitor 로그에 메트릭 데이터를 스트리밍하는 경우에도 Azure에서 요금을 청구합니다.

다음 메트릭은 서비스의 상태에 대한 개요를 제공합니다. 

> [!NOTE]
> 다른 이름으로 이동되므로 여러 가지 메트릭을 사용 중단하는 중입니다. 참조를 업데이트해야 할 수도 있습니다. “사용되지 않음” 키워드로 표시된 메트릭은 앞으로 지원되지 않습니다.

모든 메트릭 값은 매분마다 Azure Monitor에 전송됩니다. 시간 세분성은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 Event Hubs 메트릭에 대해 지원되는 시간 간격은 1분입니다.

## <a name="azure-event-hubs-metrics"></a>Azure Event Hubs 메트릭
서비스에서 지원하는 메트릭 목록은 [Azure Event Hubs](../azure-monitor/essentials/metrics-supported.md#microsofteventhubnamespaces)를 참조하세요.

> [!NOTE]
> 사용자 오류가 발생하면 Azure Event Hubs는 **사용자 오류** 메트릭을 업데이트하지만 다른 진단 정보는 기록하지 않습니다. 따라서 애플리케이션에서 사용자 오류 세부 정보를 캡처해야 합니다. 또는 메시지가 전송되거나 Application Insights로 수신될 때 생성되는 원격 분석 데이터를 변환할 수도 있습니다. 예시를 보려면 [Application Insights로 추적](../service-bus-messaging/service-bus-end-to-end-tracing.md#tracking-with-azure-application-insights)을 참조하세요.

## <a name="azure-monitor-integration-with-siem-tools"></a>SIEM 도구와 Azure Monitor 통합
Azure Monitor를 사용하여 모니터링 데이터(활동 로그, 진단 로그 등)를 이벤트 허브로 라우팅하면 SIEM(보안 정보 및 이벤트 관리) 도구와 쉽게 통합할 수 있습니다. 자세한 내용은 다음 문서/블로그 게시물을 참조하세요.

- [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
- [Azure Log Integration 소개](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Azure Monitor를 사용하여 SIEM 도구와 통합](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

SIEM 도구가 이벤트 허브의 로그 데이터를 사용하는 시나리오에서, 들어오는 메시지가 표시되지 않거나 메트릭 그래프에 들어오는 메시지는 표시되지만 나가는 메시지가 표시되지 않는 경우 다음 단계를 수행합니다.

- **들어오는 메시지가 없는** 경우 Azure Monitor 서비스가 감사/진단 로그를 이벤트 허브로 옮기고 있지 않다는 것을 의미합니다. 이 시나리오에서는 Azure Monitor 팀을 대상으로 지원 티켓을 엽니다. 
- 들어오는 메시지는 있지만 **보내는 메시지는 없는** 경우 SIEM 애플리케이션이 메시지를 읽지 않고 있는 것입니다. SIEM 공급자에게 문의하여 애플리케이션의 이벤트 허브 구성이 올바른지를 확인합니다.


## <a name="next-steps"></a>다음 단계

* [Azure Monitor 개요](../azure-monitor/overview.md)를 참조하세요.
* GitHub의 [.NET을 사용하여 Azure Monitor 메트릭 검색](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) 샘플입니다. 

Event Hubs에 대한 자세한 내용은 다음 링크를 방문하세요.

- Event Hubs 자습서 시작
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs FAQ](event-hubs-faq.md)
* [Event Hubs를 사용하는 샘플 애플리케이션](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
