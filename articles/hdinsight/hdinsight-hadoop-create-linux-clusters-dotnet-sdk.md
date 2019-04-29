---
title: .NET를 사용하여 Apache Hadoop 클러스터 만들기 - Azure HDInsight
description: HDInsight .NET SDK를 사용하여 HDInsight용 Linux에서 Apache Hadoop, Apache HBase, Apache Storm 또는 Apache Spark 클러스터를 만드는 방법을 알아봅니다.
author: mamccrea
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: mamccrea
ms.openlocfilehash: 04760ab570ffc213950c28c22c499d8e9252d574
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124784"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-net-sdk"></a>.NET SDK를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]


.NET SDK를 사용하여 Azure HDInsight 클러스터에서 [Apache Hadoop](https://hadoop.apache.org/) 클러스터를 만드는 방법을 알아봅니다.

> [!IMPORTANT]  
> 이 문서의 단계에서는 하나의 작업자 노드가 있는 클러스터를 만듭니다. 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
>
> 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* **Azure Storage 계정** [저장소 계정 만들기](../storage/common/storage-quickstart-create-account.md)를 참조하세요.
* **Visual Studio 2013, Visual Studio 2015 또는 Visual Studio 2017**

## <a name="create-clusters"></a>클러스터 만들기

1. Visual Studio 2017을 엽니다.
2. 새 Visual C# 콘솔 애플리케이션을 만듭니다.
3. **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.
4. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

    ```powershell
    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.HDInsight
    ```

    .NET 라이브러리 및 이에 대한 참조가 현재 Visual Studio 프로젝트에 추가됩니다.
5. 솔루션 탐색기에서 **Program.cs** 를 두 번 클릭하여 열고 다음 코드를 붙여 넣은 후 변수의 값을 제공합니다.

    ```csharp
    using System;
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.HDInsight;
    using Microsoft.Azure.Management.HDInsight.Models;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    namespace CreateHDInsightCluster
    {
        class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;

            private const string SubscriptionId = "<Your Azure Subscription ID>";
            // Replace with your AAD tenant ID if necessary
            private const string TenantId = UserTokenProvider.CommonTenantId; 
            // This is the GUID for the PowerShell client. Used for interactive logins in this example.
            private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

            private const string ExistingResourceGroupName = "<Enter Resource Group Name>";
            private const string ExistingStorageName = "<Enter Default Storage Account Name>.blob.core.windows.net";
            private const string ExistingStorageKey = "<Enter Default Storage Account Key>";
            private const string ExistingBlobContainer = "<Enter Default Bob Container Name>";

            private const string NewClusterName = "<Enter HDInsight Cluster Name>";
            private const int NewClusterNumNodes = 2;
            private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
            private const OSType NewClusterOSType = OSType.Linux;
            private const string NewClusterType = "Hadoop";
            private const string NewClusterVersion = "3.6";
            private const string NewClusterUsername = "admin";
            private const string NewClusterPassword = "<Enter HTTP User Password>";
            private const string NewClusterSshUserName = "sshuser";

            // You can use eitehr password or public key. See https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix
            private const string NewClusterSshPassword = "<Enter SSH User Password>";
            private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
                Comment: ""rsa-key-20150731""
                AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
                gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
                yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
                WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
                pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
                zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
                ---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

            static void Main(string[] args)
            {
                System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

                // Authenticate and get a token
                var authToken = GetTokenCloudCredentials(TenantId, ClientId, SubscriptionId);
                // Flag subscription for HDInsight, if it isn't already.
                EnableHDInsight(authToken);
                // Get an HDInsight management client
                _hdiManagementClient = new HDInsightManagementClient(authToken);

                // Set parameters for the new cluster
                var parameters = new ClusterCreateParameters
                {
                    ClusterSizeInNodes = NewClusterNumNodes,
                    UserName = NewClusterUsername,
                    ClusterType = NewClusterType,
                    OSType = NewClusterOSType,
                    Version = NewClusterVersion,

                    // Use an Azure storage account as the default storage
                    DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingBlobContainer),

                    // Is the cluster type RServer? If so, you can set the EdgeNodeSize.
                    // Otherwise, the default VM size is used.
                    //EdgeNodeSize = "Standard_D12_v2",

                    Password = NewClusterPassword,
                    Location = NewClusterLocation,

                    SshUserName = NewClusterSshUserName,
                    SshPassword = NewClusterSshPassword,
                    //SshPublicKey = NewClusterSshPublicKey
                };

                // Is the cluster type RServer? If so, add the RStudio configuration option.
                /*
                parameters.Configurations.Add(
                    "rserver",
                    new Dictionary<string, string>()
                    {
                        { "rstudio", "true" }
                    }
                );
                */

                // Create the cluster
                _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

                System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            /// <summary>
            /// Authenticate to an Azure subscription and retrieve an authentication token
            /// </summary>
            static TokenCloudCredentials GetTokenCloudCredentials(string TenantId, string ClientId, string SubscriptionId)
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
                var tokenAuthResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
                    ClientId, 
                    new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                    new PlatformParameters(PromptBehavior.Always), 
                    UserIdentifier.AnyUser);
                return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.Result.AccessToken);
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

6. 클래스 멤버 값을 대체합니다.
7. **F5** 키를 눌러 애플리케이션을 실행합니다. 콘솔 창이 열리고 애플리케이션의 상태가 표시되며 또한 Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. 일반적으로 15분 정도입니다.

## <a name="use-bootstrap"></a>부트스트랩 사용

부트스트랩을 사용하여 클러스터를 만드는 중에 추가 설정을 구성할 수 있습니다.  자세한 내용은 [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.

[클러스터 만들기](#create-clusters)에서 샘플을 수정하여 Apache Hive 설정을 구성합니다.

```csharp
static void Main(string[] args)
{
    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

    // Authenticate and get a token
    var authToken = GetTokenCloudCredentials(TenantId, ClientId, SubscriptionId);
    // Flag subscription for HDInsight, if it isn't already.
    EnableHDInsight(authToken);
    // Get an HDInsight management client
    _hdiManagementClient = new HDInsightManagementClient(authToken);

    // Set parameters for the new cluster
    var extendedParameters = new ClusterCreateParametersExtended
    {
        Location = NewClusterLocation,
        Properties = new ClusterCreateProperties
        {
            ClusterDefinition = new ClusterDefinition
            {
                ClusterType = NewClusterType.ToString()
            },
            ClusterVersion = NewClusterVersion,
            OperatingSystemType = NewClusterOSType
        }
    };

    var coreConfigs = new Dictionary<string, string>
    {
        {"fs.defaultFS", string.Format("wasb://{0}@{1}", ExistingBlobContainer, ExistingStorageName)},
        {
            string.Format("fs.azure.account.key.{0}", ExistingStorageName),
            ExistingStorageKey
        }
    };

    // bootstrap
    var hiveConfigs = new Dictionary<string, string>
    {
        { "hive.metastore.client.socket.timeout", "90"}
    };

    var gatewayConfigs = new Dictionary<string, string>
    {
        {"restAuthCredential.isEnabled", "true"},
        {"restAuthCredential.username", NewClusterUsername},
        {"restAuthCredential.password", NewClusterPassword}
    };

    var configurations = new Dictionary<string, Dictionary<string, string>>
    {
        {"core-site", coreConfigs},
        {"gateway", gatewayConfigs},
        {"hive-site", hiveConfigs}
    };

    var serializedConfig = JsonConvert.SerializeObject(configurations);
    extendedParameters.Properties.ClusterDefinition.Configurations = serializedConfig;

    var sshPublicKeys = new List<SshPublicKey>();
    var sshPublicKey = new SshPublicKey
    {
        CertificateData =
            string.Format("ssh-rsa {0}", NewClusterSshPublicKey)
    };
    sshPublicKeys.Add(sshPublicKey);

    var headNode = new Role
    {
        Name = "headnode",
        TargetInstanceCount = 2,
        HardwareProfile = new HardwareProfile
        {
            VmSize = "Large"
        },
        OsProfile = new OsProfile
        {
            LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
            {
                UserName = NewClusterSshUserName,
                Password = NewClusterSshPassword //,
                // When use a SSH pulbic key, make sure to remove comments, headers and trailers, and concatenate the key into one line 
                //SshProfile = new SshProfile
                //{
                //    SshPublicKeys = sshPublicKeys
                //}
            }
        }
    };

    var workerNode = new Role
    {
        Name = "workernode",
        TargetInstanceCount = NewClusterNumNodes,
        HardwareProfile = new HardwareProfile
        {
            VmSize = "Large"
        },
        OsProfile = new OsProfile
        {
            LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
            {
                UserName = NewClusterSshUserName,
                Password = NewClusterSshPassword //,
                //SshProfile = new SshProfile
                //{
                //    SshPublicKeys = sshPublicKeys
                //}
            }
        }
    };

    extendedParameters.Properties.ComputeProfile = new ComputeProfile();
    extendedParameters.Properties.ComputeProfile.Roles.Add(headNode);
    extendedParameters.Properties.ComputeProfile.Roles.Add(workerNode);

    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, extendedParameters);

    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
    System.Console.ReadLine();
}
```

## <a name="use-script-action"></a>스크립트 동작 사용

스크립트 동작을 사용하여 클러스터를 만드는 중에 추가 설정을 구성할 수 있습니다.  자세한 내용은 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

[클러스터 만들기](#create-clusters) 에서 샘플을 수정하여 R:를 설치하는 스크립트 작업을 호출합니다.

```csharp
static void Main(string[] args)
{
    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

    // Authenticate and get a token
    var authToken = GetTokenCloudCredentials(TenantId, ClientId, SubscriptionId);
    // Flag subscription for HDInsight, if it isn't already.
    EnableHDInsight(authToken);
    // Get an HDInsight management client
    _hdiManagementClient = new HDInsightManagementClient(authToken);

    // Set parameters for the new cluster
    var parameters = new ClusterCreateParameters
    {
        ClusterSizeInNodes = NewClusterNumNodes,
        Location = NewClusterLocation,
        ClusterType = NewClusterType,
        OSType = NewClusterOSType,
        Version = NewClusterVersion,

        DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingBlobContainer),

        UserName = NewClusterUsername,
        Password = NewClusterPassword,
        SshUserName = NewClusterSshUserName,
        SshPublicKey = NewClusterSshPublicKey
    };

    ScriptAction rScriptAction = new ScriptAction("Install R",
        new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

    parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
    parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });

    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
    System.Console.ReadLine();
}
```

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](hdinsight-hadoop-create-linux-clusters-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
HDInsight 클러스터를 성공적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다. 

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 클러스터
* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hadoop/hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 클러스터
* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 Apache HBase용 Java 애플리케이션 개발](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 클러스터
* [HDInsight에서 Apache Storm용 Java 토폴로지 개발](storm/apache-storm-develop-java-topology.md)
* [HDInsight의 Apache Storm에서 Python 구성 요소 사용](storm/apache-storm-develop-python-topology.md)
* [HDInsight에서 Apache Storm을 사용하는 토폴로지 배포 및 모니터링](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 클러스터
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](spark/apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](spark/apache-spark-livy-rest-interface.md)
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](spark/apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="run-jobs"></a>작업 실행
* [.NET SDK를 사용하여 HDInsight에서 Apache Hive 작업 실행](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [.NET SDK를 사용하여 HDInsight에서 Apache Pig 작업 실행](hadoop/apache-hadoop-use-pig-dotnet-sdk.md)
* [.NET SDK를 사용하여 HDInsight에서 Apache Sqoop 작업 실행](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md)
* [HDInsight에서 Apache Oozie 작업 실행](hdinsight-use-oozie-linux-mac.md)

