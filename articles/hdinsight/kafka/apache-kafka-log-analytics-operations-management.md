---
title: Azure HDInsight의 Apache Kafka에 대 한 azure Monitor 로그
description: Azure Monitor 로그를 사용 하 여 Azure HDInsight의 Apache Kafka 클러스터에서 로그를 분석 하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: b4eced461f798f3cbf3ce968dae59cfb8f1a0363
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115097"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight의 Apache Kafka에 대한 로그 분석

Azure Monitor 로그를 사용 하 여 HDInsight의 Apache Kafka에서 생성 된 로그를 분석 하는 방법을 알아봅니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Apache Kafka에 대 한 Azure Monitor 로그를 사용 하도록 설정

HDInsight에 대 한 Azure Monitor 로그를 사용 하는 단계는 모든 HDInsight 클러스터에 대해 동일 합니다. 다음 링크를 사용하여 필요한 서비스를 만들고 구성하는 방법을 이해하세요.

1. Log Analytics 작업 영역을 만듭니다. 자세한 내용은 [Log Analytics 작업 영역 시작](https://docs.microsoft.com/azure/log-analytics) 문서를 참조하세요.

2. HDInsight 클러스터에서 Kafka를 만듭니다. 자세한 내용은 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md) 문서를 참조하세요.

3. Azure Monitor 로그를 사용 하도록 Kafka 클러스터를 구성 합니다. 자세한 내용은 참조는 [HDInsight 모니터링을 사용 하 여 Azure Monitor 로그](../hdinsight-hadoop-oms-log-analytics-tutorial.md) 문서.

    > [!NOTE]  
    > 클러스터에서 사용 하 여 Azure Monitor 로그를 사용 하도록 구성할 수도 있습니다는 `Enable-AzHDInsightOperationsManagementSuite` cmdlet. 이 cmdlet을 사용하려면 다음과 같은 정보가 필요합니다.
    >
    > * HDInsight 클러스터 이름.
    > * Azure Monitor 로그에 대 한 작업 영역 ID입니다. Log Analytics 작업 영역에서 작업 영역 ID를 찾을 수 있습니다.
    > * Log analytics 연결에 대 한 기본 키입니다. 기본 키를 찾으려면 Azure Portal에서 작업 영역을 열고, 왼쪽 메뉴에서 __고급 설정__을 선택합니다. 고급 설정에서 __연결된 원본__>__Linux 서버__를 선택합니다.


> [!IMPORTANT]  
> 데이터는 Azure Monitor 로그 수까지 약 20 분 정도 걸릴 수 있습니다.

## <a name="query-logs"></a>쿼리 로그

1. [Azure Portal](https://portal.azure.com)에서 Log Analytics 작업 영역을 선택합니다.

2. __로그 검색__을 선택합니다. 여기서 Kafka에서 수집한 데이터를 검색할 수 있습니다. 다음은 검색 예제입니다.

   * 디스크 사용량:`Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * CPU 사용량: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * 초당 들어오는 메시지: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

   * 초당 들어오는 바이트: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

   * 초당 나가는 바이트: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

     > [!IMPORTANT]  
     > 쿼리 값을 클러스터 관련 정보로 바꿉니다. 예를 들어 `ClusterName_s`를 클러스터의 이름으로 설정해야 합니다. `HostName_s`는 클러스터의 작업자 노드 도메인 이름으로 설정해야 합니다.

     `*`를 입력하여 기록된 모든 종류를 검색할 수도 있습니다. 현재 다음 로그를 쿼리에 사용할 수 있습니다.

     | 로그 형식 | 설명 |
     | ---- | ---- |
     | log\_kafkaserver\_CL | Kafka broker server.log |
     | log\_kafkacontroller\_CL | Kafka broker controller.log |
     | metrics\_kafka\_CL | Kafka JMX 메트릭 |

     ![CPU 사용량 검색의 이미지](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>다음 단계

Azure Monitor에 대 한 자세한 내용은 참조는 [Azure Monitor 개요](../../log-analytics/log-analytics-get-started.md) 문서.

Apache Kafka 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

 * [HDInsight 클러스터 간 Apache Kafka 미러링](apache-kafka-mirroring.md)
 * [HDInsight의 Apache Kafka 확장성 개선](apache-kafka-scalability.md)
 * [Apache Kafka에서 Apache Spark 스트리밍(DStream) 사용](../hdinsight-apache-spark-with-kafka.md)
 * [Apache Kafka에서 Apache Spark 구조적 스트림 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)
