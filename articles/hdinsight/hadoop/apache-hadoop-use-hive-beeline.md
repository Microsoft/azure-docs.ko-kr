---
title: Apache Hive와 Apache Beeline 사용 - Azure HDInsight
description: Beeline 클라이언트를 사용하여 HDInsight에서 Hadoop과 Hive 쿼리를 실행하는 방법을 알아봅니다. Beeline은 JDBC를 통한 HiveServer2 작업을 위한 유틸리티입니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: dcfcd4b55f848e1725e286e6ef2a87a2c36e5a71
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123147"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Apache Hive와 Apache Beeline 클라이언트 사용

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell)을 사용하여 HDInsight에서 Apache Hive 쿼리를 실행하는 방법에 대해 알아봅니다.

Beeline은 HDInsight 클러스터의 헤드 노드에 포함된 Hive 클라이언트입니다. Beeline은 JDBC를 사용하여 HDInsight 클러스터에서 호스팅되는 서비스인 HiveServer2에 연결합니다. 또한 Beeline을 사용하면 인터넷을 통해 HDInsight의 Hive에 원격으로 액세스할 수 있습니다. 다음 예에서는 Beeline에서 HDInsight에 연결하는 데 사용되는 가장 일반적인 연결 문자열을 제공합니다.

## <a name="types-of-connections"></a>연결 형식

### <a name="from-an-ssh-session"></a>SSH 세션에서

에 연결할 때 SSH 세션에서 클러스터 헤드 노드에 연결할 수 있습니다 다음 합니다 `headnodehost` 포트에서 주소 `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Azure Virtual Network를 통해

에 연결할 때 클라이언트에서 HDInsight Azure Virtual Network를 통해 클러스터 헤드 노드의 정규화 된 도메인 이름 (FQDN)을 제공 해야 합니다. 이 연결은 클러스터 노드로 직접 설정되므로 연결은 포트 `10001`을 사용합니다.

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

대체 `<headnode-FQDN>` 클러스터 헤드 노드의 정규화 된 도메인 이름입니다. 헤드 노드의 정규화된 도메인 이름을 찾으려면 [Apache Ambari REST API를 사용하여 HDInsight 관리](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 문서의 정보를 사용합니다.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>HDInsight 엔터프라이즈 보안 패키지 (ESP) 클러스터

엔터프라이즈 보안 패키지 (ESP) 클러스터에 클라이언트에서 연결 하려면 Active Directory (AAD (Azure)에 가입 하는 경우 도메인 이름을 지정 해야 `<AAD-Domain>` 클러스터에 액세스할 수 있는 권한이 있는 도메인 사용자 계정의 이름과 `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

`<username>`을 클러스터에 액세스할 수 있는 권한이 있는 도메인의 계정 이름으로 바꿉니다. 대체 `<AAD-DOMAIN>` 의 Active Directory AAD (Azure) 클러스터에 포함 된 이름입니다. 에 대 한 대문자 문자열을 사용 하 여는 `<AAD-DOMAIN>` 값 그렇지 않은 경우 자격 증명을 찾을 수 없습니다. 확인 `/etc/krb5.conf` 필요한 경우 영역 이름에 대 한 합니다.

---

### <a name="over-public-internet"></a>공용 인터넷을 통해

공용 인터넷을 통해 연결할 때 클러스터 로그인 계정 이름(기본 `admin`) 및 암호를 제공해야 합니다. 예를 들어 클라이언트 시스템에서 Beeline을 사용하여 `<clustername>.azurehdinsight.net` 주소에 연결합니다. 이 연결은 `443` 포트를 통해 이루어지며 SSL을 사용하여 암호화됩니다.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

`clustername`을 HDInsight 클러스터 이름으로 바꿉니다. `admin`을 클러스터의 클러스터 로그인 계정으로 바꿉니다. `password`를 클러스터 로그인 계정의 암호로 바꿉니다.

---

### <a id="sparksql"></a>Apache Spark와 함께 Beeline 사용

Apache Spark는 자체적으로 HiveServer2를 구현하며, HiveServer2는 종종 Spark Thrift 서버라고 합니다. 이 서비스는 Hive 대신 Spark SQL을 사용하여 쿼리를 해결하고, 쿼리에 따라 더 나은 성능을 제공할 수 있습니다.

#### <a name="over-public-internet-with-apache-spark"></a>Apache Spark를 사용 하 여 공용 인터넷을 통해

인터넷을 통해 연결할 때 사용 된 연결 문자열은 약간 다릅니다. 포함 하는 대신 `httpPath=/hive2` 것이 `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>클러스터에서 Apache Spark를 사용 하 여 헤드 또는 내부 Azure Virtual Network

클러스터 헤드 노드에서 직접 연결하거나 HDInsight 클러스터와 동일한 Azure Virtual Network 내부의 리소스에서 연결하는 경우 `10001` 포트 대신 `10002` 포트를 Spark Thrift 서버에 사용해야 합니다. 다음 예제에서는 헤드 노드에 직접 연결 하는 방법을 보여 줍니다.

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>필수 조건

* HDInsight에서 Hadoop 클러스터입니다. 참조 [Linux에서 HDInsight 시작](./apache-hadoop-linux-tutorial-get-started.md)합니다.

* 표시 된 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 클러스터의 기본 저장소에 대 한 합니다. 예를 들어 `wasb://` Azure Storage에 대 한 `abfs://` 에 대 한 Azure Data Lake 저장소 Gen2 또는 `adl://` Azure 데이터 레이크 저장소 Gen1에 대 한 합니다. 에 대해 Azure Storage 또는 Data Lake 저장소 Gen2 전송 보안을 사용 하는 경우 URI는 `wasbs://` 또는 `abfss://`, 각각. 자세한 내용은 [보안 전송](../../storage/common/storage-require-secure-transfer.md)합니다.


