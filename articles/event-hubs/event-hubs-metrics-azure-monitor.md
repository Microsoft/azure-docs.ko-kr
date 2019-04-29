---
title: Azure Monitor의 메트릭 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Monitoring을 사용하여 Azure Event Hubs를 모니터링하는 방법을 설명합니다.
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: b5df69e9670c01b576afe242b39532acb1e1c526
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685899"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Monitor의 Azure Event Hubs 메트릭

Event Hubs 메트릭은 Azure 구독에서 Event Hubs 리소스의 상태를 제공합니다. 풍부한 메트릭 데이터 집합을 사용하여 네임스페이스 수준뿐만 아니라 엔터티 수준에서 Event Hubs의 전반적인 상태를 평가할 수 있습니다. 이러한 통계는 Event Hubs의 상태를 모니터링하는 데 도움을 주므로 중요할 수 있습니다. 메트릭은 Azure 지원에 문의할 필요 없이 근본 원인 문제를 해결할 수도 있습니다.

Azure Monitor는 다양한 Azure 서비스를 모니터링하기 위한 통합된 사용자 인터페이스를 제공합니다. 자세한 내용은 GitHub의 [Microsoft Azure에서 모니터링](../monitoring-and-diagnostics/monitoring-overview.md) 및 [.NET을 사용하여 Azure Monitor 메트릭 검색](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) 샘플을 참조하세요.

## <a name="access-metrics"></a>메트릭에 액세스

