---
title: Hive 웨어하우스 커넥터를 사용 하 여 Apache Hive 및 Apache Spark 통합
description: Azure HDInsight에서 Hive Warehouse 커넥터를 사용 하 여 Apache Spark 및 Apache Hive를 통합 하는 방법에 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/18/2019
ms.openlocfilehash: b450fe763104adbbd08e4b5f362bd51ffbf82c81
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126548"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Hive 웨어하우스 커넥터를 사용 하 여 Apache Hive 및 Apache Spark 통합

Apache Hive 웨어하우스 커넥터 HWC ()는 Apache Spark 및 Apache Hive를 사용 하 여 Spark 데이터 프레임 및 Hive 테이블 간 데이터 이동도 Spark 스트리밍 Hive 테이블로 데이터를 보내는 등의 작업을 지원 하 여 보다 쉽게 작업할 수 있도록 라이브러리입니다. Spark 및 Hive 간의 브리지를 처럼 하는 hive 웨어하우스 커넥터 작동 합니다. Scala, Java 및 Python 개발을 위한 지원합니다.

Hive Warehouse 커넥터를 사용 하면 강력한 빅 데이터 응용 프로그램을 빌드하는 Hive 및 Spark의 고유한 기능을 활용할 수 있습니다. Apache Hive는 원자성, 일관성, 격리 및 내구성 (ACID)는 데이터베이스 트랜잭션에 대 한 지원을 제공 합니다. ACID에 대 한 자세한 정보 및 Hive의 트랜잭션이 [Hive 트랜잭션](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)합니다. 또한 hive Apache Ranger 및 낮은 대기 시간 분석 처리 Apache Spark에서 사용할 수 없는 통한 자세한 보안 제어를 제공합니다.

Apache Spark, Apache Hive에서 사용할 수 없는 스트리밍 기능을 제공 하는 구조적 스트리밍 API에 있습니다. Apache Spark 및 Apache Hive 상호 운용성을 어렵게 만들 수 있는 별도 metastore가 Hortonworks Data Platform (HDP) 3.0 사용을 시작 합니다. Hive 웨어하우스 커넥터를 사용 하면 Spark 및 Hive를 함께 사용 하기 쉽습니다. HWC 라이브러리 LLAP 디먼에서 Spark 실행 기를 병렬로 더 효율적이 고 확장 가능한 표준 JDBC 연결할 Spark에서 Hive 사용 하 여 보다 쉽게 데이터를 로드 합니다.

