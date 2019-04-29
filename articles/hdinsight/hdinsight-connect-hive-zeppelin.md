---
title: Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행
description: Apache Zeppelin을 사용하여 Apache Hive 쿼리를 실행하는 방법을 알아봅니다.
keywords: hdinsight,hadoop,hive,대화형 쿼리,LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 417e9f8ae78889374983bf77900ee00fa7fc6338
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098763"
---
# <a name="use-apache-zeppelin-to-run-apache-hive-queries-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행 

HDInsight 대화형 쿼리 클러스터에는 대화형 Hive 쿼리를 실행하는 데 사용할 수 있는 [Apache Zeppelin](https://zeppelin.apache.org/) Notebook이 포함되어 있습니다. 이 문서에서는 Azure HDInsight에서 Apache Zeppelin을 사용하여 [Apache Hive](https://hive.apache.org/) 쿼리를 실행하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* **HDInsight 대화형 쿼리 클러스터**. HDInsight 클러스터를 만들려면 [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요.  대화형 쿼리 형식을 선택해야 합니다. 

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin 노트 만들기

1. 다음 URL로 이동합니다.

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다.

2. Hadoop 사용자 이름 및 암호를 입력합니다. Zeppelin 페이지에서 새 노트를 만들거나 기존 노트를 열 수 있습니다. HiveSample에는 몇 가지 샘플 Hive 쿼리가 포함되어 있습니다.  

    ![HDInsight 대화형 쿼리 zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. **새 노트 만들기**를 클릭합니다.
4. 다음 값을 입력하거나 선택합니다.

    - 노트 이름: 노트의 이름을 입력합니다.
    - 기본 인터프리터: **JDBC**를 선택합니다.

5. **노트 만들기**를 클릭합니다.
6. 다음 Hive 쿼리를 실행합니다.

        %jdbc(hive)
        show tables

    ![HDInsight 대화형 쿼리 zeppelin 쿼리 실행](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    첫 번째 줄의 **%jdbc(hive)** 문은 노트북이 Hive JDBC 인터프리터를 사용한다는 것을 나타냅니다.

    쿼리에서 *hivesampletable*이라는 하나의 Hive 테이블을 반환합니다.

    다음 두 개의 추가 쿼리는 hivesampletable에 대해 실행할 수 있는 Hive 쿼리입니다. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    기존의 Hive에 비해 쿼리 결과를 반환하는 속도가 훨씬 빠릅니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 [Microsoft Power BI](https://powerbi.microsoft.com/)를 사용하여 HDInsight에서 데이터를 시각화하는 방법을 알아보았습니다.  자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Apache Hive 데이터 시각화](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Apache Hive 데이터 시각화](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결](hadoop/apache-hadoop-connect-excel-power-query.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Azure HDInsight Tool for Visual Studio Code 사용](hdinsight-for-vscode.md)
* [HDInsight에 데이터 업로드](./hdinsight-upload-data.md)
