---
title: 스트리밍 수집 처리량 제한-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 수신 처리량 제한에 대해 알아봅니다.
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: a9ac55802e4bcc435bb4bd6fd4af8977db9fd293
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950462"
---
# <a name="streaming-ingestion-throughput-limits"></a>스트리밍 수집 처리량 제한

Azure Time Series Insights Gen2 스트리밍 데이터 수신 제한 사항은 아래에 설명 되어 있습니다.

> [!TIP]
> 모든 제한의 포괄적인 목록은 [Azure Time Series Insights Gen2 환경 계획](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) 을 참조 하세요.

## <a name="per-environment-limitations"></a>환경당 제한

일반적으로 수신 속도는 조직 내 디바이스 수, 이벤트 내보내기 빈도 및 각 이벤트의 크기에 대한 요소로 표시됩니다.

*  **디바이스 수** × **이벤트 내보내기 빈도** × **각 이벤트의 크기**

기본적으로 Azure Time Series Insights Gen2는 **Azure Time Series Insights Gen2 환경 당 최대 1mbps (초당 메가바이트)** 의 속도로 들어오는 데이터를 수집할 수 있습니다. [허브 파티션](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)마다 추가 제한이 있습니다.

> [!TIP]
>
> * 요청에서 최대 8mbps의 수집 속도를 지원할 수 있습니다.
> * Azure Portal을 통해 지원 티켓을 제출하여 처리량을 늘려야 하는 경우 문의해 주세요.
 
* **예제 1:**

    Contoso Shipping에는 이벤트를 분당 3회 내보내는 100,000개의 디바이스가 있습니다. 이벤트의 크기는 200바이트입니다. Azure Time Series Insights Gen2 이벤트 원본으로 4 개의 파티션이 있는 IoT Hub를 사용 하 고 있습니다.

    * Azure Time Series Insights Gen2 환경에 대 한 수집 비율은 **10만 장치 * 200 바이트/이벤트 * (3/60 이벤트/초) = 1 MBps**입니다.
    * 분산 된 파티션이 있는 경우 파티션당 수집 률은 0.25 MBps가 됩니다.
    * Contoso 배송료의 수집 률은 크기 제한 내에 있습니다.

* **예 2:**

    Contoso 제 분석에는 1 초 마다 이벤트를 내보내는 4만 장치가 있습니다. Azure Time Series Insights Gen2 이벤트 원본으로 파티션 수가 2 인 이벤트 허브를 사용 하 고 있습니다. 이벤트의 크기는 200바이트입니다.

    * 환경 수집 율은 **4만 장치 * 200 바이트/이벤트 * 1 이벤트/초 = 8 MBps**입니다.
    * 분산 된 파티션이 있다고 가정 하면 파티션 전송률이 4 MBps가 됩니다.
    * Contoso Fleet Analytics의 수집 속도는 환경 및 파티션 제한을 초과합니다. Azure Portal를 통해 Azure Time Series Insights Gen2 요청을 제출 하 여 환경에 대 한 수집 률을 높이고 제한 내에 추가 파티션이 있는 이벤트 허브를 만들 수 있습니다.

## <a name="hub-partitions-and-per-partition-limits"></a>허브 파티션 및 파티션당 제한

Azure Time Series Insights Gen2 환경을 계획할 때 Azure Time Series Insights Gen2에 연결할 이벤트 원본의 구성을 고려 하는 것이 중요 합니다. Azure IoT Hub 및 Event Hubs는 모두 파티션을 활용하여 이벤트 처리를 위한 수평 크기 조정을 사용하도록 설정합니다. 

*파티션*은 허브에서 저장되는 순서가 지정된 이벤트 시퀀스입니다. 파티션 수는 허브 만들기 중에 설정되며 변경할 수 없습니다.

Event Hubs 분할 모범 사례는 [필요한 파티션은 몇 개인가요?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)를 검토하세요.

> [!NOTE]
> Azure Time Series Insights Gen2에 사용 되는 대부분의 IoT Hub에는 4 개의 파티션만 필요 합니다.

Azure Time Series Insights Gen2 환경에 대 한 새 허브를 만들지, 아니면 기존 환경에서 사용할 수 있는지에 관계 없이 파티션 수집 율을 계산 하 여 제한 내에 있는지 확인 해야 합니다. 

Azure Time Series Insights Gen2는 현재 **파티션 제한인 0.5 MBps 당**일반적으로 발생 합니다.

### <a name="iot-hub-specific-considerations"></a>IoT Hub 관련 고려 사항

IoT Hub에서 디바이스가 만들어지면 파티션에 영구적으로 할당됩니다. 이렇게 하면 할당이 변경되지 않으므로 IoT Hub에서 이벤트 순서를 보장할 수 있습니다.

고정 파티션 할당은 IoT Hub 다운스트림에서 전송 된 데이터를 수집 Gen2 인스턴스에도 영향을 Azure Time Series Insights 줍니다. 여러 디바이스에서 동일한 게이트웨이 디바이스 ID를 사용하여 메시지를 허브로 전달하는 경우 동일한 파티션에 동시에 도달하여 파티션당 크기 제한을 초과할 수 있습니다.

**영향**:

* 단일 파티션이 한도를 초과 하 여 수집 하는 비율이 지속 되는 경우 IoT Hub 데이터 보존 기간이 초과 되기 전에 Azure Time Series Insights Gen2가 모든 장치 원격 분석을 동기화 하지 않을 수 있습니다. 따라서 수집 제한이 지속적으로 초과되면 전송된 데이터가 손실될 수 있습니다.

이러한 상황을 완화하려면 다음과 같은 모범 사례를 따르는 것이 좋습니다.

* 솔루션을 배포하기 전에 환경당 및 파티션당 수집 속도를 계산합니다.
* IoT Hub 디바이스가 가능한 최대의 범위까지 부하 분산되었는지 확인합니다.

> [!IMPORTANT]
> IoT Hub를 이벤트 원본으로 사용 하는 환경의 경우 사용 중인 허브 장치 수를 사용 하 여 수집 율을 계산 하 여 해당 비율이 파티션 제한 당 0.5 MBps 미만인 지 확인할 수 있습니다.
>
> * 여러 이벤트가 동시에 도착 하더라도 제한을 초과 하지 않습니다.

  ![IoT Hub 파티션 다이어그램](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

허브 처리량 및 파티션을 최적화하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [IoT Hub 크기 조정](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event Hub 크기 조정](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event Hub 파티션](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>다음 단계

* 데이터 [저장소](./concepts-storage.md) 에 대해 읽기