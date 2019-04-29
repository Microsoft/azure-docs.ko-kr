---
title: Azure HDInsight에서 대화형 쿼리 사용
description: HDInsight에서 Interactive Query(Hive LLAP)를 사용하는 방법에 대해 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: c7cee3dfd3b091d75f4dadcaa62513fddf0c0e68
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126599"
---
# <a name="use-interactive-query-with-hdinsight"></a>HDInsight에서 대화형 쿼리 사용
대화형 쿼리(Apache Hive LLAP 또는 [짧은 대기 시간 분석 처리](https://cwiki.apache.org/confluence/display/Hive/LLAP)라고도 함)는 Azure HDInsight [클러스터 유형](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types)입니다. Interactive Query에서는 메모리 내 캐싱을 지원하여 Apache Hive 쿼리를 더 강화된 대화형 방식으로 더 빠르게 수행할 수 있습니다.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Interactive Query 클러스터는 Apache Hadoop 클러스터와 다릅니다. Hive 서비스만 포함합니다. 

> [!NOTE]  
> Interactive Query 클러스터의 Hive 서비스는 Apache Ambari Hive 보기, Beeline 및 Microsoft Hive ODBC(Open Database Connectivity) Driver를 통해서만 액세스할 수 있습니다. Hive 콘솔, Templeton, Azure 클래식 CLI 또는 Azure PowerShell을 통해서는 액세스할 수 없습니다. 

## <a name="create-an-interactive-query-cluster"></a>대화형 쿼리 클러스터 만들기
HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. 대화형 쿼리 클러스터 형식을 선택합니다.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Interactive Query에서 Apache Hive 쿼리 실행
Hive 쿼리를 실행하려면 다음 옵션이 있어야 합니다.

* Microsoft Power BI 사용

    [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Apache Hive 데이터 시각화](./apache-hadoop-connect-hive-power-bi-directquery.md) 참조 [Azure HDInsight에서 Power BI를 사용하여 빅 데이터 시각화](../hadoop/apache-hadoop-connect-hive-power-bi.md)를 참조하세요.
 
* Apache Zeppelin 사용

    참조 [Azure HDInsight에서 Apache Hive 쿼리 실행을 사용 하 여 Apache Zeppelin](../hdinsight-connect-hive-zeppelin.md)합니다.

* Visual Studio 사용

    [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)을 참조하세요.

* Visual Studio Code 사용

    [Apache Hive, LLAP 또는 pySpark용 Visual Studio Code 사용](../hdinsight-for-vscode.md)을 참조하세요.
* Apache Ambari Hive View를 사용하여 Apache Hive를 실행합니다.
  
    [Azure HDInsight에서 Apache Hadoop과 Apache Hive View 사용](../hadoop/apache-hadoop-use-hive-ambari-view.md)을 참조하세요.

* Beeline을 사용하여 Apache Hive를 실행합니다.
  
    [Beeline를 사용하여 HDInsight에서 Apache Hadoop과 Apache Hive 사용](../hadoop/apache-hadoop-use-hive-beeline.md)을 참조하세요.
  
    헤드 노드 또는 빈 에지 노드에서 Beeline을 사용할 수 있습니다. 빈 에지 노드에서 Beeline을 사용하는 것이 좋습니다. 빈 에지 노드를 사용하여 HDInsight 클러스터를 만드는 방법에 자세한 내용은 [HDInsight에서 빈 에지 노드 사용](../hdinsight-apps-use-edge-node.md)을 참조하세요.
* Hive ODBC를 사용하여 Apache Hive를 실행합니다.
  
    [Microsoft Hive ODBC 드라이버를 사용하여 Apache Hadoop에 Excel 연결](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)을 참조하세요.

JDBC(Java Database Connectivity) 연결 문자열을 찾으려면 다음을 수행합니다.

1. https://\<cluster name\>.AzureHDInsight.net URL을 사용하여 Apache Ambari에 로그인합니다.
2. 왼쪽 메뉴에서 **Hive**를 선택합니다.
3. URL을 복사하려면 클립보드 아이콘을 선택합니다.
   
   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 Interactive Query 클러스터를 만드는 방법](../hdinsight-hadoop-provision-linux-clusters.md)에 대해 알아봅니다.
* [Azure HDInsight에서 Power BI를 사용하여 빅 데이터를 시각화](../hadoop/apache-hadoop-connect-hive-power-bi.md)하는 방법에 대해 알아봅니다.
* 설명 하는 방법 [Apache Zeppelin을 사용 하 여 Azure HDInsight에서 Apache Hive 쿼리를 실행 하려면](../hdinsight-connect-hive-zeppelin.md).
* [Data Lake Tools for Visual Studio를 사용하여 Apache Hive 쿼리를 실행하는 방법](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)을 알아봅니다.
* [HDInsight Tools for Visual Studio Code를 사용](../hdinsight-for-vscode.md)하는 방법에 대해 알아봅니다.
* [HDInsight에서 Apache Hadoop과 Apache Hive View를 사용하는 방법](../hadoop/apache-hadoop-use-hive-ambari-view.md)을 알아봅니다.
* [Beeline을 사용하여 HDInsight에서 Apache Hive 쿼리를 제출하는 방법](../hadoop/apache-hadoop-use-hive-beeline.md)을 알아봅니다.
* [Microsoft Hive ODBC 드라이버를 사용하여 Apache Hadoop에 Excel을 연결하는 방법](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)을 알아봅니다.

