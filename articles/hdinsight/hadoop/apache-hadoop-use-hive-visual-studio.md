---
title: Visual Studio용 Data Lake(Apache Hadoop) 도구로 Apache Hive 사용 - Azure HDInsight
description: Azure HDInsight의 Apache Hadoop에서 Data Lake Tools for Visual Studio를 사용하여 Apache Hive 쿼리를 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 3a2e81234702e1fcff0349a14a4bc2852d257ad6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095475"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Visual Studio용 Data Lake 도구를 사용하여 Apache Hive 쿼리 실행

Data Lake Tools for Visual Studio를 사용하여 Apache Hive를 쿼리하는 방법을 알아봅니다. Data Lake 도구를 사용하면 Azure HDInsight에서 Apache Hadoop에 대해 Hive 쿼리를 쉽게 만들고, 제출하고, 모니터링할 수 있습니다.

## <a id="prereq"></a>필수 조건

* Azure HDInsight(HDInsight의 Apache Hadoop) 클러스터

  > [!IMPORTANT]  
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* Visual Studio(다음 버전 중 하나)

    * Visual Studio 2013 Community/Professional/Premium/Ultimate 업데이트 4

    * Visual Studio 2015(모든 버전)

    * Visual Studio 2017(모든 버전)

* Visual Studio용 HDInsight 도구 또는 Visual Studio용 Azure Data Lake 도구 도구 설치 및 구성에 대한 내용은 [HDInsight용 Visual Studio Hadoop 도구 사용 시작](apache-hadoop-visual-studio-tools-get-started.md) 을 참조하세요.

## <a id="run"></a> Visual Studio를 사용하여 Apache Hive 쿼리 실행

1. **Visual Studio**를 열고 **새로 만들기** > **프로젝트** > **Azure Data Lake** > **HIVE** > **Hive 애플리케이션**을 차례로 선택합니다. 이 프로젝트에 대한 이름을 입력합니다.

2. 이 프로젝트에서 만든 **Script.hql** 파일을 열고 아래 HiveQL 문을 붙여 넣습니다.

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    이러한 문은 다음 작업을 수행합니다.

   * `DROP TABLE`: 테이블이 있는 경우 이 문에서 삭제합니다.

   * `CREATE EXTERNAL TABLE`: Hive에서 새 ‘외부’ 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 남아 있습니다.

     > [!NOTE]  
     > 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. MapReduce 작업 또는 Azure 서비스를 예로 들 수 있습니다.
     >
     > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

   * `ROW FORMAT`: 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

   * `STORED AS TEXTFILE LOCATION`: 데이터가 example/data 디렉터리에 텍스트로 저장되었음을 Hive에 알립니다.

   * `SELECT`: `t4` 열에 `[ERROR]` 값이 포함된 모든 행의 수를 선택합니다. 이 문에서는 이 값을 포함하는 행이 3개 있으므로 `3` 값이 반환되어야 합니다.

   * `INPUT__FILE__NAME LIKE '%.log'` - .log로 끝나는 파일의 데이터만 반환하도록 Hive에 지시합니다. 이 절은 데이터가 포함된 sample.log 파일로 검색을 제한합니다.

3. 도구 모음에서 쿼리에 사용할 **HDInsight 클러스터**를 선택합니다. **제출**을 선택하여 Hive 작업으로 문을 실행합니다.

   ![제출 표시줄](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. **Hive 작업 요약** 이 표시되고 실행 중인 작업 정보가 표시됩니다. **작업 상태**가 **완료**로 변경될 때까지 **새로 고침** 링크를 사용하여 작업 정보를 새로 고칩니다.

   ![완료된 작업을 표시하는 작업 요약](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. 이 작업의 출력을 보려면 **작업 출력** 링크를 사용합니다. 이 쿼리로 반환된 값으로 `[ERROR] 3`이 표시되어야 합니다.

6. 또한 프로젝트를 만들지 않고도 Hive 쿼리를 실행할 수 있습니다. **서버 탐색기**에서 **Azure** > **HDInsight**를 확장하고, HDInsight 서버를 마우스 오른쪽 단추로 클릭한 다음 **Hive 쿼리 작성**을 선택합니다.

7. 나타나는 **temp.hql** 문서에서 다음 HiveQL 문을 추가합니다.

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    이러한 문은 다음 작업을 수행합니다.

   * `CREATE TABLE IF NOT EXISTS`: 테이블이 아직 없는 경우 테이블을 만듭니다. `EXTERNAL` 키워드가 사용되지 않으므로 이 문으로 내부 테이블이 생성됩니다. 내부 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에 의해 관리됩니다.

     > [!NOTE]  
     > `EXTERNAL` 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

   * `STORED AS ORC`: 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. ORC는 Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.

   * `INSERT OVERWRITE ... SELECT`: `[ERROR]`가 포함된 `log4jLogs` 테이블에서 행을 선택하고 데이터를 `errorLogs` 테이블에 삽입합니다.

8. 도구 모음에서 **제출** 을 선택하여 작업을 실행합니다. **작업 상태** 를 사용하여 작업이 성공적으로 완료되었는지 결정합니다.

9. 작업에서 테이블이 만들어졌는지 확인하려면 **서버 탐색기**를 사용하여 **Azure** > **HDInsight** > HDInsight 클러스터 > **Hive 데이터베이스** > **기본값**을 확장합니다. **errorLogs** 및 **log4jLogs** 테이블이 나열됩니다.

## <a id="nextsteps"></a>다음 단계

여기에서 볼 수 있듯이 Visual Studio용 HDInsight 도구는 HDInsight에서 Hive 쿼리를 수행하는 쉬운 방법을 제공합니다.

HDInsight의 Hive에 대한 일반적인 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

Visual Studio용 HDInsight 도구에 대한 자세한 내용은 다음을 참조하세요.

* [Visual Studio용 HDInsight 도구 시작](apache-hadoop-visual-studio-tools-get-started.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
