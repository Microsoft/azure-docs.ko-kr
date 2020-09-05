---
title: 스트리밍 수집 이벤트 원본-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2으로 데이터를 스트리밍하는 방법에 대해 알아봅니다.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: a16a83408587a1b99485140174c45e457ac820f8
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855093"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Gen2 이벤트 원본

 Azure Time Series Insights Gen2 환경에는 최대 두 개의 스트리밍 이벤트 소스가 있을 수 있습니다. 입력으로 지원 되는 두 가지 유형의 Azure 리소스는 다음과 같습니다.

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

이벤트는 u t f-8로 인코딩된 JSON으로 전송 되어야 합니다.

## <a name="create-or-edit-event-sources"></a>이벤트 원본 만들기 또는 편집

이벤트 원본 리소스는 Azure Time Series Insights Gen2 환경 또는 다른 구독과 동일한 Azure 구독에 존재할 수 있습니다. [Azure Portal](time-series-insights-update-create-environment.md#create-a-preview-payg-environment), [Azure CLI](https://github.com/Azure/azure-cli-extensions/tree/master/src/timeseriesinsights), [ARM 템플릿](time-series-insights-manage-resources-using-azure-resource-manager-template.md)및 [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) 를 사용 하 여 환경의 이벤트 원본을 생성, 편집 또는 제거할 수 있습니다.

이벤트 원본을 연결 하면 Azure Time Series Insights Gen2 환경에서 가장 오래 된 이벤트부터 시작 하 여 현재 Iot 또는 Event Hub에 저장 된 모든 이벤트를 읽습니다.

> [!IMPORTANT]
>
> - Azure Time Series Insights Gen2 환경에 이벤트 원본을 연결할 때 초기 대기 시간이 길어질 수 있습니다.
> - 이벤트 원본 대기 시간은 현재 IoT Hub 또는 Event Hub에 있는 이벤트 수에 따라 달라집니다.
> - 이벤트 원본 데이터가 처음 수집된 후에는 대기 시간이 줄어듭니다. 대기 시간이 계속 긴 경우 Azure Portal을 통해 지원 티켓을 제출하세요.

## <a name="streaming-ingestion-best-practices"></a>스트리밍 수집 모범 사례

- 항상 Azure Time Series Insights Gen2 환경을 위한 고유한 소비자 그룹을 만들어 이벤트 원본의 데이터를 사용 합니다. 소비자 그룹을 다시 사용 하면 임의 연결이 끊길 수 있으며 데이터가 손실 될 수 있습니다.

- 동일한 Azure 지역에서 Azure Time Series Insights Gen2 환경과 IoT Hub 및/또는 Event Hubs를 구성 합니다. 별도의 지역에 이벤트 원본을 구성할 수는 있지만이 시나리오는 지원 되지 않으며 고가용성을 보장할 수 없습니다.

- 사용자 환경의 [처리량 속도 제한](./concepts-streaming-ingress-throughput-limits.md) 또는 파티션 당 제한을 초과 하지 마세요.

- 환경에서 데이터를 처리 하는 데 문제가 발생 하는 경우 알림을 받도록 지연 [경고](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency#monitor-latency-and-throttling-with-alerts) 를 구성 합니다.

- 거의 실시간 및 최근 데이터에만 스트리밍 수집을 사용합니다. 기록 데이터 스트리밍은 지원되지 않습니다.

- 속성을 이스케이프 하 고 JSON 데이터를 [결합 하 고 저장](./concepts-json-flattening-escaping-rules.md) 하는 방법을 이해 합니다.

- 이벤트 원본 연결 문자열을 제공할 때 최소 권한의 원칙을 따릅니다. Event Hubs의 경우, *보내기* 클레임만 사용 하 여 공유 액세스 정책을 구성 하 고 IoT Hub에 대해서는 *서비스 연결* 권한만 사용 합니다.

### <a name="historical-data-ingestion"></a>기록 데이터 수집

스트리밍 파이프라인을 사용 하 여 기록 데이터를 가져오는 Azure Time Series Insights Gen2에서 현재 지원 되지 않습니다. 이전 데이터를 사용자 환경으로 가져와야 하는 경우 아래 지침을 따릅니다.

- 라이브 데이터와 기록 데이터를 동시에 스트림하지 않습니다. 순서가 잘못된 데이터를 수집하면 쿼리 성능이 저하됩니다.
- 최상의 성능을 위해 기록 데이터를 지정된 시간 순서대로 수집합니다.
- 아래의 수집 처리량 속도 제한 내에서 유지합니다.
- 데이터가 웜 저장소 보존 기간보다 오래된 경우 웜 저장소를 사용하지 않도록 설정합니다.

## <a name="event-source-timestamp"></a>이벤트 원본 타임 스탬프

이벤트 원본을 구성할 때 타임 스탬프 ID 속성을 제공 하 라는 메시지가 표시 됩니다. Timestamp 속성은 시간이 지남에 따라 이벤트를 추적 하는 데 사용 됩니다 .이 시간은 $event로 사용 됩니다. [쿼리 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute) 및 Azure Time Series Insights Tsi 탐색기의 그리기 시리즈에서 $ts. 만든 시간에 속성이 제공 되지 않거나 타임 스탬프 속성이 이벤트에 없는 경우 이벤트의 IoT Hub 또는 이벤트 허브 큐에 넣은 시간이 기본값으로 사용 됩니다. Timestamp 속성 값은 UTC로 저장 됩니다.

일반적으로 사용자는 타임 스탬프 속성을 사용자 지정 하도록 선택 하 고, 센서 또는 태그가 기본 허브 큐에 대기 시간을 사용 하는 대신 읽기를 생성 한 시간을 사용 합니다. 이는 장치가 간헐적으로 연결 손실이 발생 하 고 지연 된 메시지의 일괄 처리가 Azure Time Series Insights Gen2 전달 되는 경우에 특히 필요 합니다.

사용자 지정 타임 스탬프가 중첩 된 JSON 개체 또는 배열 내에 있는 경우 [평면화 및 이스케이프 명명 규칙](concepts-json-flattening-escaping-rules.md)에 따라 올바른 속성 이름을 제공 해야 합니다. 예를 들어 [여기](concepts-json-flattening-escaping-rules.md#example-a) 에 표시 된 JSON 페이로드의 이벤트 원본 타임 스탬프는로 입력 해야 합니다 `"values.time"` .

### <a name="time-zone-offsets"></a>표준 시간대 오프셋

타임 스탬프는 ISO 8601 형식으로 전송 해야 하며 UTC로 저장 됩니다. 표준 시간대 오프셋이 제공 되는 경우에는 오프셋이 적용 되 고 시간을 UTC 형식으로 저장 하 고 반환 합니다. 오프셋의 형식이 잘못 된 경우 무시 됩니다. 솔루션에 원래 오프셋의 컨텍스트가 없는 경우 별도의 추가 이벤트 속성으로 오프셋 데이터를 전송 하 여 유지 되 고 응용 프로그램이 쿼리 응답에서 참조할 수 있는지 확인할 수 있습니다.

표준 시간대 오프셋은 다음 중 하나로 포맷 되어야 합니다.

± HHMMZ</br>
± HH: MM</br>
± HH: MMZ</br>

## <a name="next-steps"></a>다음 단계

- 이벤트를 저장 하는 방법을 이해 하려면 [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md) 을 참조 하세요.

- 사용자 환경의 [처리량 제한 사항](./concepts-streaming-ingress-throughput-limits.md) 이해
