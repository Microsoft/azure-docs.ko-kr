---
title: .NET 및 HDInsight를 사용하여 Apache Sqoop 작업 실행 - Azure
description: HDInsight .NET SDK를 사용하여 Apache Hadoop 클러스터와 Azure SQL 데이터베이스 간에 Apache Sqoop 가져오기 및 내보내기를 실행하는 방법을 알아봅니다.
keywords: sqoop 작업
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
origin.date: 05/16/2018
ms.date: 04/29/2019
ms.author: v-yiso
ms.openlocfilehash: ac0890be0abccb316bffc4d9bdd6868a80173e18
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128994"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>HDInsight에서 Apache Hadoop에 대해 .NET SDK를 사용하여 Apache Sqoop 작업 실행
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Azure HDInsight .NET SDK를 사용하여 HDInsight 클러스터와 Azure SQL 데이터베이스 또는 SQL Server 데이터베이스 간에 가져오고 내보내는 HDInsight에서 Apache Sqoop 작업을 실행하는 방법을 알아봅니다.

> [!NOTE]
> 이 문서의 절차는 Windows 기반 또는 Linux 기반 HDInsight 클러스터에서 사용할 수 있지만, Windows 클라이언트에서만 작동합니다. 다른 방법을 선택하려면 이 문서의 위쪽에 있는 탭 선택기를 사용하세요.
> 

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음 항목이 있어야 합니다.

* HDInsight의 Apache Hadoop 클러스터. 자세한 내용은 [클러스터 및 SQL 데이터베이스 만들기](hdinsight-use-sqoop.md#create-cluster-and-sql-database)를 참조하세요.

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>.NET SDK를 사용하여 HDInsight 클러스터에서 Sqoop 사용
HDInsight .NET SDK는 .NET 클라이언트 라이브러리를 제공하므로 .NET에서 HDInsight 클러스터 작업을 더 쉽게 수행할 수 있습니다. 이 섹션에서는 C# 콘솔 애플리케이션을 만들어 이 자습서의 앞 부분에서 만든 SQL Database 테이블에 hivesampletable을 내보냅니다.

## <a name="submit-a-sqoop-job"></a>Sqoop 작업 제출

1. Visual Studio를 사용하여 C# 콘솔 애플리케이션을 만듭니다.

2. Visual Studio 패키지 관리자 콘솔에서 다음 NuGet 명령을 실행하여 패키지를 가져옵니다.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Program.cs 파일에서 다음 코드를 사용합니다.

        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.cn";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitSqoopJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";

                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";

                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.chinacloudapi.cn;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;

                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };

                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. 프로그램을 실행하려면 **F5** 키를 선택합니다. 

## <a name="limitations"></a>제한 사항
Linux 기반 HDInsight에에 대한 제한 사항은 다음과 같습니다.

* 대량 내보내기: Microsoft SQL Server 또는 Azure SQL Database에 데이터를 내보내는 데 사용되는 Sqoop 커넥터는 현재 대량 삽입을 지원하지 않습니다.

* 일괄 처리: `-batch` 스위치를 사용하면 Sqoop에서 삽입 작업을 일괄 처리하는 대신 여러 번의 삽입 작업을 수행합니다.

## <a name="next-steps"></a>다음 단계
이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight에서 Apache Oozie 사용](../hdinsight-use-oozie-linux-mac.md): Oozie 워크플로에서 Sqoop 작업을 사용합니다.
* [HDInsight에 데이터 업로드](../hdinsight-upload-data.md): HDInsight 또는 Azure Blob 스토리지에 데이터를 업로드하는 다른 방법을 찾습니다.

