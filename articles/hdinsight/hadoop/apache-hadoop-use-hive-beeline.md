---
title: Apache Hive와 Apache Beeline 사용 - Azure HDInsight
description: Beeline 클라이언트를 사용하여 HDInsight에서 Hadoop과 Hive 쿼리를 실행하는 방법을 알아봅니다. Beeline은 JDBC를 통한 HiveServer2 작업을 위한 유틸리티입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: contperfq1
ms.openlocfilehash: f6d8f804fa26383435d191af27289ffd2ecb3e0b
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88755095"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Apache Hive와 Apache Beeline 클라이언트 사용

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell)을 사용하여 HDInsight에서 Apache Hive 쿼리를 실행하는 방법에 대해 알아봅니다.

Beeline은 HDInsight 클러스터의 헤드 노드에 포함된 Hive 클라이언트입니다. HDInsight 클러스터에 설치 된 Beeline 클라이언트에 연결 하거나 로컬로 Beeline을 설치 하려면 [Apache Beeline에 연결 또는 설치](connect-install-beeline.md)를 참조 하세요. Beeline은 JDBC를 사용하여 HDInsight 클러스터에서 호스팅되는 서비스인 HiveServer2에 연결합니다. 또한 Beeline을 사용하면 인터넷을 통해 HDInsight의 Hive에 원격으로 액세스할 수 있습니다. 다음 예에서는 Beeline에서 HDInsight에 연결 하는 데 사용 되는 가장 일반적인 연결 문자열을 제공 합니다.

## <a name="prerequisites-for-examples"></a>예에 대한 필수 조건

