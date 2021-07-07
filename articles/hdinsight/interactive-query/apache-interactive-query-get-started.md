---
title: Azure HDInsight의 대화형 쿼리란?
description: Azure HDInsight의 대화형 쿼리 소개(Apache Hive LLAP라고도 함)
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 464521cee3242859294de42d2086e1bea33bb4c4
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290926"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Azure HDInsight의 대화형 쿼리란?

대화형 쿼리(Apache Hive LLAP 또는 [짧은 대기 시간 분석 처리](https://cwiki.apache.org/confluence/display/Hive/LLAP)라고도 함)는 Azure HDInsight [클러스터 유형](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)입니다. Interactive Query에서는 메모리 내 캐싱을 지원하여 Apache Hive 쿼리를 더 강화된 대화형 방식으로 더 빠르게 수행할 수 있습니다. 고객은 대화형 쿼리를 사용하여 Azure Storage 및 Azure Data Lake Storage에 저장된 데이터를 초고속 방식으로 쿼리합니다. 개발자 및 데이터 과학자는 대화형 쿼리를 사용하여 가장 선호하는 BI 도구를 사용하여 쉽게 빅 데이터 작업을 수행할 수 있습니다. HDInsight 대화형 쿼리는 빅 데이터에 쉽게 액세스할 수 있도록 몇 가지 도구를 지원합니다.

[!INCLUDE [hdinsight-price-change](../includes/hdinsight-enhancements.md)]

Interactive Query 클러스터는 Apache Hadoop 클러스터와 다릅니다. Hive 서비스만 포함합니다.

Interactive Query 클러스터의 Hive 서비스는 Apache Ambari Hive 보기, Beeline 및 Microsoft Hive ODBC(Open Database Connectivity) Driver를 통해서만 액세스할 수 있습니다. Hive 콘솔, Templeton, Azure 클래식 CLI 또는 Azure PowerShell을 통해서는 액세스할 수 없습니다.

## <a name="create-an-interactive-query-cluster"></a>대화형 쿼리 클러스터 만들기

HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. 대화형 쿼리 클러스터 형식을 선택합니다.

> [!IMPORTANT]
> 대화형 쿼리 클러스터의 최소 헤드 노드 크기는 Standard_D13_v2입니다. 자세한 내용은 [Azure VM 크기 조정 차트](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)를 참조하세요.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Interactive Query에서 Apache Hive 쿼리 실행

Hive 쿼리를 실행하려면 다음 옵션이 있어야 합니다.

|메서드 |설명 |
|---|---|
|Microsoft Power BI|[Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Apache Hive 데이터 시각화](./apache-hadoop-connect-hive-power-bi-directquery.md) 및 [Azure HDInsight에서 Power BI를 사용하여 빅 데이터 시각화](../hadoop/apache-hadoop-connect-hive-power-bi.md)를 참조하세요.|
|Visual Studio|[Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)을 참조하세요.|
|Visual Studio Code|[Apache Hive, LLAP 또는 pySpark용 Visual Studio Code 사용](../hdinsight-for-vscode.md)을 참조하세요.|
|Apache Ambari Hive 보기|[Azure HDInsight에서 Apache Hadoop과 Apache Hive View 사용](../hadoop/apache-hadoop-use-hive-ambari-view.md)을 참조하세요. HDInsight 4.0에서는 Hive 보기를 사용할 수 없습니다.|
|Apache Beeline|[Beeline를 사용하여 HDInsight에서 Apache Hadoop과 Apache Hive 사용](../hadoop/apache-hadoop-use-hive-beeline.md)을 참조하세요. 헤드 노드 또는 빈 에지 노드에서 Beeline을 사용할 수 있습니다. 빈 에지 노드에서 Beeline을 사용하는 것이 좋습니다. 빈 에지 노드를 사용하여 HDInsight 클러스터를 만드는 방법에 자세한 내용은 [HDInsight에서 빈 에지 노드 사용](../hdinsight-apps-use-edge-node.md)을 참조하세요.|
|Hive ODBC|[Microsoft Hive ODBC 드라이버를 사용하여 Apache Hadoop에 Excel 연결](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)을 참조하세요.|

JDBC(Java Database Connectivity) 연결 문자열을 찾으려면 다음을 수행합니다.

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.
1. URL을 복사하려면 클립보드 아이콘을 선택합니다.

   :::image type="content" source="./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png" alt-text="HDInsight Hadoop Interactive Query LLAP JDBC" border="true":::

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 Interactive Query 클러스터를 만드는 방법](../hdinsight-hadoop-provision-linux-clusters.md)에 대해 알아봅니다.
* [Azure HDInsight에서 Power BI를 사용하여 빅 데이터를 시각화](../hadoop/apache-hadoop-connect-hive-power-bi.md)하는 방법에 대해 알아봅니다.
* [Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리를 실행하는 방법](../interactive-query/hdinsight-connect-hive-zeppelin.md)을 알아봅니다.
