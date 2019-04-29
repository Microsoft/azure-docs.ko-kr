---
title: Hadoop용 .NET SDK로 Apache Pig 작업 실행 - Azure HDInsight
description: HDInsight에서 Hadoop로 Pig 작업을 제출하기 위해 Hadoop용 .NET SDK를 사용하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: hrasheed
ms.openlocfilehash: ebf1f2806a6606294c61860a24fb2f02033a4bf4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110959"
---
# <a name="run-apache-pig-jobs-using-the-net-sdk-for-apache-hadoop-in-hdinsight"></a>HDInsight에서 Apache Hadoop용 .NET SDK를 사용하여 Apache Pig 작업 실행

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Azure HDInsight에서 Hadoop로 Apache Pig 작업을 제출하기 위해 Apache Hadoop용 .NET SDK를 사용하는 방법에 대해 알아봅니다.

HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. Pig를 사용하면 일련의 데이터 변환을 모델링하여 MapReduce 작업을 만들 수 있습니다. 이 문서에서는 기본 C# 애플리케이션을 사용하여 HDInsight 클러스터에 Pig 작업을 제출하는 방법에 대해 배웁니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Azure HDInsight(HDInsight의 Hadoop) 클러스터(Windows 또는 Linux 기반)

  > [!IMPORTANT]
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* Visual Studio 2012, 2013, 2015 또는 2017.

## <a name="create-the-application"></a>애플리케이션 만들기

HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다.

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.

2. 새 프로젝트에서 다음 값을 입력하거나 선택합니다.

   | 자산 | 값 |
   | ------ | ------ |
   | Category | Templates/Visual C#/Windows |
   | Template | 콘솔 애플리케이션 |
   | 이름 | SubmitPigJob |

3. **확인**을 클릭하여 프로젝트를 만듭니다.

4. **도구** 메뉴에서 **라이브러리 패키지 관리자** 또는 **NuGet 패키지 관리자**를 선택한 다음 **패키지 관리자 콘솔**을 선택합니다.

5. .NET SDK 패키지를 설치하려면 다음 명령을 사용합니다.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. 솔루션 탐색기에서 **Program.cs** 를 두 번 클릭하여 엽니다. 기존 코드를 다음으로 바꿉니다.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. 애플리케이션을 시작하려면 **F5** 키를 누릅니다.

8. 애플리케이션을 종료하려면 **ENTER** 키를 누릅니다.

## <a name="next-steps"></a>다음 단계

HDInsight의 Pig에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop과 Apache Pig 사용](hdinsight-use-pig.md)을 참조하세요.

HDInsight에서 Hadoop을 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