* 옵션 1: SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. 이 문서에 나온 대부분의 단계는 SSH 세션에서 클러스터까지 Beeline을 사용한다고 가정합니다.

* 옵션 2:  로컬 Beeline 클라이언트입니다.


## <a id="beeline"></a>Hive 쿼리 실행

이 예제에서는 SSH 연결에서 Beeline 클라이언트를 사용 하는 기반으로 합니다.

1. 아래 코드를 사용 하 여 클러스터에 대 한 SSH 연결을 엽니다. `sshuser`은 클러스터의 SSH 사용자로, `CLUSTERNAME`은 클러스터 이름으로 바꿉니다. 메시지가 표시 되 면 SSH 사용자 계정의 암호를 입력 합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. HiveServer2에 연결 Beeline 클라이언트를 사용 하 여 열린 SSH 세션에서 다음 명령을 입력 하 여:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline 명령은 일반적으로 `!` 문자로 시작합니다. 예를 들어 `!help`는 도움말을 표시합니다. 그러나 일부 명령에서는 `!`를 생략할 수 있습니다. 예를 들어 `help`도 작동합니다.

    HiveQL 문을 실행하는 데 사용되는 `!sql`이 있습니다. 그러나 HiveQL은 너무 일반적으로 사용되어 이전의 `!sql`를 생략할 수 있습니다. 다음 두 문은 동일합니다.

    ```hiveql
    !sql show tables;
    show tables;
    ```

    새 클러스터에서는 **hivesampletable**이라는 테이블 하나만 나열됩니다.

4. 다음 명령을 사용하여 hivesampletable의 스키마를 표시합니다.

    ```hiveql
    describe hivesampletable;
    ```

    이 명령은 다음 정보를 반환합니다.

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    이 정보는 테이블의 열을 설명합니다.

5. 다음 문을 입력하여 HDInsight 클러스터와 함께 제공되는 샘플 데이터로 **log4jLogs**라는 새 테이블을 만듭니다. (필요에 따라 수정에 따라 프로그램 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    이러한 문은 다음 작업을 수행합니다.

    * `DROP TABLE` - 테이블이 있으면 삭제됩니다.

    * `CREATE EXTERNAL TABLE` - Hive에서 **외부** 테이블을 만듭니다. 외부 테이블만 테이블 정의를 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.

    * `ROW FORMAT` - 데이터의 형식을 지정하는 방식입니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

    * `STORED AS TEXTFILE LOCATION` - 데이터를 저장하는 위치 및 파일 형식입니다.

    * `SELECT` - 열 **t4**에 값 **[ERROR]** 가 포함된 모든 행의 수를 선택합니다. 이 값을 포함하는 세 개의 행이 있으므로 이 쿼리는 **3** 값을 반환합니다.

    * `INPUT__FILE__NAME LIKE '%.log'` - Hive는 디렉터리의 모든 파일에 스키마를 적용하려고 합니다. 이 경우 디렉터리에 스키마와 일치하지 않는 파일이 포함되어 있습니다. 결과에 가비지 데이터가 나타나는 것을 방지하기 위해 이 명령문은 .log로 끝나는 파일의 데이터만 반환해야 함을 Hive에게 알립니다.

   > [!NOTE]  
   > 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. 예를 들어 자동화된 데이터 업로드 프로세스 또는 MapReduce 작업이 있습니다.
   >
   > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

    이 명령의 출력은 다음 텍스트와 비슷합니다.

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. Beeline을 종료하려면 `!exit`을 사용합니다.

## <a id="file"></a>HiveQL 파일 실행

이전 예제에서 연속 작업입니다. 다음 단계를 사용하여 파일을 만든 다음 Beeline를 사용하여 실행합니다.

1. 다음 명령을 사용하여 **query.hql**이라는 파일을 만듭니다.

    ```bash
    nano query.hql
    ```

2. 다음 텍스트를 파일의 내용으로 사용합니다. 이 쿼리는 **errorLogs**라는 새 '내부' 테이블을 만듭니다.

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    이러한 문은 다음 작업을 수행합니다.

   * **CREATE TABLE IF NOT EXISTS** - 테이블이 아직 없으면 테이블을 만듭니다. **EXTERNAL** 키워드가 사용되지 않으므로 이 문은 내부 테이블을 만듭니다. 내부 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에 서 완전히 관리됩니다.
   * **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. ORC 형식은 Hive 데이터를 저장하기 위해 고도로 최적화되고 효율적인 형식입니다.
   * **덮어쓰기 삽입... SELECT** - **[ERROR]** 가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.

    > [!NOTE]  
    > 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

3. 파일을 저장하려면 **Ctrl**+**_X**을 사용한 다음 **Y**를 입력하고 마지막으로 **Enter** 키를 누릅니다.

4. 다음을 사용하여 Beeline을 통해 파일을 실행합니다.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` 매개 변수는 Beeline을 시작하고 `query.hql` 파일의 문을 실행합니다. 쿼리가 완료되면 `jdbc:hive2://headnodehost:10001/>` 프롬프트가 표시됩니다. 쿼리 완료 후 Beeline을 종료하는 `-f` 매개 변수를 사용하여 파일을 실행할 수도 있습니다.

5. **errorLogs** 테이블을 만들었는지 확인하려면 다음 문을 사용하여 **errorLogs**에서 모든 행을 반환합니다.

    ```hiveql
    SELECT * from errorLogs;
    ```

    데이터 중 t4 열에 모두 **[ERROR]** 가 포함된 3개 행이 반환되어야 합니다.

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>다음 단계

HDInsight의 Hive에 대한 보다 일반적인 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop을 사용하여 작업할 수 있는 다른 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
