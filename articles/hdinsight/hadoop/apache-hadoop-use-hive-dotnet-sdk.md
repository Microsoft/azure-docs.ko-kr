---
title: HDInsight .NET SDK를 사용하여 Apache Hive 쿼리 실행 - Azure
description: HDInsight .NET SDK를 사용하여 Azure HDInsight Apache Hadoop에 Apache Hadoop 작업을 제출하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: cdb2207ab834ab84cedd2ad23e5e26186ac44039
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020476"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>HDInsight .NET SDK를 사용하여 Apache Hive 쿼리 실행

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

HDInsight.NET SDK를 사용하여 Apache Hive 쿼리를 제출하는 방법에 대해 알아봅니다. Hive 테이블을 나열하기 위한 Hive 쿼리를 제출한 후 결과를 표시하는 C# 프로그램을 작성합니다.

> [!NOTE]  
> 이 문서의 단계는 Windows 클라이언트에서 수행되어야 합니다. Hive와 함께 작동하도록 Linux, OS X 또는 Unix 클라이언트를 사용하는 방법에 대한 정보를 보려면 문서 맨 위에 표시된 탭 선택기를 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* HDInsight의 Apache Hadoop 클러스터. [HDInsight에서 Linux 기반 Hadoop 사용 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

    > [!IMPORTANT]  
    > 2017년 9월 15일부터 HDInsight .NET SDK는 Azure Storage 계정의 Hive 쿼리 결과만 반환할 수 있습니다. Azure Data Lake Storage를 주 스토리지로 사용하는 HDInsight 클러스터에서 이 예제를 사용하면 .NET SDK를 사용하여 검색 결과를 검색할 수 없습니다.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 이상. 최소한의 워크 로드 **.net 데스크톱 개발** 을 설치 해야 합니다.

## <a name="run-a-hive-query"></a>Hive 쿼리 실행

HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다.

1. Visual Studio를 사용하여 C# 콘솔 애플리케이션을 만듭니다.

1. NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. 변수에 대 한 값을 초기화 하려면 아래 코드를 편집 `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName` 합니다. 그런 다음 수정 된 코드를 Visual Studio에서 **Program.cs** 의 전체 콘텐츠로 사용 합니다.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. **F5** 키를 눌러 애플리케이션을 실행합니다.

응용 프로그램의 출력은 다음과 유사 해야 합니다.

![HDInsight Hadoop Hive 작업 출력](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight .NET SDK를 사용 하 여 Apache Hive 쿼리를 전송 하는 방법을 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight에 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight .NET SDK 참조](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [HDInsight에서 Apache Sqoop 사용](apache-hadoop-use-sqoop-mac-linux.md)
* [비대화형 인증 .NET HDInsight 애플리케이션 만들기](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
