---
title: HDInsight에서 Curl과 Apache Hadoop Hive 사용 - Azure
description: Curl을 사용하여 HDInsight에 Apache Pig 작업을 원격으로 제출하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: e020cbc9c18db2142ee7f52cdac22a3518683fce
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095511"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>REST를 사용하여 HDInsight에서 Apache Hadoop과 함께 Apache Hive 쿼리 실행

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

WebHCat REST API를 사용하여 Azure HDInsight 클러스터에서 Apache Hadoop과 함께 Apache Hive 쿼리를 실행하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight 클러스터 버전 3.4 이상의 Linux 기반 Hadoop

  > [!IMPORTANT]  
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* REST 클라이언트 이 문서에서는 Windows PowerShell 및 [Curl](https://curl.haxx.se/) 예제를 사용합니다.

    > [!NOTE]  
    > Azure PowerShell은 HDInsight의 Hive 사용에 대한 전용 cmdlet을 제공합니다. 자세한 내용은 [Azure PowerShell과 함께 Apache Hive 사용](apache-hadoop-use-hive-powershell.md) 문서를 참조하세요.

이 문서에서는 Windows PowerShell 및 [Jq](https://stedolan.github.io/jq/)를 사용하여 REST 요청에서 반환된 JSON 데이터를 처리합니다.

## <a id="curl"></a>Hive 쿼리 실행

> [!NOTE]  
> WebHCat에서 cURL 또는 다른 모든 REST 통신을 사용하는 경우 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다.
>
> REST API는 [기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 Secure HTTP(HTTPS)를 사용하여 요청하십시오.

1. 이 문서의 스크립트에서 사용되는 클러스터 로그인을 설정하려면 다음 명령 중 하나를 사용합니다.

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. 클러스터 이름을 설정하려면 다음 명령 중 하나를 사용합니다.

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. HDInsight 클러스터에 연결할 수 있는지 확인하려면 다음 명령 중 하나를 사용합니다.

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    다음 텍스트와 유사한 응답이 수신됩니다.

    ```json
    {"status":"ok","version":"v1"}
    ```

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * `-u` - 요청을 인증하는 데 사용되는 사용자 이름 및 암호입니다.
    * `-G` - 이 요청이 GET 작업임을 나타냅니다.

   URL의 시작 부분, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`은 모든 요청에 대해 동일합니다. 경로, `/status`는 요청이 서버에 대한 WebHCat(Templeton라고도 함)의 상태를 반환하는 경우 나타납니다. 또한 다음 명령을 사용하여 Hive의 버전을 요청할 수 있습니다.

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    이 요청은 다음 텍스트와 유사한 응답을 반환합니다.

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. 다음을 사용하여 **log4jLogs**라는 새 테이블을 만듭니다.

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    이 요청은 REST API에 대한 요청의 일부로 데이터를 전송하는 POST 메서드를 사용합니다. 다음 데이터 값은 요청과 함께 전송됩니다.

     * `user.name` - 명령을 실행하는 사용자입니다.
     * `execute` - 실행할 HiveQL 문입니다.
     * `statusdir` - 이 작업의 상태를 기록할 디렉터리입니다.

   이러한 문은 다음 작업을 수행합니다.
   
   * `DROP TABLE` - 이미 테이블이 있으면 삭제됩니다.
   * `CREATE EXTERNAL TABLE` - Hive에서 새 "외부" 테이블을 만듭니다. 외부 테이블은 테이블 정의만 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.

     > [!NOTE]  
     > 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. 예를 들어 자동화된 데이터 업로드 프로세스 또는 다른 MapReduce 작업이 있습니다.
     >
     > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

   * `ROW FORMAT` - 데이터의 형식을 지정하는 방식입니다. 각 로그의 필드는 공백으로 구분됩니다.
   * `STORED AS TEXTFILE LOCATION` - 데이터가 저장된 위치(example/data 디렉터리)이며 텍스트로 저장됩니다.
   * `SELECT` - 열 **t4**에 값 **[ERROR]** 가 포함된 모든 행의 수를 선택합니다. 이 값이 포함된 행이 3개이므로 이 문은 **3** 값을 반환합니다.

     > [!NOTE]  
     > Curl과 함께 사용할 경우 HiveQL 문 사이의 공백이 `+` 문자로 바뀝니다. 구분 기호와 같이 공백을 포함하는 따옴표로 묶인 값은 `+`로 바뀌지 않아야 합니다.

      이 명령은 작업 상태를 확인하는 데 사용할 수 있는 작업 ID를 반환합니다.

5. 작업 상태를 확인하려면 다음 명령을 사용합니다.

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    작업이 완료된 경우 상태는 **SUCCEEDED**입니다.

6. 작업 상태가 **SUCCEEDED**로 변경되면 Azure Blob Storage에서 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달된 `statusdir` 매개 변수에는 출력 파일의 위치(이 경우 `/example/rest`)가 포함됩니다. 이 주소는 클러스터 기본 저장소의 `example/curl` 디렉터리에 출력을 저장합니다.

    [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 사용하여 이러한 파일을 나열하고 다운로드할 수 있습니다. Azure Storage에서 Azure CLI를 사용하는 방법에 대한 자세한 내용은 [Azure Storage에서 Azure CLI 사용](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) 문서를 참조하세요.

## <a id="nextsteps"></a>다음 단계

HDInsight Hive에 대한 일반적인 내용입니다.

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

이 문서에 사용된 REST API에 대한 자세한 내용은 [WebHCat 참조](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) 문서를 참조하세요.

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


