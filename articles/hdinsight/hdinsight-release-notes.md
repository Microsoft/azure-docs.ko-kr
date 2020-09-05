---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0587a179b98a410cdba46b7817d86567f275f25d
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826823"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-08092020"></a>릴리스 날짜: 08/09/2020

이 릴리스는 HDInsight 4.0에만 적용 됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래 변경 내용이 표시 되지 않으면 며칠 동안 해당 지역에서 릴리스가 라이브 될 때까지 기다립니다.

## <a name="new-features"></a>새로운 기능
### <a name="support-for-sparkcruise"></a>SparkCruise 지원
SparkCruise는 Spark의 자동 계산 재사용 시스템입니다. 이전 쿼리 워크 로드를 기준으로 구체화 하는 공통 부분식을 선택 합니다. SparkCruise는 이러한 하위 식을 쿼리 처리의 일부로 구체화 하 고 계산 재사용은 백그라운드에서 자동으로 적용 됩니다. Spark 코드를 수정 하지 않고 SparkCruise의 이점을 누릴 수 있습니다.
 
### <a name="support-hive-view-for-hdinsight-40"></a>HDInsight 4.0에 대 한 Hive 보기 지원
Apache Ambari Hive 보기는 웹 브라우저에서 Hive 쿼리를 작성, 최적화 및 실행 하는 데 도움이 되도록 설계 되었습니다. Hive 보기는이 릴리스에서부터 HDInsight 4.0 클러스터에 대해 기본적으로 지원 됩니다. 기존 클러스터에는 적용 되지 않습니다. 기본 제공 Hive 보기를 가져오려면 클러스터를 삭제 하 고 다시 만들어야 합니다.
 
### <a name="support-tez-view-for-hdinsight-40"></a>HDInsight 4.0에 대 한 Tez 보기 지원
Apache Tez 뷰는 Hive Tez 작업의 실행을 추적 하 고 디버그 하는 데 사용 됩니다. Tez 뷰는이 릴리스에서부터 HDInsight 4.0에 대해 기본적으로 지원 됩니다. 기존 클러스터에는 적용 되지 않습니다. 기본 제공 Tez 뷰를 가져오려면 클러스터를 삭제 하 고 다시 만들어야 합니다.

## <a name="deprecation"></a>사용 중단
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>HDInsight 3.6 Spark 클러스터의 Spark 2.1 및 2.2 사용 중단
7 월 1 2020부터 고객은 HDInsight 3.6에서 Spark 2.1 및 2.2을 사용 하 여 새 Spark 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 3.6의 Spark 2.3으로 전환하는 것이 좋습니다.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>HDInsight 4.0 Spark 클러스터의 Spark 2.3 사용 중단
7 월 1 2020부터 고객이 HDInsight 4.0의 Spark 2.3을 사용 하 여 새 Spark 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Spark 2.4로 전환하는 것이 좋습니다.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>HDInsight 4.0 Kafka 클러스터의 Kafka 1.1 사용 중단
2020년 7월 1일부터 고객은 HDInsight 4.0의 Kafka 1.1을 사용하여 새 Kafka 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Kafka 2.1로 전환하는 것이 좋습니다.

## <a name="behavior-changes"></a>동작 변경 내용
### <a name="ambari-stack-version-change"></a>Ambari stack 버전 변경
이 릴리스에서 Ambari 버전은 2.x에서 4.1로 변경 됩니다. Ambari UI >에서 Ambari 버전을 가져올 수 있습니다.

## <a name="upcoming-changes"></a>예정된 변경
앞으로 주의 해야 하는 주요 변경 내용이 없습니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속해서 클러스터 안정성과 성능을 향상시킵니다. 

다음은 Hive에 대 한 백 포팅 된 JIRAs.
* [HIVE-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [HIVE-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [HIVE-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [HIVE-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [HIVE-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [HIVE-18786](https://issues.apache.org/jira/browse/HIVE-18786)

다음은 HBase에 대 한 백 포팅 된 JIRAs
* [HBASE-21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE-24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE-24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스에 대한 구성 요소 버전이 변경되지 않았습니다. [이 문서](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)에서 hdinsight 4.0 및 hdinsight 3.6의 최신 구성 요소 버전을 찾을 수 있습니다.

## <a name="known-issues"></a>알려진 문제

Azure Portal에서 사용자가 공개 키의 SSH 인증 유형을 사용 하 여 Azure HDInsight 클러스터를 만들 때 오류가 발생 하는 문제가 해결 되었습니다. 사용자가 **검토 + 만들기**를 클릭 하면 "SSH 사용자 이름에서 3 개의 연속 문자를 포함 하지 않아야 합니다." 오류가 표시 됩니다. 이 문제는 해결 되었지만 수정 된 보기를 로드 하려면 CTRL + f 5를 눌러 브라우저 캐시를 새로 고쳐야 할 수도 있습니다. 이 문제에 대 한 해결 방법은 ARM 템플릿을 사용 하 여 클러스터를 만드는 것 이었습니다. 
