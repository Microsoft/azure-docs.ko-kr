---
title: 메트릭 및 경고-Azure DNS
description: 이 학습 경로를 사용 하 여 Azure DNS 메트릭 및 경고를 시작 하세요.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 42acbc0d32b3ce5de4befcf112b68f611ad70542
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937449"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS 메트릭 및 경고
Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. 이 문서에서는 Azure DNS 서비스에 대한 메트릭 및 경고를 설명합니다.

## <a name="azure-dns-metrics"></a>Azure DNS 메트릭

Azure DNS은 고객이 서비스에서 호스트 되는 DNS 영역의 특정 측면을 모니터링할 수 있도록 하는 메트릭을 제공 합니다. 또한 Azure DNS 메트릭을 사용하여 관심 조건에 따라 경고를 구성하고 받을 수 있습니다. 메트릭은 [Azure Monitor 서비스](../azure-monitor/index.yml)를 통해 제공됩니다. Azure DNS는 DNS 영역에 대한 Azure Monitor를 통해 다음 메트릭을 제공합니다.

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Azure Monitor 설명서 페이지에서 [이러한 메트릭의 정의](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones)를 확인할 수도 있습니다.
>[!NOTE]
> 이때 이러한 메트릭은 Azure DNS에서 호스트되는 공용 DNS 영역에만 사용할 수 있습니다. Azure DNS에서 호스트되는 프라이빗 영역을 사용하는 경우 이러한 메트릭은 해당 영역에 대한 데이터를 제공하지 않습니다. 또한 메트릭 및 경고 기능은 Azure 퍼블릭 클라우드에서만 지원됩니다. 소버린 클라우드에 대한 지원은 나중에 제공될 예정입니다. 

메트릭을 볼 수 있는 가장 세분화 된 요소는 DNS 영역입니다. 현재 영역 내의 개별 리소스 레코드에 대한 메트릭을 볼 수 없습니다.

### <a name="query-volume"></a>쿼리 볼륨

Azure DNS의 *쿼리 볼륨* 메트릭은 DNS 영역에 대한 Azure DNS가 받는 DNS 쿼리의 볼륨(쿼리 트래픽)을 표시합니다. 측정 단위는 Count이며 집계는 시간의 경과를 통해 받는 모든 쿼리의 합계입니다. 

이 메트릭을 보려면 Azure Portal의 모니터 탭에서 메트릭(미리 보기) 탐색기 환경을 선택합니다. 리소스 드롭다운에서 DNS 영역을 선택하고, 쿼리 볼륨 메트릭을 선택하고, 집계로 합계를 선택합니다. 아래 스크린샷은 예제를 보여줍니다.  메트릭 탐색기 환경 및 차트에 대한 자세한 내용은 [Azure Monitor 메트릭 탐색기](../azure-monitor/platform/metrics-charts.md)를 참조하세요.

![쿼리 볼륨](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*그림: Azure DNS 쿼리 볼륨 메트릭*

### <a name="record-set-count"></a>레코드 집합 수
*레코드 집합 수* 메트릭은 DNS 영역에 대한 Azure DNS에서 레코드 집합의 수를 표시합니다. 영역에서 정의된 모든 레코드 집합이 계산됩니다. 측정 단위는 Count이며 집계는 모든 레코드 집합의 최대입니다. 이 메트릭을 보려면 Azure Portal의 **모니터** 탭에서 **메트릭(미리 보기)** 탐색기 환경을 선택합니다. **리소스** 드롭다운에서 DNS 영역을 선택하고, **레코드 집합 수** 메트릭을 선택한 다음, **집계**로 **최대**를 선택합니다. 메트릭 탐색기 환경 및 차트에 대한 자세한 내용은 [Azure Monitor 메트릭 탐색기](../azure-monitor/platform/metrics-charts.md)를 참조하세요. 

![레코드 집합 수](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*그림: Azure DNS 레코드 집합 수 메트릭*


### <a name="record-set-capacity-utilization"></a>레코드 집합 용량 사용률
Azure DNS의 *레코드 집합 용량 사용률* 메트릭은 DNS 영역에 대한 레코드 집합 용량 사용률의 백분율을 표시합니다. Azure DNS의 모든 DNS 영역은 영역에 허용되는 레코드 집합의 최대 수를 정의하는 레코드 집합 제한이 적용됩니다([DNS 제한](dns-zones-records.md#limits) 참조). 따라서 이 메트릭은 레코드 집합 제한에 도달할 때까지 얼마나 가까운지를 보여줍니다. 예를 들어 DNS 영역에 대해 구성된 500개의 레코드 집합이 있고, 영역에 5000개의 기본 레코드 집합 제한이 있는 경우 RecordSetCapacityUtilization 메트릭은 10%의 값을 표시합니다(5000을 500으로 나눠 얻음). 측정 단위는 **백분율**이며 **집계** 형식은 **최대**입니다. 이 메트릭을 보려면 Azure Portal의 모니터 탭에서 메트릭(미리 보기) 탐색기 환경을 선택합니다. 리소스 드롭다운에서 DNS 영역을 선택하고, 레코드 집합 용량 사용률 메트릭을 선택하고, 집계로 최대를 선택합니다. 아래 스크린샷은 예제를 보여줍니다. 메트릭 탐색기 환경 및 차트에 대한 자세한 내용은 [Azure Monitor 메트릭 탐색기](../azure-monitor/platform/metrics-charts.md)를 참조하세요. 

![레코드 집합 수](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*그림: Azure DNS 레코드 집합 용량 사용률 메트릭*

## <a name="alerts-in-azure-dns"></a>Azure DNS의 경고
Azure Monitor는 사용 가능한 메트릭 값에 대해 경고하는 기능을 제공합니다. DNS 메트릭은 새 경고 구성 환경에서 사용할 수 있습니다. [Azure Monitor 경고 설명서](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)에 자세히 설명된 대로 리소스로 DNS 영역을 선택하고, 메트릭 신호 유형을 선택하고, 경고 논리 및 **기간**및 **빈도**와 같은 다른 매개 변수를 구성합니다. 선택한 작업을 통해 경고가 배달되는 경고 조건이 충족되는 경우에 대한 [작업 그룹](../azure-monitor/platform/action-groups.md)을 세부적으로 정의할 수 있습니다. Azure Monitor 메트릭에 대한 경고를 구성하는 방법에 대한 자세한 내용은 [Azure Monitor를 사용하여 경고 만들기, 보기 및 관리](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
- [Azure DNS](dns-overview.md)에 대해 자세히 알아봅니다.
