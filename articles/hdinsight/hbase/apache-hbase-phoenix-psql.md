---
title: psql을 사용하여 Apache Phoenix로 대량 로드 - Azure HDInsight
description: psql 도구를 사용하여 Phoenix 테이블에 데이터를 대량으로 로드합니다.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: fe6705dc3dedd521357f924dd433c7eacf88ba84
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114174"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>psql을 사용하여 Apache Phoenix로 데이터 대량 로드

[Apache Phoenix](https://phoenix.apache.org/)는 [Apache HBase](../hbase/apache-hbase-overview.md)에서 구축되는 오픈 소스 대규모 병렬 관계형 데이터베이스입니다. Phoenix는 HBase보다는 SQL과 비슷한 쿼리를 제공합니다. Phoenix는 JDBC 드라이버를 사용하여 사용자가 개별적으로 및 대량으로 SQL 테이블, 인덱스, 뷰 및 시퀀스를 생성, 삭제 및 변경하고, 행을 Upsert할 수 있도록 합니다. Phoenix는 MapReduce를 사용하여 쿼리를 컴파일하는 대신, noSQL 네이티브 컴파일을 사용하여 HBase 위에 대기 시간이 짧은 애플리케이션을 만듭니다. Phoenix는 서버의 주소 공간에서 클라이언트 제공 코드를 실행하도록 지원하는 보조 프로세서를 추가하고, 데이터와 함께 있는 코드를 실행합니다. 따라서 클라이언트/서버 데이터 전송이 최소화됩니다.  HDInsight에서 Phoenix를 사용하여 데이터로 작업하려면 먼저 테이블을 만든 다음, 데이터를 로드합니다.

## <a name="bulk-loading-with-apache-phoenix"></a>Apache Phoenix를 사용하여 대량 로드

클라이언트 API를 사용하거나, TableOutputFormat을 통해 MapReduce 작업을 수행하거나, HBase 셸을 통해 데이터를 수동으로 입력하는 경우를 비롯한 다양한 방법으로 데이터를 HBase로 가져올 수 있습니다. Phoenix는 CSV 데이터를 Phoenix 테이블로 로드하는 2가지 방법을 제공합니다. 하나는 `psql`이라는 클라이언트 로드 도구를 사용하는 것이고, 다른 하나는 MapReduce 기반 대량 로드 도구를 사용하는 것입니다.

`psql` 도구는 단일 스레드 도구로, 메가바이트 또는 기가바이트 단위의 데이터를 로드하는 데 가장 적합합니다. 로드할 모든 CSV 파일의 확장명은 '.csv'여야 합니다.  '.sql' 파일 확장명을 사용하여 `psql` 명령줄에 SQL 스크립트 파일을 지정할 수도 있습니다.

MapReduce는 여러 스레드를 사용하므로, 일반적으로 프로덕션 시나리오에서 훨씬 많은 양의 데이터를 대량으로 로드하는 데 사용됩니다.

데이터 로드를 시작하기 전에, Phoenix가 사용되도록 설정되어 있는지와 쿼리 제한 시간 설정이 예상대로 지정되어 있는지 확인합니다.  HDInsight 클러스터 [Apache Ambari](https://ambari.apache.org/) 대시보드에 액세스하고, HBase를 선택한 후 구성 탭을 선택합니다.  아래로 스크롤하여 Apache Phoenix가 표시된 것처럼 `enabled`로 설정되어 있는지 확인합니다.

![Apache Phoenix HDInsight 클러스터 설정](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>`psql`을 사용하여 테이블 대량 로드

1. 새 테이블을 만든 다음, 파일 이름 `createCustomersTable.sql`을 사용해서 쿼리를 저장합니다.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. CSV 파일(표시된 예제 내용 참조)을 새로 만든 테이블에 로드하기 위해 `customers.csv` 이름으로 `/tmp/` 디렉터리에 복사합니다.  `hdfs` 명령을 사용하여 CSV 파일을 원하는 원본 위치에 복사합니다.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. 입력 데이터가 올바르게 로드되었는지 확인하는 SQL SELECT 쿼리를 만든 다음, 파일 이름 `listCustomers.sql`을 사용하여 쿼리를 저장합니다. 어떤 SQL 쿼리도 사용할 수 있습니다.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. *새* Hadoop 명령 창을 열어 데이터를 대량으로 로드합니다. 먼저 `cd` 명령을 사용하여 실행 디렉터리 위치로 변경한 후 `psql` 도구(Python `psql.py` 명령)를 사용합니다. 

    다음 예제에서는 위의 2단계와 같이 `hdfs`를 사용하여 저장소 계정의 `customers.csv` 파일을 로컬 임시 디렉터리에 복사했을 것으로 예상합니다.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > `ZookeeperQuorum` 이름을 확인하려면 속성 이름으로 `hbase.zookeeper.quorum`을 사용하여 `/etc/hbase/conf/hbase-site.xml` 파일에서 [Apache ZooKeeper](https://zookeeper.apache.org/) 쿼럼 문자열을 찾습니다.

5. `psql` 작업이 완료된 후에 명령 창에 메시지가 표시됩니다.

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce를 사용하여 테이블 대량 로드

더 높은 처리량의 부하가 클러스터에 분산되도록 하려면 MapReduce 로드 도구를 사용합니다. 이 로더는 먼저 모든 데이터를 HFiles로 변환하고 만든 HFiles를 HBase에 제공합니다.

1. Phoenix 클라이언트 jar에 `hadoop` 명령을 사용하여 CSV MapReduce 로더를 시작합니다.

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. 앞에 나온 1단계의 `CreateCustomersTable.sql`와 마찬가지로 SQL 문을 사용하여 새 테이블을 만듭니다.

3. 테이블의 스키마를 확인하려면 `!describe inputTable`을 실행합니다.

4. 입력 데이터에 대한 위치 경로를 확인합니다(예: 예제 `customers.csv` 파일). 입력 파일은 WASB/ADLS 저장소 계정에 있을 수 있습니다. 이 예제 시나리오에서 입력 파일은 `<storage account parent>/inputFolderBulkLoad` 디렉터리에 있습니다.

5. MapReduce 대량 로드 명령에 대한 실행 디렉터리로 변경합니다.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. 속성 이름 `hbase.zookeeper.quorum`을 사용하여 `/etc/hbase/conf/hbase-site.xml`에서 `ZookeeperQuorum` 값을 찾습니다.

7. 클래스 경로를 설정하고 `CsvBulkLoadTool` 도구 명령을 실행합니다.

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Azure Data Lake Storage에서 MapReduce를 사용하려면 Data Lake Storage 루트 디렉터리(`hbase-site.xml`의 `hbase.rootdir` 값)를 찾습니다. 다음 명령에서 Data Lake Storage 루트 디렉터리는 `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`입니다. 이 명령에서 다음과 같이 Data Lake Storage 입력 및 출력 폴더를 매개 변수로 지정합니다.

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>권장 사항

* 입력 및 출력 폴더에 동일한 스토리지 미디어(Azure Storage(WASB) 또는 Azure Data Lake Storage(ADL))를 사용합니다. 데이터를 Azure Storage에서 Data Lake Storage로 전송하려면 `distcp` 명령을 사용합니다.

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* 더 큰 크기의 작업자 노드를 사용합니다. MapReduce 대량 복사의 매핑 프로세스는 사용 가능한 비 DFS 공간을 채우는 대량의 임시 출력을 생성합니다. 많은 양의 데이터에 대한 대량 로드를 위해 더 큰 작업자 노드를 더 많이 사용합니다. 클러스터에 직접 할당하는 작업자 노드 수는 처리 속도를 영향을 줍니다.

* 입력 파일을 ~10GB 청크로 분할합니다. 대량 로드는 저장소 집약적 작업이므로, 입력 파일을 여러 개의 청크로 분할하면 성능이 더 좋아집니다.

* 지역 서버 핫스폿을 방지합니다. 행 키가 단조롭게 증가하는 경우 HBase 순차 쓰기로 인해 지역 서버 핫스폿이 발생할 수 있습니다. 행 키를 *솔트*하면 순차 쓰기가 줄어듭니다. Phoenix는 아래에 참조된 것처럼, 특정 테이블에 대해 솔트 바이트로 행 키를 투명하게 솔트하는 방법을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Apache Phoenix로 데이터 대량 로드](https://phoenix.apache.org/bulk_dataload.html)
* [HDInsight에서 Linux 기반 Apache HBase 클러스터와 함께 Apache Phoenix 사용](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [솔트된 테이블](https://phoenix.apache.org/salted.html)
* [Apache Phoenix 문법](https://phoenix.apache.org/language/index.html)
