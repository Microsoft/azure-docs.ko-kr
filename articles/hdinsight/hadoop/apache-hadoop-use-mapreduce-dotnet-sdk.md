---
title: HDInsight .NET SDK를 사용하여 MapReduce 작업 제출 - Azure
description: HDInsight .NET SDK를 사용하여 Azure HDInsight Apache Hadoop에 MapReduce 작업을 제출하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/15/2020
ms.openlocfilehash: 3de7101eb998d0f9b3b57afba6d19595738cbb63
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020408"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK를 사용하여 MapReduce 작업 실행

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

HDInsight .NET SDK를 사용하여 MapReduce 작업을 제출하는 방법을 알아봅니다. HDInsight 클러스터에는 여러 MapReduce 샘플이 담긴 jar 파일이 포함되어 있습니다. Jar 파일은 `/example/jars/hadoop-mapreduce-examples.jar` 입니다.  샘플 중 하나는 **wordcount**입니다. C# 콘솔 애플리케이션을 개발하여 단어 세기 작업을 제출합니다.  작업은 `/example/data/gutenberg/davinci.txt` 파일을 읽고 결과를로 출력 `/example/data/davinciwordcount` 합니다.  애플리케이션을 다시 실행하려면 출력 폴더를 정리해야 합니다.

> [!NOTE]  
> 이 문서의 단계는 Windows 클라이언트에서 수행되어야 합니다. Hive와 함께 작동하도록 Linux, OS X 또는 Unix 클라이언트를 사용하는 방법에 대한 정보를 보려면 문서 맨 위에 표시된 탭 선택기를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Hadoop 클러스터. [Azure Portal를 사용 하 여 Apache Hadoop 클러스터 만들기를](../hdinsight-hadoop-create-linux-clusters-portal.md)참조 하세요.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/)

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK를 사용하여 MapReduce 작업 제출

HDInsight .NET SDK는 .net에서 HDInsight 클러스터로 더 쉽게 작업할 수 있도록 하는 .NET 클라이언트 라이브러리를 제공 합니다.

1. Visual Studio를 시작 하 고 c # 콘솔 응용 프로그램을 만듭니다.

1. **도구**  >  **NuGet 패키지 관리자**  >  **패키지 관리자 콘솔** 로 이동 하 여 다음 명령을 입력 합니다.

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. 아래 코드를 **Program.cs**에 복사 합니다. 그런 다음,,, 및에 대 한 값을 설정 하 여 코드를 편집 `existingClusterName` `existingClusterPassword` `defaultStorageAccountName` `defaultStorageAccountKey` `defaultStorageContainerName` 합니다.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. **F5** 키를 눌러 애플리케이션을 실행합니다.

작업을 다시 실행 하려면 샘플에서 작업 출력 폴더 이름을 변경 해야 합니다 `/example/data/davinciwordcount` .

작업이 성공적으로 완료 되 면 응용 프로그램은 출력 파일의 내용을 인쇄 합니다 `part-r-00000` .

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* Hive 작업 제출에 대해서는 [HDInsight .NET SDK를 사용하여 Apache Hive 쿼리 실행](apache-hadoop-use-hive-dotnet-sdk.md)을 참조하세요.
* HDInsight 클러스터 만들기는 [HDInsight에서 Linux 기반 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
* HDInsight 클러스터 관리는 [HDInsight에서 Apache Hadoop 클러스터 관리](../hdinsight-administer-use-portal-linux.md)를 참조하세요.
* HDInsight .NET SDK에 대한 내용은 [HDInsight .NET SDK 참조](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)를 확인하세요.
* Azure에 대한 비대화형 인증은 [비대화형 인증 .NET HDInsight 애플리케이션 만들기](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)를 참조하세요.
