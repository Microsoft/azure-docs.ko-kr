---
title: Apache Spark 클러스터 구성 최적화 - Azure HDInsight
description: Azure HDInsight의 처리량을 최대화하도록 Apache Spark 클러스터를 구성하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperfq1
ms.openlocfilehash: 456b955d40e417c9851734b0acaa86a14c9c83f0
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757849"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Apache Spark에 대 한 클러스터 구성 최적화

이 문서에서는 Azure HDInsight에서 최상의 성능을 위해 Apache Spark 클러스터의 구성을 최적화하는 방법을 설명합니다.

## <a name="overview"></a>개요

Spark 클러스터 워크로드에 따라 기본이 아닌 Spark 구성을 사용하여 보다 최적화된 Spark 작업이 실행되도록 할 수 있습니다.  샘플 워크로드로 벤치마크 테스트를 수행하여 기본이 아닌 클러스터 구성의 유효성을 검사합니다.

다음은 조정할 수 있는 몇 가지 공통 매개 변수입니다.

|매개 변수 |Description |
|---|---|
|--num-executors|실행기의 적절한 수를 설정합니다.|
|--executor-cores|각 실행기의 코어 수를 설정합니다. 일반적으로 다른 프로세스가 사용 가능한 메모리 중 일부를 소비하기 때문에 중간 규모의 실행기를 사용해야 합니다.|
|--executor-memory|각 실행기의 메모리 크기를 설정하며 이를 통해 YARN에서 힙 크기를 제어합니다. 실행 오버헤드를 위해 약간의 메모리를 남겨 두세요.|

## <a name="select-the-correct-executor-size"></a>올바른 실행기 크기 선택

실행기 구성을 결정할 때 Java GC(가비지 수집) 오버헤드를 고려합니다.

* 실행기 크기를 줄이는 요소:
    1. GC 오버헤드를 10% 미만으로 유지하려면 힙 크기를 32GB 미만으로 줄입니다.
    2. GC 오버헤드를 10% 미만으로 유지하려면 코어 수를 줄입니다.

* 실행기 크기를 늘리는 요소:
    1. 실행기 간 통신 오버헤드를 줄입니다.
    2. 대규모 클러스터(100개가 넘는 실행기)에서 실행기(N2) 간의 열린 연결 수를 줄입니다.
    3. 메모리를 많이 사용하는 작업에 적합하도록 힙 크기를 늘립니다.
    4. 선택 사항: 실행기당 메모리 오버헤드를 줄입니다.
    5. 선택 사항: CPU를 초과 구독하여 사용률 및 동시성을 늘립니다.

실행기 크기를 선택할 때 일반적 규칙은 다음과 같습니다.

1. 실행기당 30GB로 시작하고 사용 가능한 컴퓨터 코어를 배포합니다.
2. 대규모 클러스터(100개가 넘는 실행기)에 대해서는 실행기 코어 수를 늘립니다.
3. 평가판 실행과 GC 오버헤드와 같은 이전 요인을 기준으로 크기를 수정합니다.

동시 쿼리를 실행할 때는 다음 사항을 고려합니다.

1. 실행기당 30GB와 모든 컴퓨터 코어를 사용하여 시작합니다.
2. CPU를 초과 구독하여(약 30%의 대기 시간 개선) 여러 병렬 Spark 애플리케이션을 만듭니다.
3. 병렬 애플리케이션에서 쿼리를 배포합니다.
4. 평가판 실행과 GC 오버헤드와 같은 이전 요인을 기준으로 크기를 수정합니다.

Ambari를 사용하여 실행기를 구성하는 방법에 대한 자세한 내용은 [Apache Spark 설정 - Spark 실행기](apache-spark-settings.md#configuring-spark-executors)를 참조하세요.

타임라인 보기를 확인하여 이상값 또는 다른 성능 문제의 쿼리 성능을 모니터링합니다. SQL 그래프, 작업 통계 등을 확인하는 방법도 있습니다. YARN 및 Spark 기록 서버를 사용하여 Spark 작업을 디버깅하는 방법에 대한 자세한 내용은 [Azure HDInsight에서 실행 중인 Apache Spark 작업 디버그](apache-spark-job-debugging.md)를 참조하세요. YARN Timeline Server를 사용하는 방법에 대한 팁은 [Apache Hadoop YARN 애플리케이션 로그에 액세스](../hdinsight-hadoop-access-yarn-app-logs-linux.md)를 참조하세요.

경우에 따라 하나 또는 몇 개의 실행기가 다른 실행기보다 느린 경우 작업을 실행하는 데 훨씬 많은 시간이 소요될 수 있습니다. 이러한 느린 현상은 대규모 클러스터(30개가 넘는 노드)에서 자주 발생합니다. 이 경우 스케줄러가 느린 작업을 보정할 수 있도록 작업을 더 많은 수로 나눕니다. 예를 들어 애플리케이션에서 실행기 코어의 수보다 최소한 두 배의 작업이 있어야 합니다. `conf: spark.speculation = true`를 사용하면 작업을 추측에 근거하여 실행해 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Apache Spark 데이터 처리 최적화](optimize-cluster-configuration.md)
* [Apache Spark 데이터 스토리지 최적화](optimize-data-storage.md)
* [Apache Spark 메모리 사용량 최적화](optimize-memory-usage.md)
