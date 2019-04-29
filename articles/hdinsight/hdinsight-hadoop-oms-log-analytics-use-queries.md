---
title: Azure HDInsight 클러스터를 모니터링 하려면 로그 쿼리 Azure 모니터링
description: HDInsight 클러스터에서 실행 하는 작업을 모니터링 하려면 Azure Monitor 로그 쿼리를 실행 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: cbaaed3fff99778bfab1feeacdab02bf8245a85a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761226"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>HDInsight 클러스터를 모니터링 하려면 로그 쿼리 Azure 모니터링

Azure Monitor 로그를 사용 하 여 Azure HDInsight 클러스터를 모니터링 하는 방법에 몇 가지 기본 시나리오에 알아봅니다.

* [HDInsight 클러스터 메트릭 분석](#analyze-hdinsight-cluster-metrics)
* [특정 로그 메시지 검색](#search-for-specific-log-messages)
* [이벤트 경고 만들기](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>필수 조건

* 있어야 Azure Monitor 로그를 사용 하 여 HDInsight 클러스터를 구성 하 고 HDInsight 클러스터와 관련 Azure Monitor 로그 모니터링 작업 영역에 솔루션을 추가 합니다. 자세한 내용은 [HDInsight 클러스터와 함께 사용 하 여 Azure Monitor 로그](hdinsight-hadoop-oms-log-analytics-tutorial.md)합니다.

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight 클러스터 메트릭 분석

HDInsight 클러스터에 대한 특정 메트릭을 조회하는 방법을 알아봅니다.

1. Azure Portal에서 HDInsight 클러스터에 연결된 Log Analytics 작업 영역을 엽니다.
2. **로그 검색** 타일을 선택합니다.
3. Azure Monitor 로그를 사용 하 여 선택한 후에 구성 된 모든 HDInsight 클러스터에 대 한 모든 사용 가능한 메트릭에 대 한 모든 메트릭을 검색 하려면 검색 상자에 다음 쿼리를 입력 **실행**합니다.

        search *

    ![모든 메트릭 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "모든 메트릭 검색")

    출력은 다음과 유사합니다.

    ![모든 메트릭 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "모든 메트릭 검색 출력")

5. 왼쪽 창의 **형식**에서 심층적으로 분석하려는 메트릭을 선택한 다음, **적용**을 선택합니다. 다음 스크린샷은 `metrics_resourcemanager_queue_root_default_CL` 형식을 선택한 것입니다.

    > [!NOTE]  
    > **[+]자세히** 단추를 선택하여 원하는 메트릭을 찾을 수도 있습니다. 또한 **적용** 단추는 목록의 아래쪽에 있으므로 스크롤해야 볼 수 있습니다.

    텍스트 상자의 쿼리가 다음 스크린샷에서 강조 표시된 상자에 표시된 내용으로 변경됩니다.

    ![특정 메트릭 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "특정 메트릭 검색")

6. 이 특정 메트릭을 자세히 분석할 수 있습니다. 예를 들어 다음 쿼리를 사용하여 클러스터 이름별로 분류되고 10분 간격으로 사용된 리소스의 평균에 기반한 기존 출력을 구체화할 수 있습니다.

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. 사용된 리소스의 평균에 따라 구체화하는 대신 다음 쿼리를 사용하여 10분 간격으로 최대 리소스가 사용된 시점(90번째 및 95번째 백분위수)에 따라 결과를 구체화할 수 있습니다.

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>특정 로그 메시지 검색

특정 시간 범위 중의 오류 메시지를 검색하는 방법을 알아봅니다. 다음에 나오는 단계는 관심 있는 오류 메시지에 도달할 수 있는 방법에 대한 하나의 예입니다. 찾으려는 오류를 확인할 수 있는 속성을 사용하면 됩니다.

1. Azure Portal에서 HDInsight 클러스터에 연결된 Log Analytics 작업 영역을 엽니다.
2. **로그 검색** 타일을 선택합니다.
3. 다음 Azure Monitor 로그를 사용 하도록 구성 된 모든 HDInsight 클러스터에 대 한 모든 오류 메시지를 검색할 쿼리를 선택한 형식 **실행**합니다. 

         search "Error"

    다음 출력과 유사한 결과가 표시됩니다.

    ![모든 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "모든 오류 검색 출력")

4. 왼쪽 창의 **형식** 범주에서 심층적으로 분석하려는 오류 형식을 선택한 다음, **적용**을 선택합니다.  결과가 사용자가 선택한 오류 형식만 표시하도록 구체화된 것을 볼 수 있습니다.
5. 왼쪽 창에서 사용할 수 있는 옵션을 사용하여 이 특정 오류 목록을 자세히 분석할 수 있습니다. 예를 들면 다음과 같습니다.

    - 특정 작업자 노드의 오류 메시지 보기

        ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "특정 오류 검색 출력")

    - 특정 시간에 발생한 오류 보기

        ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "특정 오류 검색 출력")

6. 특정 오류 보기 **[+]자세히 표시**를 선택하면 실제 오류 메시지를 볼 수 있습니다.

    ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "특정 오류 검색 출력")

## <a name="create-alerts-for-tracking-events"></a>추적 이벤트에 대한 경고 만들기

경고를 만드는 첫 번째 단계는 경고를 트리거하는 쿼리에 도달하는 것입니다. 경고를 만들려는 모든 쿼리를 사용할 수 있습니다.

1. Azure Portal에서 HDInsight 클러스터에 연결된 Log Analytics 작업 영역을 엽니다.
2. **로그 검색** 타일을 선택합니다.
3. 경고를 만들려는 항목에 대해 다음 쿼리를 실행한 다음, **실행**을 선택합니다.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    쿼리는 HDInsight 클러스터에서 실행되는 실패한 애플리케이션의 목록을 제공합니다.

4. 페이지 맨 위에서 **새 경고 규칙**을 선택합니다.

    ![쿼리를 입력하여 경고 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "쿼리를 입력하여 경고 만들기")

5. **규칙 만들기** 창에서 쿼리 및 기타 세부 정보를 입력하여 경고를 만든 다음, **경고 규칙 만들기**를 선택합니다.

    ![쿼리를 입력하여 경고 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "쿼리를 입력하여 경고 만들기")

기존 경고를 편집하거나 삭제하려면 다음을 수행합니다.

1. Azure Portal에서 Log Analytics 작업 영역을 엽니다.
2. 왼쪽 메뉴에서 **경고**를 선택합니다.
3. 편집하거나 삭제하려는 경고를 선택합니다.
4. 다음 옵션이 있습니다. **저장**, **취소**, **사용 안 함** 및 **삭제**.

    ![HDInsight Azure Monitor의 로그 경고 삭제 편집](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-metric.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

* [Azure Monitor에서 뷰 디자이너를 사용 하 여 사용자 지정 뷰 만들기](../azure-monitor/platform/view-designer.md)
* [만들기, 보기 및 Azure Monitor를 사용 하 여 메트릭 경고 관리](../azure-monitor/platform/alerts-metric.md)
