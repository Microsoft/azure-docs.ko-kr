---
title: Azure 데이터 탐색기 성능, 상태 및 메트릭 사용 하 여 사용량 모니터링
description: Azure Data Explorer 메트릭을 사용 하 여 클러스터의 성능, 상태 및 사용량을 모니터링 하는 방법에 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: a9c9f4d827d21c374bebba9d39e33b0bcad8a83e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826801"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Azure 데이터 탐색기 성능, 상태 및 메트릭 사용 하 여 사용량 모니터링

Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. Azure Data Explorer를 사용하려면 먼저 클러스터를 만들고 이 클러스터에 데이터베이스를 하나 이상 만듭니다. 그런 다음, 데이터베이스에 대해 쿼리를 실행할 수 있도록 데이터베이스에 데이터를 수집(로드)합니다. Azure 데이터 탐색기 메트릭은 클러스터 리소스의 성능과 상태에 대 한 핵심 지표를 제공합니다. Azure 데이터 탐색기 클러스터 상태 및 독립 실행형 메트릭으로 특정 시나리오에서 성능을 모니터링 하려면이 문서에 자세히 설명 되어 있는 메트릭을 사용 합니다. 메트릭을 기준으로 사용할 수도 있습니다 operational [Azure 대시보드](/azure/azure-portal/azure-portal-dashboards) 하 고 [Azure Alerts](/azure/azure-monitor/platform/alerts-metric-overview)합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 없으면 만듭니다는 [무료 Azure 계정](https://azure.microsoft.com/free/)합니다.

* 만들기는 [클러스터와 데이터베이스](create-cluster-database-portal.md)합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="using-metrics"></a>메트릭을 사용 하 여

Azure 데이터 탐색기 클러스터에서 선택 **메트릭을** 메트릭 창 열기 및 클러스터에 대 한 분석을 시작 합니다.

![메트릭 선택](media/using-metrics/select-metrics.png)

메트릭 창의:

![메트릭 창](media/using-metrics/metrics-pane.png)

1. 메트릭 차트를 만들려면 **메트릭을** 이름 및 관련 **집계** 아래 설명 된 대로 메트릭 당 합니다. **리소스** 하 고 **메트릭 Namespace** 선택기는 Azure 데이터 탐색기 클러스터에 미리 선택 합니다.

    **메트릭** | **단위** | **집계** | **메트릭 설명**
    |---|---|---|---|
    | 캐시 사용률 | 백분율 | Avg, Max, Min | 클러스터에서 사용 중인 할당 된 캐시 리소스의 백분율입니다. 캐시는 정의 된 캐시 정책에 따라 사용자 작업에 할당 된 SSD의 크기를 가리킵니다. 평균 캐시 사용률을 80% 이하의 클러스터에 대 한 지속 가능한 상태입니다. 평균 캐시 사용률을 80% 인 경우에 클러스터 여야 합니다 [확장할](manage-cluster-scale-up.md) 저장소에 액세스에 최적화 된 가격 책정 계층 또는 [확장할](manage-cluster-scale-out.md) 더 많은 인스턴스를 합니다. 또는 캐시 정책 (캐시에 더 적은 일)를 조정 합니다. 캐싱 정책에 따라 캐시 데이터의 크기가 더 큰 캐시 사용률 100%를 넘는 경우는 클러스터에서 캐시의 총 크기입니다. |
    | CPU | 백분율 | Avg, Max, Min | 클러스터의 컴퓨터에서 사용 중인 할당 된 계산 리소스의 백분율입니다. 80% 이하의 평균 CPU가 클러스터에 대 한 유지 합니다. CPU의 최대값은 100% 즉, 데이터를 처리 하는 데 대 한 추가 계산 리소스가 없습니다. 클러스터를 정상적으로 수행 되지 않습니다 하는 경우 차단 된 특정 Cpu 있는지 확인 하려면 CPU의 최대 값을 확인 합니다. |
    | 이벤트 처리 (Event Hubs) | 카운트 | Max, Min, Sum | 이벤트의 총 event hubs에서 읽기 및 클러스터에 의해 처리 합니다. 이벤트를 거부 하는 이벤트 및 클러스터 엔진에서 허용 하는 이벤트로 분할 됩니다. |
    | 수집 대기 시간 | 초 | Avg, Max, Min | 대기 시간 쿼리를 준비 될 때까지 클러스터의 데이터를 받은 시간에서 수집 된 데이터입니다. 수집 대기 시간 수집 시나리오에 따라 달라 집니다. |
    | 수집 결과 | 카운트 | 카운트 | 성공 및 실패 하 여 수집 작업의 총 수입니다. 사용 하 여 **분할을 적용할** 를 만들고 버킷 성공 및 실패 결과 크기 분석 (**값** > **상태**).|
    | 수집 사용률 | 백분율 | Avg, Max, Min | 수집 하는 데 용량 정책에 할당 된 총 리소스에서 데이터를 수집 하는 데 사용 되는 실제 리소스의 백분율입니다. 기본 용량 정책에는 512 동시 수집 작업 또는 수집에 투자 하 고 클러스터 리소스의 75% 넘지 않습니다. 평균 수집 사용률이 80% 이하의 클러스터에 대 한 지속 가능한 상태입니다. 수집 사용률의 최대값은 100% 모든 클러스터 수집 기능이 사용 되 고 수집 큐를 발생할 수 있습니다. |
    | Mb 단위로 수집 볼륨 | 카운트 | Max, Min, Sum | 압축 하기 전에 클러스터 (MB)에 수집 된 데이터의 총 크기입니다. |
    | 연결 유지 | 카운트 | 평균 | 클러스터의 응답성을 추적합니다. 값 1을 반환 하는 완벽 하 게 반응 형 클러스터 및 차단 되거나 연결이 끊어진 클러스터 0을 반환 합니다. |
    | 쿼리 기간 | 초 | Count, Avg, Min, Max, Sum | 총 쿼리 결과 받을 때까지 시간 (네트워크 대기 시간을 포함 하지 않습니다). |
    | | | |

    추가 정보에 대 한 [지원 되는 Azure 데이터 탐색기 클러스터 메트릭](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. 선택 된 **메트릭을 추가** 동일한 차트에 표시 되는 여러 메트릭을 보려면 단추입니다.
3. 선택 된 **+ 새 차트** 하나의 뷰에 여러 차트를 표시 하는 단추입니다.
4. 시간 선택기를 사용 하 여 시간 범위를 변경 하려면 (기본: 지난 24 시간).
5. 사용 하 여 [ **필터 추가** 하 고 **분할 적용** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) 차원이 포함 된 메트릭에 대 한 합니다.
6. 선택 **대시보드에 고정** 다시 볼 수 있도록 차트 구성을 대시보드에 추가 합니다.
7. 설정할 **새 경고 규칙** 집합 조건을 사용 하 여 메트릭을 시각화할 수 있습니다. 새 경고 규칙에는 사용자 차트의 대상 리소스, 메트릭, 분할 및 필터 차원이 포함됩니다. 이러한 설정을 수정 합니다 [경고 규칙 만들기 창](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)합니다.

사용 하 여 추가 정보를 [메트릭 탐색기](/azure/azure-monitor/platform/metrics-getting-started)합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: Azure Data Explorer에서 데이터 쿼리](web-query-data.md)
