---
title: HDInsight에서 PowerShell과 Apache Pig 사용 - Azure
description: Azure PowerShell을 사용 하 여 HDInsight에서 Apache Hadoop 클러스터에 Apache Pig 작업을 제출 하는 방법에 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 9ad788989273f28f38ee95f8d669fdf17f1fd785
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105039"
---
# <a name="use-azure-powershell-to-run-apache-pig-jobs-with-hdinsight"></a>Azure PowerShell을 사용하여 HDInsight에서 Apache Pig 작업 실행

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

이 문서는 Azure PowerShell을 사용 하 여 HDInsight 클러스터에는 Apache Hadoop로 Apache Pig 작업을 제출 하는 예제를 제공 합니다. Pig를 사용하면 매핑하고 함수를 줄이는 대신 데이터 변환을 모델링하는 언어(Pig Latin)를 사용하여 MapReduce 작업을 작성할 수 있습니다.

> [!NOTE]  
> 이 문서에는 예제에 사용된 Pig Latin 문이 수행하는 작업에 대해 자세한 설명을 제공하지 않습니다. 이 예제에 사용된 Pig Latin에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop과 Apache Pig 사용](hdinsight-use-pig.md)을 참조하세요.

## <a id="prereq"></a>필수 조건

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Azure HDInsight 클러스터**

  > [!IMPORTANT]  
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* **Azure PowerShell이 포함된 워크스테이션**.

## <a id="powershell"></a>Apache Pig 작업 실행

Azure PowerShell은 HDInsight에서 Pig 작업을 원격으로 실행할 수 있는 *cmdlet* 을 제공합니다. 내부적으로 PowerShell은 HDInsight 클러스터에서 실행되는 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)으로 REST 호출을 사용합니다.

다음 cmdlet은 원격 HDInsight 클러스터에서 Pig 작업을 실행할 때 사용됩니다.

* **Connect-AzAccount**: Azure 구독에 대해 Azure PowerShell을 인증합니다.
* **New-AzHDInsightPigJobDefinition**: 지정한 Pig Latin 문을 사용하여 작업 정의를 만듭니다.
* **Start-AzHDInsightJob**: HDInsight에 작업 정의를 보내고 작업을 시작합니다. *작업* 개체가 반환됩니다.
* **Wait-AzHDInsightJob**: 작업 개체를 사용하여 작업 상태를 확인합니다. 작업이 완료될 때까지 기다리거나 대기 시간이 초과될 때까지 기다립니다.
* **Get-AzHDInsightJobOutput**: 작업의 출력을 검색하는 데 사용합니다.

다음 단계는 HDInsight 클러스터에서 작업을 실행하기 위해 이러한 cmdlet을 사용하는 방법에 대해 설명합니다.

1. 편집기를 사용하여 다음 코드를 **pigjob.ps1**로 저장합니다.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. 새 Windows PowerShell 명령 프롬프트를 엽니다. **pigjob.ps1** 파일의 디렉터리 위치를 변경한 다음 명령을 사용하여 스크립트를 실행합니다.

        .\pigjob.ps1

    Azure 구독에 로그인하라는 메시지가 표시됩니다. 그런 다음 HTTPs/관리자 계정 이름과 HDInsight 클러스터 암호를 묻는 메시지가 표시됩니다.

2. 작업이 완료되면 다음 텍스트와 유사한 정보가 반환됩니다.

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>문제 해결

작업이 완료될 때 정보가 반환되지 않으면 오류 로그를 봅니다. 이 작업에 대한 오류 정보를 보려면 **pigjob.ps1** 파일 끝에 다음 명령을 추가하고 저장한 다음 다시 실행합니다.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

이 cmdlet은 작업 프로세스 중 STDERR에 작성된 정보를 반환합니다.

## <a id="summary"></a>요약
이처럼 Azure PowerShell은 HDInsight 클러스터에서 Pig 작업을 실행하고, 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 알려줍니다.

## <a id="nextsteps"></a>다음 단계
HDInsight의 Pig에 대한 일반적인 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)
