---
title: Curl을 사용 하 여 Azure HDInsight에서 Apache Sqoop을 사용 하 여 데이터를 내보내려면
description: 원격으로 Curl을 사용하여 HDInsight에 Apache Sqoop 작업을 제출하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 345f492c5b2c754cbbcfa150561ee06b5a4154a5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126956"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Curl 사용 하 여 HDInsight에서 Apache Sqoop 작업 실행
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight의 Apache Hadoop 클러스터에서 Curl을 사용하여 Apache Sqoop 작업을 실행하는 방법에 대해 알아봅니다. 이 문서에서는 Azure storage에서 데이터를 내보내고 Curl을 사용 하 여 SQL Server 데이터베이스에 가져오는 방법을 보여 줍니다. 이 문서는 이어지는 [HDInsight에서 Hadoop과 Apache Sqoop을 사용 하 여](./hdinsight-use-sqoop.md)입니다.

Sqoop 작업을 실행하고 모니터링하며 결과를 검색하는 원시 HTTP 요청을 사용하여 HDInsight와 상호 작용하는 방법을 설명하는 데 Curl을 사용합니다. 이 Curl은 HDInsight 클러스터에서 제공하는 WebHCat REST API(이전의 Templeton)를 사용하여 작동합니다.

## <a name="prerequisites"></a>필수 조건

* 완료 [테스트 환경 설정](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) 에서 [HDInsight에서 Hadoop과 Apache Sqoop을 사용 하 여](./hdinsight-use-sqoop.md)입니다.

* Azure SQL database를 쿼리 하는 클라이언트입니다. 사용 하는 것이 좋습니다 [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) 하거나 [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)합니다.

* [Curl](https://curl.haxx.se/). Curl은 HDInsight 클러스터에서(로) 데이터를 전송하는 도구입니다.

* [jq](https://stedolan.github.io/jq/). jq 유틸리티는 REST 요청에서 반환된 JSON 데이터를 처리하는 데 사용합니다.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Curl을 사용하여 Apache Sqoop 작업 제출

Azure storage에서 SQL Server로 Apache Sqoop 작업을 사용 하 여 데이터를 내보내는 데 Curl을 사용 합니다.

> [!NOTE]  
> WebHCat에서 Curl 또는 다른 모든 REST 통신을 사용하는 경우 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. 또한 클러스터 이름을 서버로 요청을 보내는 데 사용되는 URI(Uniform Resource Identifier)의 일부로 사용해야 합니다.

이 섹션의 명령에 대 한 대체 `USERNAME` 사용자로 바꾸고, 클러스터 인증 `PASSWORD` 사용자 계정의 암호를 사용 합니다. `CLUSTERNAME`을 클러스터의 이름으로 바꿉니다.
 
REST API는 [기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 보안 HTTP(HTTPS)를 사용하여 요청해야 합니다.

1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다.

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    그러면 다음과 같은 응답이 표시됩니다.

    ```json
    {"status":"ok","version":"v1"}
    ```

2. 바꿉니다 `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`를 `PASSWORD`, `SQLDATABASENAME` 필수 구성 요소에서 적절 한 값을 사용 하 여 합니다. 다음을 사용하여 Sqoop 작업을 제출합니다.

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

   * **-d** - `-G`가 사용되지 않으므로 요청은 POST 메서드로 기본 설정됩니다. `-d` 는 요청과 함께 전송되는 데이터 값을 지정합니다.

       * **user.name** - 명령을 실행하는 사용자입니다.

       * **명령** - 실행할 Sqoop 명령입니다.

       * **statusdir** - 이 작업의 상태를 기록할 디렉터리입니다.

     이 명령은 작업 상태를 확인하는데 사용할 수 있는 작업 ID를 반환해야 합니다.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. 작업 상태를 확인하려면 다음 명령을 사용합니다. `JOBID`를 이전 단계에서 반환된 값으로 바꿉니다. 예를 들어 반환 값이 `{"id":"job_1415651640909_0026"}`, 한 다음 `JOBID` 것 `job_1415651640909_0026`입니다.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    작업이 완료되면 상태는 **SUCCEEDED**가 됩니다.
   
   > [!NOTE]  
   > 이 Curl 요청은 작업에 관한 정보가 있는 JSON(JavaScript Object Notation) 문서를 반환합니다. jq는 상태 값을 검색하는 데만 사용됩니다.

4. 작업 상태가 **SUCCEEDED**로 변경되면 Azure Blob Storage에서 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달된 `statusdir` 매개 변수에는 출력 파일의 위치(이 경우 `wasb:///example/data/sqoop/curl`)가 포함됩니다. 작업의 출력을 저장 하는이 주소는 `example/data/sqoop/curl` HDInsight 클러스터에서 사용 되는 저장소 컨테이너에 디렉터리입니다.

    Azure Portal을 사용하여 stderr 및 stdout Blob에 액세스할 수 있습니다.

5. 데이터를 내보낸 있는지를 확인 하려면 SQL 클라이언트에서 다음 쿼리를 사용 하 여 내보낸된 데이터를 보려면:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>제한 사항
* 대량 내보내기 - Linux 기반 HDInsight와 함께 Microsoft SQL Server 또는 Azure SQL Database에 데이터를 내보내는 데 사용된 Sqoop 커넥터도 현재 대량 삽입을 지원하지 않습니다.
* Batch - Linux 기반 HDInsight와 함께 삽입을 수행할 때 `-batch` 스위치를 사용하는 경우 Sqoop는 삽입 작업을 일괄 처리하는 대신 여러 삽입 작업을 수행합니다.

## <a name="summary"></a>요약
이 문서에서 볼 수 있듯이 원시 HTTP 요청을 사용하여 HDInsight 클러스터의 Sqoop 작업을 실행하고 모니터링하며 결과를 확인할 수 있습니다.

이 문서에 사용된 REST 인터페이스에 대한 자세한 내용은 <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API 가이드</a>를 참조하세요.

## <a name="next-steps"></a>다음 단계
[HDInsight에서 Apache Hadoop과 함께 Apache Sqoop 사용](hdinsight-use-sqoop.md)

curl 관련 기타 HDInsight 문서:
 
* [Azure REST API를 사용하여 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [REST를 사용하여 HDInsight에서 Apache Hadoop과 함께 Apache Hive 쿼리 실행](apache-hadoop-use-hive-curl.md)
* [REST를 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 작업 실행](apache-hadoop-use-mapreduce-curl.md)
* [cURL을 사용하여 HDInsight에서 Apache Hadoop과 함께 Apache Pig 작업 실행](apache-hadoop-use-pig-curl.md)