![아키텍처](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive 웨어하우스 커넥터에서 지 원하는 작업은 다음과 같습니다.

* 테이블을 설명 하는
* ORC 형식의 데이터에 대 한 테이블 만들기
* Hive 데이터를 선택 하 고 데이터 프레임을 검색 합니다.
* 일괄 처리의 데이터 프레임을 Hive에 작성
* Hive update 문을 실행합니다.
* Hive에서 테이블 데이터를 읽는, spark, 변환 및 새 Hive 테이블에 작성
* 데이터 프레임 또는 Spark stream HiveStreaming를 사용 하 여 Hive에 쓰기

## <a name="hive-warehouse-connector-setup"></a>Hive 웨어하우스 커넥터 설치

Azure HDInsight에서 Spark 및 대화형 쿼리 클러스터 간에 Hive Warehouse 커넥터를 설정 하려면 다음이 단계를 수행 합니다.

1. Azure portal을 사용 하 여 저장소 계정 및 사용자 지정 Azure virtual network를 사용 하 여 HDInsight Spark 4.0 클러스터를 만듭니다. Azure 가상 네트워크에서 클러스터 만들기에 대 한 내용은 참조 하세요 [기존 가상 네트워크에 HDInsight 추가](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet)합니다.
1. Spark 클러스터와 동일한 저장소 계정 및 Azure 가상 네트워크를 사용 하 여 Azure portal을 사용 하 여 HDInsight 대화형 쿼리 (LLAP) 4.0 클러스터를 만듭니다.
1. 내용을 복사 합니다 `/etc/hosts` 대화형 쿼리 클러스터의 headnode0에 파일은 `/etc/hosts` Spark 클러스터의 headnode0에 파일입니다. 이 단계는 Spark 클러스터에서 대화형 쿼리 클러스터 노드의 IP 주소를 확인할 수 있습니다. 사용 하 여 업데이트 된 파일의 내용을 보려면 `cat /etc/hosts`합니다. 출력은 아래 스크린샷에 표시 된 것은 어떤 같은 비슷해야 합니다.

    ![호스트 파일 보기](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. 다음 단계를 수행 하 여 Spark 클러스터 설정을 구성 합니다. 
    1. Azure portal로 HDInsight 클러스터를 선택한 클러스터 이름을 클릭 합니다.
    1. 오른쪽에서 아래 **클러스터 대시보드**를 선택 **Ambari 홈**합니다.
    1. Ambari 웹 UI에서 클릭 **SPARK2** > **CONFIGS** > **Custom spark2-defaults**합니다.

        ![Spark2 Ambari 구성](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. 설정 `spark.hadoop.hive.llap.daemon.service.hosts` 속성과 동일한 값으로 **LLAP 응용 프로그램 이름** 아래 **hive 대화형 env 고급**합니다. 예를 들어 `@llap0`

    1. 설정 `spark.sql.hive.hiveserver2.jdbc.url` JDBC 연결 문자열에 연결 하는 Hiveserver2에 대화형 쿼리 클러스터에 있습니다. 클러스터에 대 한 연결 문자열은 URI 아래와 같습니다. `CLUSTERNAME` Spark 클러스터의 이름이 고 `user` 및 `password` 매개 변수는 클러스터에 대 한 올바른 값으로 설정 됩니다.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > JDBC URL Hiveserver2에 연결 하기 위한 자격 증명이 있어야 합니다. 사용자 이름과 암호를 포함 합니다.

    1. 설정 `spark.datasource.hive.warehouse.load.staging.dir` 적합 한 HDFS 호환 스테이징 디렉터리에 있습니다. 서로 다른 두 클러스터를 만든 경우 HiveServer2에 액세스할 수 있도록 준비 디렉터리가 LLAP 클러스터의 저장소 계정 준비 디렉터리의 폴더로 있어야 합니다. 예를 들어 `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` 여기서 `STORAGE_ACCOUNT_NAME` 클러스터에서 사용 되는 저장소 계정 이름 및 `STORAGE_CONTAINER_NAME` 저장소 컨테이너의 이름입니다.

    1. 설정 `spark.datasource.hive.warehouse.metastoreUri` metastore 대화형 쿼리 클러스터의 URI 값입니다. 검색할 LLAP 클러스터에는 metastoreUri를 찾으려고 합니다 **hive.metastore.uris** 속성에 LLAP에 대 한 Ambari ui에서 클러스터 **Hive** > **고급**  >  **일반**합니다. 값은 다음 URI와 같이 표시 됩니다.

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. 설정할 `spark.security.credentials.hiveserver2.enabled` 에 `false` YARN 클라이언트에 대 한 모드를 배포 합니다.
    1. 설정 `spark.hadoop.hive.zookeeper.quorum` LLAP 클러스터의 Zookeeper 쿼럼. 검색할 LLAP 클러스터에 대 한 Zookeeper 쿼럼을 찾으려고 합니다 **hive.zookeeper.quorum** 속성에 LLAP에 대 한 Ambari ui에서 클러스터 **Hive** > **고급**  >  **고급 hive 사이트**합니다. 값은 다음 문자열과 비슷합니다.

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Hive 웨어하우스 커넥터의 구성을 테스트 하려면 섹션의 단계를 따릅니다 [연결 하 고 실행 중인 쿼리](#connecting-and-running-queries)합니다.

## <a name="using-the-hive-warehouse-connector"></a>Hive 웨어하우스 커넥터를 사용 하 여

### <a name="connecting-and-running-queries"></a>연결 및 쿼리를 실행 합니다.

대화형 쿼리 클러스터에 연결 및 Hive Warehouse 커넥터를 사용 하 여 쿼리를 실행 하는 몇 가지 방법이 선택할 수 있습니다. 지원 되는 방법에는 다음 도구가 포함:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

이 문서에서 제공 하는 모든 예제는 spark 셸을 통해 실행 됩니다.

Spark 셸 세션을 시작 하려면 다음 단계를 수행 합니다.

1. 클러스터의 헤드 노드로 SSH 합니다. SSH 사용 하 여 클러스터에 연결 하는 방법에 대 한 자세한 내용은 참조 하세요. [SSH를 사용 하 여 HDInsight (Apache Hadoop)에 연결](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)합니다.
1. 입력 하 여 올바른 디렉터리로 변경 `cd /usr/hdp/current/hive_warehouse_connector` spark 셸 명령에 매개 변수로 사용 되는 모든 jar 파일의 전체 경로 제공 합니다.
1. Spark 셸을 시작 하려면 다음 명령을 입력 합니다.

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. 환영 메시지가 표시 됩니다 및 `scala>` 프롬프트 명령을 입력할 수 있습니다.

1. Spark-shell을 시작한 후 다음 명령을 사용 하 여 Hive 웨어하우스 커넥터 인스턴스를 시작할 수 있습니다.

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>연결 하 고 엔터프라이즈 보안 패키지 (ESP) 클러스터에서 쿼리를 실행 합니다.

엔터프라이즈 보안 패키지 (ESP) Active Directory 기반 인증, 다중 사용자 지원 및 Azure HDInsight에서 Apache Hadoop 클러스터에 대 한 역할 기반 액세스 제어와 같은 엔터프라이즈급 기능을 제공합니다. ESP에 대 한 자세한 내용은 참조 하세요. [엔터프라이즈 보안 패키지를 사용 하 여 Apache Hadoop 보안 소개](../domain-joined/apache-domain-joined-introduction.md)합니다.

1. 1과 2에서 초기 단계에 따라 [연결 하 고 실행 중인 쿼리](#connecting-and-running-queries)합니다.
1. 형식 `kinit` 도메인 사용자를 사용 하 여 로그인 합니다.
1. 시작으로 spark-shell 전체 목록은 아래와 같이 구성 매개 변수입니다. 모두 대문자로 꺾쇠 괄호 사이의 값을 모두 클러스터에 따라 지정 되어야 합니다. 아래 매개 변수 중 하나에 대 한 입력으로 사용할 값을 확인 해야 할 경우에 섹션을 참조 하세요 [Hive 웨어하우스 커넥터 설치](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Hive 쿼리를 Spark 데이터 프레임 만들기

HWC 라이브러리를 사용 하는 모든 쿼리의 결과 데이터 프레임으로 반환 됩니다. 다음 예제에서는 기본 쿼리를 만드는 방법을 보여 줍니다.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

쿼리 결과가 MLIB 등 SparkSQL Spark 라이브러리와 함께 사용할 수 있는 Spark DataFrames 됩니다.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark Dataframe Hive 테이블에 작성

Spark는 Hive의 관리 되는 ACID 테이블에 쓸 기본적으로 지원 하지 않습니다. 하지만 HWC를 사용 작성할 수 있습니다 Hive 테이블에 모든 데이터 프레임으로. 다음 예제에는 현재이 기능을 확인할 수 있습니다.

1. 라는 테이블을 만드는 `sampletable_colorado` 하 고 다음 명령을 사용 하 여 해당 열을 지정 합니다.

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. 테이블을 필터링 `hivesampletable` 여기서 열 `state` equals `Colorado`합니다. Hive 테이블의이 쿼리를 Spark 데이터 프레임으로 반환 됩니다. 데이터 프레임은 Hive 테이블에 저장 한 다음 `sampletable_colorado` 를 사용 하는 `write` 함수입니다.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

아래 스크린샷에 결과 테이블을 볼 수 있습니다.

![결과 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>구조적된 스트리밍 쓰기

Hive Warehouse 커넥터를 사용 하 여 Spark Hive 테이블로 데이터를 쓸 스트리밍를 사용할 수 있습니다.

Hive 테이블에 대 한 9999 localhost 포트에서 Spark 스트림에서 데이터를 수집 하는 Hive 웨어하우스 커넥터 예제를 만들려면 다음 단계를 수행 합니다.

1. Spark 클러스터에서 터미널을 엽니다.
1. 다음 명령 사용 하 여 spark stream을 시작 합니다.

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. 다음 단계를 수행 하 여 만든 Spark stream에 대 한 데이터를 생성 합니다.
    1. 다른 동일한 Spark 클러스터에서 터미널을 엽니다.
    1. 명령 프롬프트에 `nc -lk 9999`을 입력합니다. 이 명령은 지정된 된 포트를 명령줄에서 데이터를 보내도록 netcat 유틸리티를 사용 합니다.
    1. 캐리지 리턴 뒤에 Spark 스트림 수집을 선택 하는 단어를 입력 합니다.
        ![입력된 데이터를 spark 스트림으로](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. 스트리밍 데이터를 보관 하 여 새 Hive 테이블을 만듭니다. Spark 셸에서 다음 명령을 입력 합니다.

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 다음 명령을 사용 하 여 새로 만든된 테이블에 스트리밍 데이터를 작성 합니다.

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > 합니다 `metastoreUri` 고 `database` Apache Spark의 알려진된 문제로 인해 옵션 수동 설정 현재 해야 합니다. 이 문제에 대 한 자세한 내용은 참조 하세요. [SPARK 25460](https://issues.apache.org/jira/browse/SPARK-25460)합니다.

1. 다음 명령 사용 하 여 테이블에 삽입 된 데이터를 볼 수 있습니다.

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>ESP Spark 클러스터에서 데이터를 보호합니다.

1. 테이블 만들기 `demo` 다음 명령을 입력 하 여 몇 가지 샘플 데이터를 사용 하 여:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 다음 명령 사용 하 여 테이블의 내용을 봅니다. 정책을 적용 하기 전에 `demo` 테이블 전체 열을 표시 합니다.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![ranger 정책을 적용 하기 전에 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 마스킹 정책을 열의 마지막 4 자를 표시 하는 열을 적용 합니다.  
    1. Ranger 관리 UI로 이동 `https://CLUSTERNAME.azurehdinsight.net/ranger/`합니다.
    1. 클러스터의 Hive 서비스를 클릭 **Hive**합니다.
        ![ranger 정책을 적용 하기 전에 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. 클릭 합니다 **마스킹** 탭 차례로 **새 정책 추가** ![정책 목록](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. 원하는 정책 이름을 제공 합니다. 데이터베이스를 선택 합니다. **기본**, Hive 테이블: **데모**, Hive 열: **이름**, 사용자: **rsadmin2**, 액세스 형식: **선택**, 및 **부분 마스크: 마지막 4 개를 보여 줍니다** 에서 **마스킹 옵션 선택** 메뉴. **추가**를 클릭합니다.
                ![정책 목록](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 테이블의 콘텐츠를 다시 봅니다. Ranger 정책에 적용 한 후 열을 마지막 4 자를 볼 수 있습니다.

    ![ranger 정책을 적용 한 후 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>다음 단계

* [HDInsight 대화형 쿼리 사용](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Zeppelin에 Livy를 사용 하 여 Hive 웨어하우스 커넥터와의 상호 작용 하는 예가 spark 제출 및 pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
