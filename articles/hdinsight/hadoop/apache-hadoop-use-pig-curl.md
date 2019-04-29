---
title: HDInsight에서 REST와 함께 Apache Hadoop Pig 사용 - Azure
description: Azure HDInsight의 Apache Hadoop 클러스터에서 REST를 사용하여 Pig Latin 작업을 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 690a4bf08c7bfd6ccc039fdd04a3dda26b5a9301
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124103"
---
# <a name="run-apache-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>REST를 사용하여 HDInsight에서 Apache Hadoop과 Apache Pig 작업 실행

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Azure HDInsight 클러스터에 대한 REST 요청을 만들어 Apache Pig Latin 작업을 실행하는 방법을 알아봅니다. Curl은 WebHCat REST API를 사용하여 HDInsight와 상호 작용하는 방법을 보여 주는 데 사용합니다.

> [!NOTE]  
> Linux 기반 Apache Hadoop 서버에는 익숙하지만 HDInsight는 생소하다면 [Linux 기반 HDInsight 팁](../hdinsight-hadoop-linux-information.md)을 참조하세요.

## <a id="prereq"></a>필수 조건

* Azure HDInsight(HDInsight의 Hadoop) 클러스터(Linux 기반 또는 Windows 기반)

  > [!IMPORTANT]  
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* [Curl](https://curl.haxx.se/)

* [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Curl을 사용하여 Apache Pig 작업 실행


> [!NOTE]
> REST API는 [기본 액세스 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안이 유지됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 Secure HTTP(HTTPS)를 사용하여 요청합니다.
>
> 이 섹션에서 명령을 사용하는 경우 `USERNAME`은 클러스터에 대해 인증할 사용자로 바꾸고 `PASSWORD`는 사용자 계정의 암호로 바꿉니다. `CLUSTERNAME`을 클러스터의 이름으로 바꿉니다.
>


1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다.

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    다음 JSON 응답이 표시됩니다.

        {"status":"ok","version":"v1"}

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

   * **-u**: 요청을 인증하는 데 사용되는 사용자 이름 및 암호입니다.
   * **-G**: 이 요청이 GET 요청임을 나타냅니다.

     URL의 시작 부분, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**은 모든 요청에 대해 동일합니다. **/status** 경로는 요청이 서버에 대한 WebHCat(Templeton라고도 함)의 상태를 반환하는 경우 나타납니다.

2. 다음 코드를 사용하여 클러스터에 Pig Latin 작업을 제출합니다.

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

   * **-d**: `-G`가 사용되지 않으므로 요청은 POST 메서드로 기본 설정됩니다. `-d` 는 요청과 함께 전송되는 데이터 값을 지정합니다.

   * **user.name**: 명령을 실행하는 사용자입니다.
   * **execute**: 실행할 Pig Latin 문입니다.
   * **statusdir**: 이 작업의 상태를 기록할 디렉터리입니다.

     > [!NOTE]  
     > Curl과 함께 사용할 경우 Pig Latin 문의 공백이 `+` 문자로 바뀝니다.

     이 명령은 작업 상태를 확인하는데 사용할 수 있는 작업 ID를 반환해야 합니다. 예를 들면 다음과 같습니다.

       {"id":"job_1415651640909_0026"}

3. 작업 상태를 확인하려면 다음 명령을 사용합니다.

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     `JOBID`를 이전 단계에서 반환된 값으로 바꿉니다. 예를 들어 반환 값이 `{"id":"job_1415651640909_0026"}`인 경우 `JOBID`는 `job_1415651640909_0026`입니다.

    작업이 완료된 경우 상태는 **SUCCEEDED**입니다.

    > [!NOTE]  
    > 이 Curl 요청은 작업에 관한 정보가 있는 JSON(JavaScript Object Notation) 문서를 반환합니다. jq는 상태 값을 검색하는 데만 사용됩니다.

## <a id="results"></a>결과 보기

작업 상태가 **SUCCEEDED**로 변경되면 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달된 `statusdir` 매개 변수에는 출력 파일의 위치(이 경우 `/example/pigcurl`)가 포함됩니다.

HDInsight는 Azure Storage 또는 Azure Data Lake Storage를 기본 데이터 스토리지로 사용할 수 있습니다. 여러 가지 방법으로 사용하는 것에 따라 데이터를 가져올 수 있습니다. 자세한 내용은 [Linux 기반 HDInsight 정보](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-storage) 문서의 저장소 섹션을 참조하세요.

## <a id="summary"></a>요약

이 문서에 설명된 대로 원시 HTTP 요청을 사용하여 HDInsight 클러스터에서 Pig 작업을 실행하고 모니터링하며 그 결과를 확인할 수 있습니다.

이 문서에 사용된 REST 인터페이스에 대한 자세한 내용은 [WebHCat 참조](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)를 참조하세요.

## <a id="nextsteps"></a>다음 단계

HDInsight의 Pig에 대한 일반적인 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)
