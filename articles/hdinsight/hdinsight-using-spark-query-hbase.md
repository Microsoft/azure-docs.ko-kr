---
title: Spark를 사용하여 HBase 데이터 읽기 및 쓰기 - Azure HDInsight
description: Spark HBase 커넥터를 사용하여 Spark 클러스터에서 HBase 클러스터로 데이터를 읽고 씁니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: e3f5cb726dddbdbfbd1b1f48c800ac681e7a174c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765680"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Apache Spark를 사용하여 Apache HBase 데이터 읽기 및 쓰기

Apache HBase는 일반적으로 낮은 수준의 API(scans, gets, puts) 또는 Apache Phoenix를 사용하는 SQL 구문을 사용하여 쿼리됩니다. 또한 Apache는 Apache Spark HBase 커넥터를 제공하며 이 커넥터는 HBase에서 저장한 데이터를 쿼리하고 수정하는 대신 편리하고 효율적인 대안입니다.

## <a name="prerequisites"></a>필수 조건

* 별도 두 HDInsight 클러스터, HBase 및 Spark 사용 하 여 적어도 Spark 2.1 (HDInsight 3.6)이 설치 되어 있습니다.
* Spark 클러스터는 대기 시간을 최소화하면서 HBase 클러스터와 직접 통신해야 하므로 동일한 가상 네트워크에 두 클러스터를 모두 배포하는 구성 권장 자세한 내용은 [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.
* SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
* 합니다 [URI 체계](hdinsight-hadoop-linux-information.md#URI-and-scheme) 클러스터 기본 저장소에 대 한 합니다. Wasb 것: / / Azure Blob storage, abfs: / / Azure Data Lake 저장소 Gen2 또는 adl: / / Azure Data Lake 저장소 Gen1에 대 한 합니다. URI wasbs 것에 대해 Blob Storage 또는 Data Lake 저장소 Gen2 전송 보안을 사용 하는 경우: / / 또는 abfss: / / 각각도 참조 하세요 [보안 전송](../storage/common/storage-require-secure-transfer.md)합니다.


## <a name="overall-process"></a>전체 프로세스

Spark 클러스터가 HDInsight 클러스터를 쿼리할 수 ​​있도록 하는 고급 프로세스는 다음과 같습니다.

1. HBase에서 일부 샘플 데이터를 준비합니다.
2. HBase 클러스터 구성 폴더(/etc/hbase/conf)에서 hbase-site.xml 파일을 얻습니다.
3. Spark 2 구성 폴더(/etc/spark2/conf)에 hbase-site.xml 사본을 배치합니다.
4. `packages` 옵션의 Maven 좌표를 기준으로 Spark HBase 커넥터를 참조하는 `spark-shell`을 실행합니다.
5. Spark에서 HBase로 스키마를 매핑하는 카탈로그를 정의합니다.
6. RDD 또는 데이터 프레임 API를 사용하여 HBase 데이터와 상호 작용합니다.

## <a name="prepare-sample-data-in-apache-hbase"></a>Apache HBase에서 샘플 데이터 준비

이 단계에서는 Spark를 사용하여 쿼리할 수 있는 간단한 단일 테이블을 Apache HBase에 만들어 채웁니다.

1. SSH를 사용하여 HBase 클러스터의 헤드 노드에 연결합니다. 자세한 내용은 [SSH를 사용하여 HDInsight에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.  대체 하 여 아래 명령을 편집 `HBASECLUSTER` HBase 클러스터의 이름으로 `sshuser` 와 ssh 사용자 계정 이름 및 명령을 입력 합니다.

    ```
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. HBase 셸을 시작 하려면 아래 명령을 입력 합니다.

        hbase shell

3. 만들려면 아래 명령을 입력 한 `Contacts` 열 패밀리가 있는 테이블 `Personal` 고 `Office`:

        create 'Contacts', 'Personal', 'Office'

4. 데이터의 몇 가지 샘플 행을 로드 하려면 아래 명령을 입력 합니다.

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

5. HBase 셸을 종료 하려면 아래 명령을 입력 합니다.

        exit 

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Spark 클러스터에 hbase-site.xml 복사
Spark 클러스터의 기본 저장소의 루트에 hbase-site.xml을 로컬 저장소에서 복사 합니다.  명령을 편집 하 여 아래 구성을 반영 하도록 합니다.  그런 다음 HBase 클러스터에 열린 SSH 세션에서 명령을 입력 합니다.

| 값 구문 | 새 값|
|---|---|
|[URI 체계](hdinsight-hadoop-linux-information.md#URI-and-scheme) | 저장소를 반영 하도록 수정 합니다.  아래 구문은 보안 전송이 활성화 된 blob 저장소입니다.|
|`SPARK_STORAGE_CONTAINER`|Spark 클러스터에 사용 되는 기본 저장소 컨테이너 이름으로 바꿉니다.|
|`SPARK_STORAGE_ACCOUNT`|Spark 클러스터에 사용 되는 기본 저장소 계정 이름으로 바꿉니다.|

```
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Spark 클러스터에 hbase-site.xml 배치

1. SSH를 사용하여 Spark 클러스터의 헤드 노드에 연결합니다.

2. 복사 하려면 아래 명령을 입력 `hbase-site.xml` Spark 클러스터의 기본 저장소 클러스터의 로컬 저장소에 있는 Spark 2 구성 폴더에서에서:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark HBase 커넥터를 참조하는 Spark 셸 실행

1. Spark 클러스터에 열린 SSH 세션에서 spark 셸을 시작 하려면 아래 명령을 입력 합니다.

    ```
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. 이 Spark 셸 인스턴스를 열린 상태로 유지하고 다음 단계를 계속합니다.

## <a name="define-a-catalog-and-query"></a>카탈로그 및 쿼리 정의

이 단계에서는 Apache Spark에서 Apache HBase로 스키마를 매핑하는 카탈로그 개체를 정의합니다.  

1. 열려 있는 Spark 셸에서에서 다음을 입력 `import` 문:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. 아래 명령을 Contacts 테이블에 대 한 카탈로그를 정의 하 여 HBase에서 만든를 입력 합니다.

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    코드를 다음을 수행합니다.  

     a. 이름이 `Contacts`인 HBase 테이블에 대한 카탈로그 스키마를 정의합니다.  
     b. rowkey를 `key`로 식별하고 Spark에서 사용된 열 이름을 HBase에서 사용되는 열 패밀리, 열 이름 및 열 유형으로 매핑합니다.  
     다. 또한 rowkey는 `rowkey`의 특정 열 패밀리 `cf`가 있는 명명된 열(`rowkey`)로 자세하게 정의되어야 합니다.  

3. 주위에 데이터 프레임을 제공 하는 메서드를 정의 하려면 아래 명령을 입력 하 여 `Contacts` hbase에서 테이블:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. 데이터 프레임의 인스턴스를 만듭니다.

    ```scala
    val df = withCatalog(catalog)
    ```  

5. 데이터 프레임을 쿼리합니다.

    ```scala
    df.show()
    ```

6. 두 개의 데이터 행이 표시되어야 합니다.

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Spark SQL을 사용하여 HBase 테이블을 쿼리할 수 있도록 임시 테이블을 등록합니다.

    ```scala
    df.createTempView("contacts")
    ```

8. `contacts` 테이블에 대해 SQL 쿼리를 실행합니다.

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. 다음과 같은 결과가 표시되어야 합니다.

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>새 데이터 삽입

1. 새 Contact 레코드를 삽입하려면 `ContactRecord` 클래스를 정의합니다.

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. `ContactRecord` 인스턴스를 만들어 배열에 넣습니다.

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. 새 데이터의 배열을 HBase에 저장합니다.

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. 결과를 검사합니다.

    ```scala  
    df.show()
    ```

5. 다음과 유사한 출력이 표시됩니다.

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>다음 단계

* [Apache Spark HBase 커넥터](https://github.com/hortonworks-spark/shc)
