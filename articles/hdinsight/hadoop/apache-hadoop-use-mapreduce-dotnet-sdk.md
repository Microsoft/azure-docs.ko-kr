---
title: HDInsight .NET SDK를 사용하여 MapReduce 작업 제출 - Azure
description: HDInsight .NET SDK를 사용하여 Azure HDInsight Apache Hadoop에 MapReduce 작업을 제출하는 방법에 대해 알아봅니다.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 1ac2dda20ba1219c9f62e834b5cd2cfba8a50086
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124698"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK를 사용하여 MapReduce 작업 실행
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

HDInsight .NET SDK를 사용하여 MapReduce 작업을 제출하는 방법을 알아봅니다. HDInsight 클러스터에는 여러 MapReduce 샘플이 담긴 jar 파일이 포함되어 있습니다. 이 jar 파일은 */example/jars/hadoop-mapreduce-examples.jar*입니다.  샘플 중 하나는 *wordcount*입니다. C# 콘솔 애플리케이션을 개발하여 단어 세기 작업을 제출합니다.  이 작업은 */example/data/gutenberg/davinci.txt* 파일을 읽고 결과를 */example/data/davinciwordcount*에 출력합니다.  애플리케이션을 다시 실행하려면 출력 폴더를 정리해야 합니다.

> [!NOTE]  
> 이 문서의 단계는 Windows 클라이언트에서 수행되어야 합니다. Hive와 함께 작동하도록 Linux, OS X 또는 Unix 클라이언트를 사용하는 방법에 대한 정보를 보려면 문서 맨 위에 표시된 탭 선택기를 사용합니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* **HDInsight에 Hadoop 클러스터**. [HDInsight에서 Linux 기반 Apache Hadoop 사용 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.
* **Visual Studio 2013/2015/2017**

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK를 사용하여 MapReduce 작업 제출
HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 

**작업을 제출하려면**

1. Visual Studio를 사용하여 C# 콘솔 애플리케이션을 만듭니다.
2. NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. 다음 코드를 사용합니다.

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
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

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

4. **F5** 키를 눌러 애플리케이션을 실행합니다.

작업을 다시 실행하려면 작업 출력 폴더 이름을 변경해야 합니다. 이 샘플에서는 "/example/data/davinciwordcount"입니다.

작업이 성공적으로 완료되면 애플리케이션은 출력 파일의 내용 "part-r-00000"을 출력합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* Hive 작업 제출에 대해서는 [HDInsight .NET SDK를 사용하여 Apache Hive 쿼리 실행](apache-hadoop-use-hive-dotnet-sdk.md)을 참조하세요.
* HDInsight 클러스터 만들기는 [HDInsight에서 Linux 기반 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
* HDInsight 클러스터 관리는 [HDInsight에서 Apache Hadoop 클러스터 관리](../hdinsight-administer-use-portal-linux.md)를 참조하세요.
* HDInsight .NET SDK에 대한 내용은 [HDInsight .NET SDK 참조](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)를 확인하세요.
* Azure에 대한 비대화형 인증은 [비대화형 인증 .NET HDInsight 애플리케이션 만들기](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)를 참조하세요.

