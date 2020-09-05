---
title: Spark를 사용하여 HBase 데이터 읽기 및 쓰기 - Azure HDInsight
description: Spark HBase 커넥터를 사용하여 Spark 클러스터에서 HBase 클러스터로 데이터를 읽고 씁니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/12/2020
ms.openlocfilehash: 9454cb83d535d97a3dd95cd9f5d0636769797d08
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166946"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Apache Spark를 사용하여 Apache HBase 데이터 읽기 및 쓰기

Apache HBase는 일반적으로 낮은 수준의 API(scans, gets, puts) 또는 Apache Phoenix를 사용하는 SQL 구문을 사용하여 쿼리됩니다. 또한 Apache는 Apache Spark HBase 커넥터를 제공 합니다. 커넥터는 HBase에 저장 된 데이터를 쿼리 및 수정 하는 쉽고 효율적인 방법입니다.

## <a name="prerequisites"></a>사전 요구 사항

* 동일한 [가상 네트워크](./hdinsight-plan-virtual-network-deployment.md)에 배포 된 두 개의 별도 HDInsight 클러스터. HBase 하나 및 spark 2.1 (HDInsight 3.6) 이상이 설치 된 Spark 하나 자세한 내용은 [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.

* 클러스터 기본 스토리지에 대한 URI 체계입니다. 이 체계는 Azure Data Lake Storage Gen1에 대 한 Azure Data Lake Storage Gen2 또는 adl://에 대 한 Azure Blob Storage wasb://됩니다 `abfs://` . Blob Storage에 대해 보안 전송이 사용 되는 경우 URI는 `wasbs://` 입니다.  [보안 전송](../storage/common/storage-require-secure-transfer.md)도 참조하세요.

## <a name="overall-process"></a>전체 프로세스

Spark 클러스터를 사용 하 여 HBase 클러스터를 쿼리할 수 있는 높은 수준의 프로세스는 다음과 같습니다.

1. HBase에서 일부 샘플 데이터를 준비합니다.
2. HBase 클러스터 구성 폴더 (/etc/hbase/conf)에서 hbase-site.xml 파일을 가져오고 Spark 2 구성 폴더 (/etc/spark2/conf)에 hbase-site.xml 복사본을 저장 합니다. (선택 사항: HDInsight 팀에서 제공 하는 스크립트를 사용 하 여이 프로세스 자동화)
4. `packages` 옵션의 Maven 좌표를 기준으로 Spark HBase 커넥터를 참조하는 `spark-shell`을 실행합니다.
5. Spark에서 HBase로 스키마를 매핑하는 카탈로그를 정의합니다.
6. RDD 또는 데이터 프레임 API를 사용하여 HBase 데이터와 상호 작용합니다.

## <a name="prepare-sample-data-in-apache-hbase"></a>Apache HBase에서 샘플 데이터 준비

이 단계에서는 Spark를 사용 하 여 쿼리할 수 있는 Apache HBase의 테이블을 만들고 채웁니다.

1. 명령을 사용 `ssh` 하 여 HBase 클러스터에 연결 합니다. HBase 클러스터의 이름으로 대체 하 여 아래 명령을 편집 `HBASECLUSTER` 하 고 다음 명령을 입력 합니다.

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. 명령을 사용 `hbase shell` 하 여 HBase 대화형 셸을 시작 합니다. SSH 연결에서 다음 명령을 입력합니다.

    ```bash
    hbase shell
    ```

3. 명령을 사용 `create` 하 여 두 열 패밀리가 있는 HBase 테이블을 만듭니다. 다음 명령을 입력합니다.

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. 명령을 사용 `put` 하 여 특정 테이블의 지정 된 행에서 지정 된 열에 값을 삽입할 수 있습니다. 다음 명령을 입력합니다.

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. 명령을 사용 `exit` 하 여 HBase 대화형 셸을 중지 합니다. 다음 명령을 입력합니다.

    ```hbase
    exit
    ```
    
## <a name="run-scripts-to-set-up-connection-between-clusters"></a>스크립트를 실행 하 여 클러스터 간 연결 설정

클러스터 간의 통신을 설정 하려면 아래 단계를 수행 하 여 클러스터에서 스크립트 두 개를 실행 합니다. 이러한 스크립트는 아래의 ' 통신 수동 설정 ' 섹션에 설명 된 파일 복사 프로세스를 자동화 합니다. 

* HBase 클러스터에서 실행 하는 스크립트는 `hbase-site.xml` Spark 클러스터에 연결 된 기본 저장소에 HBASE IP 매핑 정보를 업로드 합니다. 
* Spark 클러스터에서 실행 하는 스크립트는 두 개의 cron 작업을 설정 하 여 두 개의 도우미 스크립트를 정기적으로 실행 합니다.  
    1.  HBase cron 작업 – `hbase-site.xml` Spark 기본 저장소 계정에서 로컬 노드로 새 파일 및 HBASE IP 매핑을 다운로드 합니다.
    2.  Spark cron job – Spark 확장이 발생 했는지와 클러스터가 안전한 지 확인 합니다. 이 경우 `/etc/hosts` 로컬에 저장 된 HBASE IP 매핑을 포함 하도록 편집 합니다.

__참고__: 계속 하기 전에 Spark 클러스터의 저장소 계정을 HBase 클러스터에 보조 저장소 계정으로 추가 했는지 확인 합니다. 아래에 표시 된 대로 스크립트를 순서 대로 확인 합니다.


1. HBase 클러스터에서 [스크립트 작업](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) 을 사용 하 여 다음 고려 사항에 따라 변경 내용을 적용 합니다. 


    |속성 | 값 |
    |---|---|
    |Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-hbase.sh`|
    |노드 유형|Azure 지역|
    |매개 변수|`-s SECONDARYS_STORAGE_URL`|
    |지속됨|예|

    * `SECONDARYS_STORAGE_URL`Spark 쪽 기본 저장소의 url입니다. 매개 변수 예:`-s wasb://sparkcon-2020-08-03t18-17-37-853z@sparkconhdistorage.blob.core.windows.net`


2.  다음 고려 사항에 따라 Spark 클러스터에서 스크립트 작업을 사용 하 여 변경 내용을 적용 합니다.

    |속성 | 값 |
    |---|---|
    |Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-spark.sh`|
    |노드 유형|헤드, 작업자, 사육 사|
    |매개 변수|`-s "SPARK-CRON-SCHEDULE"`(선택 사항) `-h "HBASE-CRON-SCHEDULE"` 필드|
    |지속됨|예|


    * 이 클러스터가 업데이트를 자동으로 확인 하도록 할 빈도를 지정할 수 있습니다. 기본값:-s "*/1 * * * *"-h 0 (이 예제에서는 Spark cron는 1 분 마다 실행 되 고 HBase cron은 실행 되지 않음)
    * HBase cron는 기본적으로 설정 되어 있지 않으므로 HBase 클러스터에 대 한 크기 조정을 수행할 때이 스크립트를 다시 실행 해야 합니다. HBase 클러스터가 자주 크기를 조정 하는 경우 HBase cron 작업을 자동으로 설정 하도록 선택할 수 있습니다. 예: `-h "*/30 * * * *"` 는 30 분 마다 검사를 수행 하도록 스크립트를 구성 합니다. 이렇게 하면 정기적으로 HBase cron 일정을 실행 하 여 로컬 노드에 대 한 일반 저장소 계정에서 새 HBase 정보를 자동으로 다운로드할 수 있습니다.
    
    

## <a name="set-up-communication-manually-optional-if-provided-script-in-above-step-fails"></a>수동으로 통신 설정 (선택 사항, 위의 단계에서 제공 된 스크립트가 실패 한 경우)

__참고:__ 이러한 단계는 클러스터 중 하나가 크기 조정 작업을 수행할 때마다 수행 해야 합니다.

1. hbase-site.xml를 로컬 저장소에서 Spark 클러스터의 기본 저장소의 루트에 복사 합니다.  구성을 반영 하도록 아래 명령을 편집 합니다.  그런 다음 열린 SSH 세션에서 HBase 클러스터로 명령을 입력 합니다.

    | 구문 값 | 새 값|
    |---|---|
    |[URI 체계](hdinsight-hadoop-linux-information.md#URI-and-scheme) | 저장소를 반영 하도록를 수정 합니다.  아래 구문은 보안 전송을 사용 하도록 설정 된 blob 저장소에 대 한 것입니다.|
    |`SPARK_STORAGE_CONTAINER`|을 Spark 클러스터에 사용 되는 기본 저장소 컨테이너 이름으로 바꿉니다.|
    |`SPARK_STORAGE_ACCOUNT`|을 Spark 클러스터에 사용 되는 기본 저장소 계정 이름으로 바꿉니다.|

    ```bash
    hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
    ```

2. 그런 다음 HBase 클러스터에 대 한 ssh 연결을 종료 합니다.

    ```bash
    exit
    ```


3. SSH를 사용하여 Spark 클러스터의 헤드 노드에 연결합니다. 을 `SPARKCLUSTER` Spark 클러스터의 이름으로 바꾸고 아래 명령을 편집 하 고 명령을 입력 합니다.

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

4. 아래 명령을 입력 하 여 `hbase-site.xml` spark 클러스터의 기본 저장소에서 클러스터의 로컬 저장소에 있는 spark 2 구성 폴더로 복사 합니다.

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark HBase 커넥터를 참조하는 Spark 셸 실행

이전 단계를 완료 한 후에는 적절 한 버전의 Spark HBase 커넥터를 참조 하는 Spark 셸을 실행할 수 있습니다. 클러스터 시나리오에 대 한 가장 최근의 적절 한 Spark HBase 커넥터 코어 버전을 찾으려면 [Shc Core 리포지토리](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/)를 참조 하세요.

예를 들어 다음 표에는 HDInsight 팀에서 현재 사용 하는 두 가지 버전과 해당 명령이 나와 있습니다. HBase 및 Spark 버전이 표에 표시 된 것과 같은 경우 클러스터에 대해 동일한 버전을 사용할 수 있습니다. 


1. Spark 클러스터에 대 한 공개 SSH 세션에서 다음 명령을 입력 하 여 Spark 셸을 시작 합니다.

    |Spark 버전| HDI HBase 버전  | SHC 버전    |  명령  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3.6 (HBase 1.1) | 1.1.0.3.1.2.2-1    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2.4    | HDI 4.0 (HBase 2.0) | 1.1.1-2.1-s_2 11  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. 이 Spark 셸 인스턴스를 열어 두고 [카탈로그와 쿼리](#define-a-catalog-and-query)를 계속 정의 합니다. SHC Core 리포지토리의 버전에 해당 하는 jar를 찾지 못하면 계속 읽습니다. 

Jar [GitHub 분기에서 직접](https://github.com/hortonworks-spark/shc) 만들 수 있습니다. 예를 들어 Spark 2.3 및 HBase 1.1를 사용 하 여를 실행 하는 경우 다음 단계를 완료 합니다.

1. 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. 분기-2.3로 이동 합니다.

    ```bash
    git checkout branch-2.3
    ```

3. 분기에서 빌드 (jar 파일 생성):

    ```bash
    mvn clean package -DskipTests
    ```
    
3. 다음 명령을 실행 합니다 (빌드한 .jar 파일에 해당 하는 jar 이름을 변경 해야 합니다.).

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. 이 Spark 셸 인스턴스를 열어 두고 다음 섹션으로 계속 진행 합니다. 



## <a name="define-a-catalog-and-query"></a>카탈로그 및 쿼리 정의

이 단계에서는 Apache Spark에서 Apache HBase로 스키마를 매핑하는 카탈로그 개체를 정의합니다.  

1. 열려 있는 Spark 셸에서 다음 문을 입력 합니다 `import` .

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. 다음 명령을 입력 하 여 HBase에서 만든 Contacts 테이블에 대 한 카탈로그를 정의 합니다.

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

    코드:  

    1. 라는 HBase 테이블에 대 한 카탈로그 스키마를 정의 `Contacts` 합니다.  
    1. Rowkey를으로 식별 `key` 하 고 Spark에 사용 되는 열 이름을 HBase에서 사용 되는 열 패밀리, 열 이름 및 열 형식에 매핑합니다.  
    1. `rowkey`의 특정 열 패밀리가 있는 명명 된 열 ()로 rowkey를 자세히 정의 합니다 `cf` `rowkey` .  

1. 다음 명령을 입력 하 여 HBase의 테이블 주위에 데이터 프레임를 제공 하는 메서드를 정의 합니다 `Contacts` .

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. 데이터 프레임의 인스턴스를 만듭니다.

    ```scala
    val df = withCatalog(catalog)
    ```  

1. 데이터 프레임을 쿼리합니다.

    ```scala
    df.show()
    ```

    두 개의 데이터 행이 표시되어야 합니다.

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Spark SQL을 사용하여 HBase 테이블을 쿼리할 수 있도록 임시 테이블을 등록합니다.

    ```scala
    df.createTempView("contacts")
    ```

1. `contacts` 테이블에 대해 SQL 쿼리를 실행합니다.

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    다음과 같은 결과가 표시되어야 합니다.

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

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

1. `ContactRecord` 인스턴스를 만들어 배열에 넣습니다.

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. 새 데이터의 배열을 HBase에 저장합니다.

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. 결과를 검사합니다.

    ```scala  
    df.show()
    ```

    다음과 유사한 출력이 표시됩니다.

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. 다음 명령을 입력 하 여 spark 셸을 닫습니다.

    ```scala
    :q
    ```

## <a name="next-steps"></a>다음 단계

* [Apache Spark HBase 커넥터](https://github.com/hortonworks-spark/shc)
