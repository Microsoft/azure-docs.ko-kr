---
title: Azure HDInsight에서 대규모 스트리밍
description: 확장성 있는 HDInsight 클러스터를 통해 데이터 스트리밍을 사용하는 방법입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: e2b6cbabc9a0c727c9eb0232bd55048493b29128
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763721"
---
# <a name="streaming-at-scale-in-hdinsight"></a>HDInsight에서 대규모 스트리밍

실시간 빅 데이터 솔루션 동작 중인 데이터 작업을 수행 합니다. 일반적으로 이 데이터는 도착 시간에 가장 가치가 있습니다. 들어오는 데이터 스트림이 그 순간에 처리할 수 있는 것보다 큰 경우 리소스를 제한해야 할 수 있습니다. 또는 필요에 따라 노드를 추가하여 스트리밍 솔루션에 맞게 HDInsight 클러스터를 강화할 수 있습니다.


스트리밍 애플리케이션에서 하나 이상의 데이터 원본이 유용한 정보를 삭제하지 않고 빠르게 수집해야 하는 이벤트(경우에 따라 수백만 개/초)를 생성합니다. 들어오는 이벤트는 서비스(예: [Apache Kafka](kafka/apache-kafka-introduction.md) 또는 [Event Hubs](https://azure.microsoft.com/services/event-hubs/))에서 ‘이벤트 큐’라고도 하는 ‘스트림 버퍼링’으로 처리됩니다. 이벤트가 수집되면 ‘스트림 처리’ 계층(예: [Apache Storm](storm/apache-storm-overview.md) 또는 [Apache Spark 스트리밍](spark/apache-spark-streaming-overview.md)) 내에서 실시간 분석 시스템을 사용하여 데이터를 분석할 수 있습니다. 처리된 데이터는 장기 스토리지 시스템(예: [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/))에 저장될 수 있으며, 비즈니스 인텔리전스 대시보드(예: [Power BI](https://powerbi.microsoft.com), Tableau 또는 사용자 지정 웹 페이지)에 실시간으로 표시될 수 있습니다.


![HDInsight 스트리밍 패턴](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka는 처리량이 높고 대기 시간이 짧은 메시지 큐 서비스를 제공하며, 이제 OSS(오픈 소스 소프트웨어)의 Apache 제품군에 속해 있습니다. Kafka는 게시 및 가입 메시징 모델을 사용하고, 복제된 분산 클러스터에 안전하게 분할된 데이터 스트림을 저장합니다. 처리량이 증가함에 따라 Kafka는 선형으로 확장됩니다.

자세한 내용은 [HDInsight의 Apache Kafka 소개](kafka/apache-kafka-introduction.md)를 참조하세요.

## <a name="apache-storm"></a>Apache Storm

Apache Storm은 내결함성이 있는 오픈 소스 분산형 계산 시스템으로, Hadoop을 통해 실시간으로 데이터 스트림을 처리하도록 최적화되어 있습니다. 들어오는 이벤트의 핵심 데이터 단위는 튜플이며, 이는 키/값 쌍의 변경할 수 없는 집합입니다. 이러한 튜플의 바인딩되지 않은 시퀀스는 Spout에서 제공되는 스트림을 형성합니다. Spout은 스트리밍 데이터 원본(예: Kafka)을 래핑하고 튜플을 내보냅니다. Storm 토폴로지는 이러한 스트림에 대한 일련의 변환입니다.

자세한 내용은 [Azure HDInsight의 Apache Storm이란?](storm/apache-storm-overview.md)을 참조하세요.

## <a name="spark-streaming"></a>Spark 스트리밍

Spark 스트리밍은 Spark를 확장한 것으로, 일괄 처리에 사용하는 것과 동일한 코드를 재사용할 수 있도록 합니다. 일괄 처리 및 대화형 쿼리를 모두 동일한 애플리케이션에서 결합할 수 있습니다. Storm과 달리 Spark 스트리밍은 정확히 한 번만 처리되는 상태 저장 의미 체계를 제공합니다. [Kafka 직접 API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html)와 함께 사용하는 경우 Spark 스트리밍에서 모든 Kafka 데이터를 정확히 한 번 받을 수 있으므로 단 한 번의 종단 간 보장을 얻을 수 있습니다. Spark 스트리밍의 장점 중 하나는 클러스터 내에서 여러 노드를 사용할 때 장애가 발생한 노드를 빠르게 복구하는 내결함성 기능입니다.

자세한 내용은 [Apache Spark 스트리밍이란?](hdinsight-spark-streaming-overview.md)을 참조하세요.

## <a name="scaling-a-cluster"></a>클러스터 크기 조정

만드는 중에 클러스터의 노드 수를 지정할 수 있지만, 작업과 일치하도록 클러스터를 확장하거나 축소할 수 있습니다. 모든 HDInsight 클러스터를 사용하면 [클러스터의 노드 수를 변경](hdinsight-administer-use-portal-linux.md#scale-clusters)할 수 있습니다. 모든 데이터가 Azure Storage 또는 Data Lake Storage에 저장되므로 데이터 손실 없이 Spark 클러스터를 삭제할 수 있습니다.

기술 분리에 대한 장점이 있습니다. 예를 들어 Kafka는 이벤트 버퍼링 기술이므로 매우 IO 집약적이고 많은 처리 능력이 필요하지 않습니다. 이와 비교하여 Spark 스트리밍과 같은 스트림 프로세서는 계산 집약적이므로 더 강력한 VM이 필요합니다. 이러한 기술을 다른 클러스터로 분리함으로써 VM을 최대한 활용하면서 독립적으로 확장할 수 있습니다.

### <a name="scale-the-stream-buffering-layer"></a>스트림 버퍼링 계층의 크기 조정

스트림 버퍼링 기술인 Event Hubs와 Kafka는 모두 파티션을 사용하고, 소비자는 이러한 파티션을 읽습니다. 입력 처리량을 조정하려면 파티션 수를 강화해야 하며, 파티션을 추가하면 병렬 처리가 증가합니다. Event Hubs에서는 배포 후에 파티션 수를 변경할 수 없으므로 대상 크기 조정을 염두에 두고 시작하는 것이 중요합니다. Kafka를 사용하면 Kafka에서 데이터를 처리하는 동안에도 [파티션을 추가](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)할 수 있습니다. Kafka는 파티션을 다시 할당하는 도구(`kafka-reassign-partitions.sh`)를 제공합니다. HDInsight는 [파티션 복제본의 균형을 다시 조정하는 도구](https://github.com/hdinsight/hdinsight-kafka-tools)(`rebalance_rackaware.py`)를 제공합니다. 이 재조정 도구는 각 복제본이 별도의 장애 도메인 및 업데이트 도메인에 있는 방식으로 `kafka-reassign-partitions.sh` 도구를 호출하여 Kafka 랙을 인식하고 내결함성을 향상시킵니다.

### <a name="scale-the-stream-processing-layer"></a>스트림 처리 계층의 크기 조정

Apache Storm과 Spark 스트리밍은 모두 데이터를 처리하는 동안에도 작업자 노드를 클러스터에 추가할 수 있습니다.

Storm 크기 조정을 통해 추가된 새 노드를 활용하려면, 클러스터 크기를 늘리기 전에 시작된 모든 Storm 토폴로지의 균형을 다시 조정해야 합니다. 이 재조정은 Storm 웹 UI 또는 CLI를 사용하여 수행할 수 있습니다. 자세한 내용은 [Apache Storm 설명서](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)를 참조하세요.

Apache Spark는 애플리케이션 요구 사항에 따라 환경을 구성하기 위한 세 가지 주요 매개 변수(`spark.executor.instances`, `spark.executor.cores` 및 `spark.executor.memory`)를 사용합니다. *실행기*는 Spark 애플리케이션에 대해 시작되는 프로세스입니다. 실행기는 작업자 노드에서 실행되며 애플리케이션의 작업을 수행해야 합니다. 실행기의 기본 수와 각 클러스터에 대한 실행기의 크기는 작업자 노드의 수와 크기에 따라 계산됩니다. 이러한 숫자는 각 클러스터 헤드 노드의 `spark-defaults.conf` 파일에 저장됩니다.

이러한 세 매개 변수는 클러스터에서 실행되는 모든 애플리케이션에 대해 클러스터 수준에서 구성할 수 있으며, 각 애플리케이션별로 지정할 수도 있습니다. 자세한 내용은 [Apache Spark 클러스터용 리소스 관리](spark/apache-spark-resource-manager.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Storm 시작](storm/apache-storm-tutorial-get-started-linux.md)
* [HDInsight의 Apache Storm에 대한 예제 토폴로지](storm/apache-storm-example-topology.md)
* [HDInsight의 Apache Spark 소개](spark/apache-spark-overview.md)
* [HDInsight에서 Apache Kafka 시작](kafka/apache-kafka-get-started.md)
