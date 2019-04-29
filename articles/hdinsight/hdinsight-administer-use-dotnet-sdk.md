---
title: .NET SDK를 사용하여 HDInsight의 Apache Hadoop 클러스터 관리 - Azure
description: HDInsight .NET SDK를 사용하여 HDInsight의 Apache Hadoop 클러스터에 대해 관리 작업을 수행하는 방법에 대해 알아봅니다.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 06e3178e344ee46f67cfd8a6feaf08d56d3c86e7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096218"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>.NET SDK를 사용하여 HDInsight의 Apache Hadoop 클러스터 관리

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)를 사용하여 HDInsight 클러스터를 관리하는 방법을 알아봅니다.

**필수 구성 요소**

이 문서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

## <a name="connect-to-azure-hdinsight"></a>Azure HDInsight에 연결

다음 NuGet 패키지가 필요합니다.

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

다음 코드 샘플은 Azure 구독에서 HDInsight 클러스터를 관리할 수 있기 전에 Azure에 연결하는 방법을 보여 줍니다.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

이 프로그램을 실행하면 프롬프트가 나타납니다.  프롬프트를 표시하지 않으려면 [비대화형 인증 .NET HDInsight 애플리케이션 만들기](hdinsight-create-non-interactive-authentication-dotnet-applications.md)를 참조하세요.

## <a name="create-clusters"></a>클러스터 만들기

 [.NET SDK를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>클러스터 나열

다음 코드 조각은 클러스터 및 일부 속성을 나열합니다.

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>클러스터 삭제

다음 코드 조각을 사용하여 동기적 또는 비동기적으로 클러스터를 삭제합니다. 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>클러스터 크기 조정

클러스터 크기 조정 기능을 사용하여 클러스터를 다시 생성하지 않고 Azure HDInsight에서 실행되는 클러스터에서 사용되는 작업자 노드 수를 변경합니다.

> [!NOTE]  
> HDInsight 버전 3.1.3 이상을 사용하는 클러스터만 지원됩니다. 클러스터 버전을 알 수 없는 경우 속성 페이지를 확인할 수 있습니다.  [클러스터 나열 및 표시](hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요.

HDInsight에서 지원되는 클러스터의 각 형식에 대한 데이터 노드 수를 변경하는 영향은 다음과 같습니다.

* Apache Hadoop
  
    모든 보류 중인 또는 실행 중인 작업에 영향을 주지 않고 실행되는 Hadoop 클러스터의 작업자 노드 수를 원활하게 늘릴 수 있습니다. 작업이 진행 중인 동안에 새 작업을 제출할 수 있습니다. 크기 조정 작업의 오류는 정상적으로 처리되므로 클러스터는 항상 기능 상태로 남아 있습니다.
  
    데이터 노드 수를 줄여 Hadoop 클러스터를 축소하면 클러스터의 서비스 중 일부가 다시 시작됩니다. 그러면 실행 중인 작업과 보류 중인 작업이 크기 조정 작업을 완료하지 못하고 실패합니다. 그러나 작업이 완료되면 작업을 다시 제출할 수 있습니다.
* Apache HBase
  
    HBase 클러스터가 실행 중인 동안 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 지역 서버는 크기 조정 작업을 완료하는 몇 분 안에 자동으로 균형을 맞춥니다. 그러나 클러스터의 헤드 노드에 로그인한 다음 명령 프롬프트 창에서 다음 명령을 실행하여 자동으로 지역 서버의 균형을 맞출 수도 있습니다.
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    실행 중인 동안 Storm 클러스터에 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 하지만 크기 조정 작업이 성공적으로 완료되면 다시 토폴로지 균형을 조정해야 합니다.
  
    다음 두 가지 방법으로 사용하여 균형을 조정할 수 있습니다.
  
  * Storm 웹 UI
  * 명령줄 인터페이스(CLI) 도구
    
    자세한 내용은 [Apache Storm 설명서](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) (영문)을 참조하세요.
    
    Storm 웹 UI는 HDInsight 클러스터에서 제공됩니다.
    
    ![HDInsight Storm 규모 균형 재조정](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    다음은 CLI 명령을 사용하여 Storm 토폴로지 균형을 다시 조정하는 방법의 예입니다.
    

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

다음 코드 조각은 동기적 또는 비동기적으로 클러스터의 크기를 조정하는 방법을 보여 줍니다.

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>액세스 권한 부여/해지

HDInsight 클러스터에는 다음과 같은 HTTP 웹 서비스가 있습니다(이러한 모든 서비스에 RESTful 엔드포인트가 있음).

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

이러한 서비스에는 기본적으로 액세스 권한이 부여됩니다. 액세스 권한을 해지/부여할 수 있습니다. 해지하려면:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

권한하려면:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> 액세스 권한을 부여/해지하여 클러스터 사용자 이름 및 암호를 다시 설정합니다.

이 작업은 포털을 통해서도 수행할 수 있습니다. 참조 [Azure portal을 사용 하 여 HDInsight에서 Apache Hadoop 관리 클러스터](hdinsight-administer-use-portal-linux.md)합니다.

## <a name="update-http-user-credentials"></a>HTTP 사용자 자격 증명 업데이트

HTTP 액세스 권한 부여/해지와 같은 절차입니다.  클러스터에 HTTP 액세스 권한이 부여된 경우 먼저 해당 권한을 해지해야 합니다.  그런 다음 새 HTTP 사용자 자격 증명을 사용하여 액세스 권한을 부여합니다.

## <a name="find-the-default-storage-account"></a>기본 저장소 계정 찾기

다음 코드 조각에서는 클러스터에 대한 기본 저장소 계정 이름 및 기본 저장소 계정 키를 가져오는 방법을 보여 줍니다.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>작업 제출

**MapReduce 작업을 제출하려면**

[HDInsight에서 MapReduce 샘플 실행](hadoop/apache-hadoop-run-samples-linux.md)을 참조하세요.

**Apache Hive 작업을 제출하려면** 

[.NET SDK를 사용하여 Apache Hive 쿼리 실행](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)을 참조하세요.

**Apache Pig 작업을 제출하려면**

[.NET SDK를 사용하여 Apache Pig 작업 실행](hadoop/apache-hadoop-use-pig-dotnet-sdk.md)을 참조하세요.

**Apache Sqoop 작업을 제출하려면**

[HDInsight에서 Apache Sqoop 사용](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md)을 참조하세요.

**Apache Oozie 작업을 제출하려면**

[Hadoop과 함께 Apache Oozie를 사용하여 HDInsight에서 워크플로 정의 및 실행](hdinsight-use-oozie-linux-mac.md)을 참조하세요.

## <a name="upload-data-to-azure-blob-storage"></a>개발자와 관리자는 응용 프로그램을 빌드하거나 관리하는 데 필요한 유틸리티를 모든 가상 머신이나 역할 인스턴스에 다 설치하지 않고, 모든 구성 요소에서 사용할 수 있는 File Storage 공유에 저장할 수 있습니다.

[HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하세요.

## <a name="see-also"></a>관련 항목

* [HDInsight .NET SDK 참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Azure Portal을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리](hdinsight-administer-use-portal-linux.md)
* [명령줄 인터페이스를 사용하여 HDInsight 관리][hdinsight-admin-cli]
* [HDInsight 클러스터 만들기][hdinsight-provision]
* [HDInsight에 데이터 업로드][hdinsight-upload-data]
* [Azure HDInsight 시작][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