* HDInsight의 Hadoop 클러스터 [Linux에서 HDInsight 시작](./apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

* 클러스터의 기본 저장소에 대 한 URI 체계를 확인 합니다. 예를 들어  `wasb://` Azure Storage, `abfs://` Azure Data Lake Storage Gen2, Azure Data Lake Storage Gen1의 경우입니다 `adl://` . Azure Storage에 대해 보안 전송이 사용 되는 경우 URI는 `wasbs://` 입니다. 자세한 내용은 [보안 전송](../../storage/common/storage-require-secure-transfer.md)을 참조 하세요.

* 옵션 1: SSH 클라이언트 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. 이 문서의 단계는 대부분 SSH 세션에서 클러스터로 Beeline를 사용 하 고 있다고 가정 합니다.

* 옵션 2: 로컬 Beeline 클라이언트

## <a name="run-a-hive-query"></a>HIVE 쿼리 실행

이 예제는 SSH 연결에서 Beeline client를 사용 하는 것을 기반으로 합니다.

1. 아래 코드를 사용 하 여 클러스터에 대 한 SSH 연결을 엽니다. `sshuser`은 클러스터의 SSH 사용자로, `CLUSTERNAME`은 클러스터 이름으로 바꿉니다. 메시지가 표시 되 면 SSH 사용자 계정에 대 한 암호를 입력 합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 다음 명령을 입력 하 여 열려 있는 SSH 세션에서 Beeline 클라이언트를 사용 하 여 HiveServer2에 연결 합니다.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline 명령은 일반적으로 `!` 문자로 시작합니다. 예를 들어 `!help`는 도움말을 표시합니다. 그러나 일부 명령에서는 `!`를 생략할 수 있습니다. 예를 들어 `help`도 작동합니다.

    `!sql`HiveQL 문을 실행 하는 데 사용 되는가 있습니다. 그러나 HiveQL은 너무 일반적으로 사용되어 이전의 `!sql`를 생략할 수 있습니다. 다음 두 문은 동일합니다.

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

    ```output
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
    ```

    이 정보는 테이블의 열을 설명합니다.

5. 다음 문을 입력 하 여 HDInsight 클러스터와 함께 제공 되는 샘플 데이터를 사용 하 여 **log4jLogs** 라는 테이블을 만듭니다. URI 체계에 따라 필요에 따라 수정 합니다.

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

    이러한 문은 다음 작업을 수행 합니다.

    |인수를 제거합니다. |설명 |
    |---|---|
    |DROP TABLE|테이블이 있으면 삭제 됩니다.|
    |CREATE EXTERNAL TABLE|Hive에서 **외부** 테이블을 만듭니다. 외부 테이블만 테이블 정의를 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.|
    |행 형식|데이터의 형식을 지정 하는 방법입니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.|
    |TEXTFILE 위치로 저장 됨|데이터가 저장 되는 위치 및 파일 형식입니다.|
    |SELECT|**T4** 열에 **[ERROR]** 값이 포함 된 모든 행의 수를 선택 합니다. 이 값을 포함하는 세 개의 행이 있으므로 이 쿼리는 **3** 값을 반환합니다.|
    |'% .Log '와 INPUT__FILE__NAME.|Hive는 디렉터리의 모든 파일에 스키마를 적용 하려고 시도 합니다. 이 경우 디렉터리에는 스키마와 일치 하지 않는 파일이 포함 됩니다. 결과에 가비지 데이터가 나타나는 것을 방지하기 위해 이 명령문은 .log로 끝나는 파일의 데이터만 반환해야 함을 Hive에게 알립니다.|

   > [!NOTE]  
   > 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. 예를 들어 자동화된 데이터 업로드 프로세스 또는 MapReduce 작업이 있습니다.
   >
   > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

    이 명령의 출력은 다음 텍스트와 비슷합니다.

    ```output
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
    ```

6. Beeline 끝내기:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>HiveQL 파일 실행

이 예제는 이전 예제에서 연속 된 것입니다. 다음 단계를 사용하여 파일을 만든 다음 Beeline를 사용하여 실행합니다.

1. 다음 명령을 사용하여 **query.hql**이라는 파일을 만듭니다.

    ```bash
    nano query.hql
    ```

1. 다음 텍스트를 파일의 내용으로 사용합니다. 이 쿼리는 **errorLogs**라는 새 '내부' 테이블을 만듭니다.

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    이러한 문은 다음 작업을 수행 합니다.

    |인수를 제거합니다. |설명 |
    |---|---|
    |존재 하지 않는 경우 CREATE TABLE|테이블이 아직 없으면 생성 됩니다. **EXTERNAL** 키워드가 사용 되지 않으므로이 문은 내부 테이블을 만듭니다. 내부 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에 서 완전히 관리됩니다.|
    |ORC로 저장 됨|데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. ORC 형식은 Hive 데이터를 저장하기 위해 고도로 최적화되고 효율적인 형식입니다.|
    |덮어쓰기 삽입 ... [|**[ERROR]** 가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.|

    > [!NOTE]  
    > 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

1. 파일을 저장 하려면 **Ctrl** + **X**를 사용한 다음 **Y**를 입력 하 고 마지막으로 **enter**키를 누릅니다.

1. 다음을 사용하여 Beeline을 통해 파일을 실행합니다.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` 매개 변수는 Beeline을 시작하고 `query.hql` 파일의 문을 실행합니다. 쿼리가 완료되면 `jdbc:hive2://headnodehost:10001/>` 프롬프트가 표시됩니다. 쿼리 완료 후 Beeline을 종료하는 `-f` 매개 변수를 사용하여 파일을 실행할 수도 있습니다.

1. **errorLogs** 테이블을 만들었는지 확인하려면 다음 문을 사용하여 **errorLogs**에서 모든 행을 반환합니다.

    ```hiveql
    SELECT * from errorLogs;
    ```

    데이터 중 t4 열에 모두 **[ERROR]** 가 포함된 3개 행이 반환되어야 합니다.

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>다음 단계

* HDInsight의 Hive에 대 한 일반적인 정보는 [hdinsight에서 Apache Hadoop와 Apache Hive 사용](hdinsight-use-hive.md) 을 참조 하세요.

* HDInsight에서 Hadoop으로 작업 하는 다른 방법에 대 한 자세한 내용은 [hdinsight에서 Apache Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md) 을 참조 하세요.
