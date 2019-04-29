---
title: Apache Ambari 보기를 사용하여 HDInsight(Apache Hadoop)에서 Hive 작업 - Azure
description: 웹 브라우저에서 Hive 뷰를 사용하여 Hive 쿼리를 제출하는 방법을 알아봅니다. Hive 뷰는 Linux 기반 HDInsight 클러스터와 함께 제공되는 Ambari 웹 UI의 일부입니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 55f8f453faf35d52c5c292e6b309194443980466
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095322"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>HDInsight에서 Apache Hadoop과 Apache Ambari Hive 보기 사용

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Apache Ambari Hive 보기를 사용하여 Hive 쿼리를 실행하는 방법을 알아봅니다. Hive 보기를 사용하면 웹 브라우저에서 Hive 쿼리를 작성, 최적화 및 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight에서 Hadoop 클러스터입니다. 참조 [Linux에서 HDInsight 시작](./apache-hadoop-linux-tutorial-get-started.md)합니다.
* 웹 브라우저

## <a name="run-a-hive-query"></a>HIVE 쿼리 실행

1. [Azure portal](https://portal.azure.com/), 클러스터를 선택 합니다.  참조 [클러스터 나열 및 표시](../hdinsight-administer-use-portal-linux.md#showClusters) 지침에 대 한 합니다. 클러스터는 새 포털 블레이드에서 열립니다.

2. **클러스터 대시보드**를 선택 **Ambari 보기**합니다. 인증하라는 메시지가 표시되면 클러스터를 만들 때 제공한 클러스터 로그인(기본값 `admin`) 계정 이름과 암호를 사용합니다.

3. 보기 목록에서 __Hive 보기__를 선택합니다.

    ![Hive 보기 선택](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    Hive 보기 페이지는 다음 이미지와 유사합니다.

    ![Hive 보기에 대한 쿼리 워크시트 이미지](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. __쿼리__ 탭에서 다음 HiveQL 문을 워크시트에 붙여넣습니다.

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    이러한 문은 다음 작업을 수행합니다.

   * `DROP TABLE`: 테이블이 이미 있는 경우 테이블과 데이터 파일을 삭제합니다.

   * `CREATE EXTERNAL TABLE`: Hive에서 새 “외부” 테이블을 만듭니다.
     외부 테이블은 테이블 정의만 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.

   * `ROW FORMAT`: 데이터의 형식을 지정하는 방법을 보여 줍니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

   * `STORED AS TEXTFILE LOCATION`: 데이터가 저장된 위치 및 텍스트로 저장되었음을 보여 줍니다.

   * `SELECT`: t4 열에 [ERROR] 값이 포함된 모든 행의 수를 선택합니다.

   > [!IMPORTANT]  
   > __데이터베이스__ 선택 영역을 __기본값__으로 둡니다. 이 문서의 예제에서는 HDInsight에 포함된 기본 데이터베이스를 사용합니다.

5. 쿼리를 시작 하려면 **Execute** 워크시트 아래. 단추가 주황색으로 바뀌고 텍스트가 **중지**로 변경됩니다.

6. 쿼리가 완료된 후에 **결과** 탭에 작업 결과가 표시됩니다. 다음 텍스트는 쿼리 결과입니다.

        loglevel       count
        [ERROR]        3

    사용할 수는 **로그** 작업에서 생성 된 로깅 정보를 보려면 탭 합니다.

   > [!TIP]  
   > 다운로드 또는 결과를 저장 합니다 **작업** 드롭다운 대화 상자의 합니다 **결과** 탭 합니다.

### <a name="visual-explain"></a>시각적 개체 설명

쿼리 계획의 시각화를 표시하려면 워크시트 아래에서 **시각적 개체 설명** 탭을 선택합니다.

쿼리의 **시각적 개체 설명** 보기는 복잡한 쿼리 흐름을 이해하는 데 도움이 됩니다.

### <a name="tez-ui"></a>Tez UI

쿼리에 대 한 Tez UI를 표시 하려면 선택 합니다 **Tez UI** 워크시트 아래에 있는 탭입니다.

> [!IMPORTANT]  
> Tez는 모든 쿼리를 해결하는 데 사용되지 않습니다. Tez를 사용하지 않고도 많은 쿼리를 확인할 수 있습니다. 

## <a name="view-job-history"></a>작업 기록 보기

__작업__ 탭에 Hive 쿼리의 기록이 표시됩니다.

![작업 기록 이미지](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>데이터베이스 테이블

__테이블__ 탭을 사용하여 Hive 데이터베이스 내의 테이블을 사용할 수 있습니다.

![테이블 탭의 이미지](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>저장된 쿼리

**쿼리** 탭에서 필요에 따라 쿼리를 저장할 수 있습니다. 쿼리를 저장한 후에 __저장된 쿼리__ 탭에서 다시 사용할 수 있습니다.

![저장된 쿼리 탭의 이미지](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]  
> 저장된 쿼리는 기본 클러스터 저장소에 저장됩니다. `/user/<username>/hive/scripts` 경로에서 저장된 쿼리를 찾을 수 있습니다. 이러한 쿼리는 일반 텍스트 `.hql` 파일로 저장됩니다.
>
> 클러스터를 삭제하지만 스토리지는 유지하는 경우 [Azure Portal](https://portal.azure.com)에서 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 또는 Data Lake Storage 탐색기와 같은 유틸리티를 사용하여 쿼리를 검색할 수 있습니다.

## <a name="user-defined-functions"></a>사용자 정의 함수

UDF(사용자 정의 함수)를 통해 Hive를 확장할 수 있습니다. UDF를 사용하여 HiveQL에서 쉽게 모델링할 수 있는 기능 또는 논리를 구현합니다.

Hive 보기 위쪽의 **UDF** 탭을 사용하여 UDF 집합을 선언하고 저장합니다. UDF는 **쿼리 편집기**를 통해 사용할 수 있습니다.

![UDF 탭의 이미지](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

UDF를 Hive 보기에 추가한 후에 **쿼리 편집기** 아래쪽에 **udf 삽입** 단추가 표시됩니다. 이 항목을 선택하면 Hive 뷰에서 정의된 UDF의 드롭다운 목록이 표시됩니다. UDF를 선택하면 쿼리에 HiveQL 문을 추가하여 UDF를 사용하도록 설정합니다.

예를 들어 다음 속성으로 UDF를 정의하는 경우:

* 리소스 이름: myudfs

* 리소스 경로: /myudfs.jar

* UDF 이름: myawesomeudf

* UDF 클래스 이름: com.myudfs.Awesome

**udf 삽입** 단추를 사용하면 해당 리소스에 정의된 각 UDF에 대한 또 다른 드롭다운 목록과 함께 **myudfs**라는 항목이 표시됩니다. 이 경우에 **myawesomeudf**입니다. 이 항목을 선택하면 쿼리의 시작 부분에 다음을 추가합니다.

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

그런 다음 쿼리에서 UDF를 사용할 수 있습니다. 예: `SELECT myawesomeudf(name) FROM people;`.

HDInsight에서 Hive를 통해 UDF를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hive 및 Apache Pig와 함께 Python 사용](python-udf-hdinsight.md)
* [HDInsight에 사용자 지정 Apache Hive UDF를 추가하는 방법](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Hive 설정

Tez(기본값)에서 MapReduce로 Hive에 대한 실행 엔진을 변경하는 등 다양한 Hive 설정을 변경할 수 있습니다.

## <a id="nextsteps"></a>다음 단계

HDInsight의 Hive에 대한 일반적인 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)
