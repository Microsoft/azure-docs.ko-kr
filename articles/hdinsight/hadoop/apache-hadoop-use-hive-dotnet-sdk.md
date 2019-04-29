---
title: HDInsight .NET SDK를 사용하여 Apache Hive 쿼리 실행 - Azure
description: HDInsight .NET SDK를 사용하여 Azure HDInsight Apache Hadoop에 Apache Hadoop 작업을 제출하는 방법에 대해 알아봅니다.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 947e797842000e4da2f9e22077bc32c24d6c6a74
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095386"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>HDInsight .NET SDK를 사용하여 Apache Hive 쿼리 실행
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

HDInsight.NET SDK를 사용하여 Apache Hive 쿼리를 제출하는 방법에 대해 알아봅니다. Hive 테이블을 나열하기 위한 Hive 쿼리를 제출한 후 결과를 표시하는 C# 프로그램을 작성합니다.

> [!NOTE]  
> 이 문서의 단계는 Windows 클라이언트에서 수행되어야 합니다. Hive와 함께 작동하도록 Linux, OS X 또는 Unix 클라이언트를 사용하는 방법에 대한 정보를 보려면 문서 맨 위에 표시된 탭 선택기를 사용합니다.

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* **HDInsight의 Apache Hadoop 클러스터** [HDInsight에서 Linux 기반 Hadoop 사용 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

    > [!WARNING]  
    > 2017년 9월 15일부터 HDInsight .NET SDK는 Azure Storage 계정의 Hive 쿼리 결과만 반환할 수 있습니다. Azure Data Lake Storage를 주 스토리지로 사용하는 HDInsight 클러스터에서 이 예제를 사용하면 .NET SDK를 사용하여 검색 결과를 검색할 수 없습니다.

* **Visual Studio 2013/2015/2017**

## <a name="run-a-hive-query"></a>Hive 쿼리 실행
HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 

**작업을 제출하려면**

1. Visual Studio를 사용하여 C# 콘솔 애플리케이션을 만듭니다.
2. NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. 다음 코드를 사용합니다.

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
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
                
                // Only Azure Storage accounts are supported by the SDK
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
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
4. **F5** 키를 눌러 애플리케이션을 실행합니다.

애플리케이션의 출력은 다음과 유사합니다.

![HDInsight Hadoop Hive 작업 출력](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight에 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight .NET SDK 참조](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [HDInsight에서 Apache Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Apache Sqoop 사용](apache-hadoop-use-sqoop-mac-linux.md)
* [비대화형 인증 .NET HDInsight 애플리케이션 만들기](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