Azure Monitor는 메트릭에 액세스하는 여러 가지 방법을 제공합니다. [Azure Portal](https://portal.azure.com)을 통해 메트릭에 액세스하거나 Azure Monitor API(REST 및 .NET) 및 Operation Management Suite 및 Event Hubs 같은 분석 솔루션을 사용할 수 있습니다. 자세한 내용은 [Azure Monitor에서 수집된 데이터 모니터링](../azure-monitor/platform/data-platform.md)을 참조하세요.

메트릭은 기본적으로 활성화되며 최근 30일분 데이터에 액세스할 수 있습니다. 더 오랜 기간에 대한 데이터를 보존해야 하는 경우 메트릭 데이터를 Azure Storage 계정에 보관할 수 있습니다. Azure Monitor의 [진단 설정](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)에서 이렇게 구성합니다.

## <a name="access-metrics-in-the-portal"></a>포털에서 메트릭에 액세스

[Azure Portal](https://portal.azure.com)에서 시간 경과에 따른 메트릭을 모니터링할 수 있습니다. 다음 예제에서는 성공한 요청 및 계정 수준에서 들어오는 요청을 확인하는 방법을 보여 줍니다.

![성공 메트릭 보기][1]

네임스페이스를 통해 메트릭에 직접 액세스할 수도 있습니다. 이렇게 하려면 네임 스페이스를 선택 하 고 클릭 **메트릭을**합니다. Event Hub 범위로 필터링된 메트릭을 표시하려면 Event Hub를 선택한 다음 **메트릭**을 클릭합니다.

다음 예제와 같이 차원을 지원하는 메트릭의 경우 원하는 차원 값을 사용하여 필터링해야 합니다.

![차원 값으로 필터링][2]

## <a name="billing"></a>결제

Azure Monitor에서 메트릭 사용은 현재 무료입니다. 그러나 메트릭 데이터를 수집하는 추가 솔루션을 사용하는 경우 해당 솔루션에서 요금을 청구할 수 있습니다. 예를 들어 메트릭 데이터를 Azure Storage 계정에 보관하는 경우 Azure Storage에서 요금을 청구합니다. 고급 분석용 Azure Monitor 로그로 메트릭 데이터를 스트리밍할 경우에 Azure에서 청구 됩니다.

다음 메트릭은 서비스의 상태에 대한 개요를 제공합니다. 

> [!NOTE]
> 다른 이름으로 이동되므로 여러 가지 메트릭을 사용 중단하는 중입니다. 참조를 업데이트해야 할 수도 있습니다. “사용되지 않음” 키워드로 표시된 메트릭은 앞으로 지원되지 않습니다.

모든 메트릭 값은 매분마다 Azure Monitor에 전송됩니다. 시간 세분성은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 Event Hubs 메트릭에 대해 지원되는 시간 간격은 1분입니다.

## <a name="request-metrics"></a>요청 메트릭

데이터 및 관리 작업 요청 수를 계산합니다.

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
| 들어오는 요청(미리 보기) | 지정된 기간 동안 Azure Event Hubs 서비스에 대한 요청 수입니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName |
| 성공한 요청(미리 보기)   | 지정된 기간 동안 Azure Event Hubs 서비스에 대한 성공한 요청 수입니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName |
| 서버 오류(미리 보기) | 지정된 기간 동안 Azure Event Hubs 서비스에서 오류로 인해 처리되지 않은 요청 수입니다. <br/><br/>단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName |
|사용자 오류(미리 보기)|지정된 기간 동안 사용자 오류로 인해 처리되지 않은 요청 수입니다.<br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|
|할당량 초과 오류(미리 보기)|사용 가능한 할당량을 초과하는 요청 수입니다. Event Hubs 할당량에 대한 자세한 내용은 [이 문서](event-hubs-quotas.md)를 참조하세요.<br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|

## <a name="throughput-metrics"></a>처리량 메트릭

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
|제한된 요청(미리 보기)|처리량 단위 사용량이 초과되었기 때문에 제한된 요청 수입니다.<br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|

## <a name="message-metrics"></a>메시지 메트릭

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
|들어오는 메시지 (미리 보기)|지정된 기간 동안 Event Hubs에 전송된 이벤트 또는 메시지 수입니다.<br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|
|보내는 메시지(미리 보기)|지정된 기간 동안 Event Hubs에서 검색된 이벤트 또는 메시지 수입니다.<br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|
|들어오는 바이트(미리 보기)|지정된 기간 동안 Azure Event Hubs 서비스에 전송된 바이트 수입니다.<br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|
|나가는 바이트(미리 보기)|지정된 기간 동안 Azure Event Hubs 서비스에서 검색된 바이트 수입니다.<br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|

## <a name="connection-metrics"></a>연결 메트릭

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
|ActiveConnections(미리 보기)|네임스페이스와 엔터티의 활성 연결 수입니다.<br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|
|열린 연결(미리 보기)|열린 연결 수입니다.<br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|
|닫힌 연결(미리 보기)|닫힌 연결 수입니다.<br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs 캡처 메트릭

Event Hubs에 캡처 기능을 사용하도록 설정하면 Event Hubs 캡처 메트릭을 모니터링할 수 있습니다. 다음 메트릭은 캡처를 사용하도록 설정하여 모니터링할 수 있는 항목을 설명합니다.

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
|캡처 백로그(미리 보기)|선택한 대상에 캡처될 수 있는 바이트 수입니다.<br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|
|캡처된 메시지(미리 보기)|지정된 기간 동안 선택한 대상에 캡처된 메시지 또는 이벤트의 수입니다.<br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|
|캡처된 바이트(미리 보기)|지정된 기간 동안 선택한 대상에 캡처된 바이트의 수입니다.<br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 차원: EntityName|

## <a name="metrics-dimensions"></a>메트릭 차원

Azure Event Hubs는 Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원합니다. 메트릭에 차원을 추가하는 것은 선택 사항입니다. 차원을 추가하지 않는 경우 메트릭은 네임스페이스 수준에서 지정됩니다. 

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
|EntityName| Event Hubs는 네임스페이스 아래에서 Event Hub 엔터티를 지원합니다.|

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 개요](../monitoring-and-diagnostics/monitoring-overview.md)를 참조하세요.
* GitHub의 [.NET을 사용하여 Azure Monitor 메트릭 검색](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) 샘플입니다. 

Event Hubs에 대한 자세한 내용은 다음 링크를 방문하세요.

* [Event Hubs 자습서](event-hubs-dotnet-standard-getstarted-send.md) 시작
* [Event Hubs FAQ](event-hubs-faq.md)
* [Event Hubs를 사용하는 샘플 애플리케이션](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



