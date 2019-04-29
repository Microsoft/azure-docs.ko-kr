---
title: HDInsight용 Azure Resource Manager 도구에 마이그레이션
description: HDInsight 클러스터용 Azure Resource Manager 개발 도구에 마이그레이션하는 방법
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: a3bbee9324b963bac1bb01271051011fdc9bc00b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124750"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>HDInsight 클러스터용 Azure Resource Manager 기반 개발 도구에 마이그레이션

HDInsight에서는 HDInsight용 ASM(Azure 서비스 관리자) 기반 도구를 지원하지 않습니다. Azure PowerShell, Azure 클래식 CLI 또는 HDInsight .NET SDK를 사용하여 HDInsight 클러스터와 함께 작업한 경우 앞으로 Azure Resource Manager 버전의 PowerShell, CLI 및 .NET SDK를 사용하는 것이 좋습니다. 이 문서는 새 Resource Manager 기반 접근 방법에 마이그레이션하는 방법에 대한 포인터를 제공합니다. 해당되는 경우 이 문서에서는 HDInsight에 대한 ASM 및 Resource Manager 접근 방법 간의 차이점을 강조합니다.

> [!IMPORTANT]  
> ASM 기반 PowerShell, CLI 및 .NET SDK에 대한 지원은 **2017년 1월 1일**에 중단됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Azure Resource Manager로 Azure 클래식 CLI 마이그레이션

> [!IMPORTANT]  
> Azure CLI에서는 HDInsight 클러스터 작업을 위한 지원을 제공하지 않습니다. 여전히 HDInsight와 함께 Azure 클래식 CLI를 사용할 수 있지만 Azure 클래식 CLI는 사용되지 않습니다.

다음은 Azure 클래식 CLI를 통해 HDInsight를 사용하기 위한 기본 명령입니다.

* `azure hdinsight cluster create` - 새 HDInsight 클러스터를 만듭니다.
* `azure hdinsight cluster delete` - 새 HDInsight 클러스터를 삭제합니다.
* `azure hdinsight cluster show` - 기존 클러스터에 대한 정보를 표시합니다.
* `azure hdinsight cluster list` - Azure 구독에 대한 HDInsight 클러스터를 나열합니다.

`-h` 을 사용하여 각 명령에 사용할 수 있는 매개 변수와 스위치를 검사합니다.

### <a name="new-commands"></a>새 명령
Azure Resource Manager로 사용할 수 있는 새 명령은 다음과 같습니다.

* `azure hdinsight cluster resize` - 클러스터에서 작업자 노드 수를 동적으로 변경합니다.
* `azure hdinsight cluster enable-http-access` - 클러스터에 대한 HTTPs 액세스를 사용합니다(기본적으로).
* `azure hdinsight cluster disable-http-access` - 클러스터에 대한 HTTPs 액세스를 사용하지 않습니다.
* `azure hdinsight script-action` - 클러스터에서 스크립트 작업을 만들기/관리하기 위한 명령을 제공합니다.
* `azure hdinsight config` - `hdinsight cluster create` 명령으로 사용할 수 있는 구성 파일을 만들기 위한 명령을 제공하여 구성 정보를 제공합니다.

### <a name="deprecated-commands"></a>사용되지 않는 명령
`azure hdinsight job` 명령을 사용하여 HDInsight 클러스터에 작업을 제출하는 경우 이러한 명령은 Resource Manager 명령을 통해 사용할 수 없습니다. 프로그래밍 방식으로 스크립트에서 HDInsight로 작업을 제출해야 하는 경우 HDInsight에서 제공하는 REST API를 대신 사용해야 합니다. REST API를 사용하여 작업을 제출하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [cURL을 사용하여 HDInsight에서 Hadoop과 MapReduce 작업 실행](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [cURL을 사용하여 HDInsight에서 Apache Hadoop과 함께 Apache Hive 쿼리 실행](hadoop/apache-hadoop-use-hive-curl.md)
* [cURL을 사용하여 HDInsight에서 Apache Hadoop과 함께 Apache Pig 작업 실행](hadoop/apache-hadoop-use-pig-curl.md)

Apache Hadoop MapReduce, Apache Hive 및 Apache Pig를 대화형으로 실행하는 다른 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop과 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md), [HDInsight에서 Hadoop과 Apache Hive 사용](hadoop/hdinsight-use-hive.md) 및 [HDInsight에서 Apache Hadoop과 Apache Pig 사용](hadoop/hdinsight-use-pig.md)을 참조하세요.

### <a name="examples"></a>예
**클러스터 만들기**

* 이전 명령(ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* 새 명령 - `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**클러스터 삭제**

* 이전 명령(ASM) - `azure hdinsight cluster delete myhdicluster`
* 새 명령 - `azure hdinsight cluster delete mycluster -g myresourcegroup`

**클러스터 나열**

* 이전 명령(ASM) - `azure hdinsight cluster list`
* 새 명령 - `azure hdinsight cluster list`

> [!NOTE]  
> 나열 명령의 경우 `-g`을 사용하여 리소스 그룹을 지정하면 지정된 리소스 그룹에 클러스터만을 반환합니다.

**클러스터 정보 표시**

* 이전 명령(ASM) - `azure hdinsight cluster show myhdicluster`
* 새 명령 - `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Azure Resource Manager로 Azure PowerShell 마이그레이션
[Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)에서 Azure Resource Manager 모드인 Azure PowerShell에 대한 일반 정보를 찾을 수 있습니다.

Azure PowerShell Resource Manager cmdlet은 ASM cmdlet과 나란히 설치될 수 있습니다. 두 가지 모드에서 cmdlet을 이름으로 구분할 수 있습니다.  Resource Manager 모드가 *AzHDInsight* 비교할 cmdlet 이름의 *AzureHDInsight* ASM 모드에서.  예를 들어 *새로 만들기-AzHDInsightCluster* vs. *New-AzureHDInsightCluster*가 있습니다. 매개 변수 및 스위치에는 새 이름이 있을 수 있고 Resource Manager를 사용하는 경우 많은 새 매개 변수를 사용할 수 있습니다.  예를 들어 일부 cmdlet에는 *-ResourceGroupName*이라는 새 스위치가 필요합니다. 

HDInsight cmdlet를 사용하기 전에 Azure 계정에 연결하고 새 리소스 그룹을 만들어야 합니다.

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>이름이 바뀐 cmdlet
Windows PowerShell 콘솔에서 HDInsight ASM cmdlet을 나열하려면:

    help *azurehdinsight*

다음 테이블에서 ASM cmdlet 및 Resource Manager 모드인 해당 이름을 나열합니다.

| ASM cmdlet | Resource Manager cmdlet |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzHDInsightConfigValues](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalues) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperties |[Get-AzHDInsightProperties](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperties) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>새 cmdlet
Resource Manager 모드에서만 사용할 수 있는 새 cmdlet은 다음과 같습니다. 

**스크립트 작업 관련 cmdlet:**

* **Get-AzHDInsightPersistedScriptAction**: 클러스터에 대한 지속형 스크립트 동작을 가져와 시간 순서로 나열하거나 지정된 지속형 스크립트 동작에 대한 세부 정보를 가져옵니다. 
* **Get-AzHDInsightScriptActionHistory**: 클러스터에 대한 스크립트 동작 기록을 가져와 시간 순서로 나열하거나 이전에 실행된 스크립트 동작의 세부 정보를 가져옵니다. 
* **Remove-AzHDInsightPersistedScriptAction**: HDInsight 클러스터에서 지속형 스크립트 동작을 제거합니다.
* **Set-AzHDInsightPersistedScriptAction**: 이전에 실행된 스크립트 동작을 지속형 스크립트 동작으로 설정합니다.
* **Submit-AzHDInsightScriptAction**: Azure HDInsight 클러스터에 새 스크립트 동작을 제출합니다. 

추가 사용 정보는 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

**클러스터 ID 관련 cmdlet:**

* **Add-AzHDInsightClusterIdentity**: HDInsight 클러스터가 Azure Data Lake Storage에 액세스할 수 있도록 클러스터 구성 개체에 클러스터 ID를 추가합니다. [Azure PowerShell을 사용하여 Data Lake Storage로 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)를 참조하세요.

### <a name="examples"></a>예
**클러스터 만들기**

이전 명령(ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

새 명령:

    New-AzHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**클러스터 삭제**

이전 명령(ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

새 명령:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**클러스터 나열**

이전 명령(ASM):

    Get-AzureHDInsightCluster

새 명령:

    Get-AzHDInsightCluster 

**클러스터 표시**

이전 명령(ASM):

    Get-AzureHDInsightCluster -Name $clusterName

새 명령:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>다른 샘플
* [HDInsight 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Apache Hive 작업 제출](hadoop/apache-hadoop-use-hive-powershell.md)
* [Apache Pig 작업 제출](hadoop/apache-hadoop-use-pig-powershell.md)
* [Apache Sqoop 작업 제출](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>새 HDInsight .NET SDK로 마이그레이션
Azure 서비스 관리 기반 [(ASM) HDInsight.NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) 는 이제 사용되지 않습니다. Azure 리소스 관리 기반 [Resource Manager 기반 HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)를 사용하는 것이 좋습니다. 다음 ASM 기반 HDInsight 패키지는 더 이상 사용되지 않습니다.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

이 섹션에서는 Resource Manager 기반 SDK를 사용하여 특정 작업을 수행하는 방법에 대한 자세한 정보의 포인터를 제공합니다.

| 방법... Resource Manager 기반 HDInsight SDK 사용 | 링크 |
| --- | --- |
| .NET SDK를 사용하여 HDInsight 클러스터 만들기 | [.NET SDK를 사용하여 HDInsight 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| .NET SDK와 스크립트 작업을 사용하여 클러스터 사용자 지정 | [스크립트 작업을 사용하여 HDInsight Linux 클러스터 사용자 지정](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| .NET SDK와 Azure Active Directory를 사용하여 대화형으로 애플리케이션 인증 |[.NET SDK를 사용하여 Apache Hive 쿼리 실행](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)을 참조하세요. 이 문서의 코드 조각에서는 대화형 인증 접근 방법을 사용합니다. |
| .NET SDK와 Azure Active Directory를 사용하여 비대화형으로 애플리케이션 인증 |[HDInsight에 대한 비대화형 애플리케이션 만들기](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| .NET SDK를 사용하여 Apache Hive 작업 제출 |[Apache Hive 작업 제출](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) 참조 |
| .NET SDK를 사용하여 Apache Pig 작업 제출 |[Apache Pig 작업 제출](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) 참조 |
| .NET SDK를 사용하여 Apache Sqoop 작업 제출 |[Apache Sqoop 작업 제출](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) 참조 |
| .NET SDK를 사용하여 HDInsight 클러스터 나열 | [HDInsight 클러스터 나열](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| .NET SDK를 사용하여 HDInsight 클러스터 크기 조정 | [HDInsight 클러스터 크기 조정](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| .NET SDK를 사용하여 HDInsight 클러스터에 대한 액세스 권한 부여/해지 | [HDInsight 클러스터에 대한 액세스 권한 부여/해지](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| .NET SDK를 사용하여 HDInsight 클러스터에 대한 HTTP 사용자 자격 증명 업데이트 | [HDInsight 클러스터에 대한 HTTP 사용자 자격 증명 업데이트](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| .NET SDK를 사용하여 HDInsight 클러스터에 대한 기본 저장소 계정 찾기 | [HDInsight 클러스터에 대한 기본 저장소 계정 찾기](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| .NET SDK를 사용하여 HDInsight 클러스터 삭제 | [HDInsight 클러스터 삭제](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>예
다음은 ASM 기반 SDK 및 Resource Manager 기반 SDK에 해당하는 코드 조각을 사용하여 작업을 수행하는 방법에 대한 일부 예제입니다.

**클러스터 CRUD 클라이언트 만들기**

* 이전 명령(ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* 새 명령(서비스 사용자 권한 부여)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* 새 명령(사용자 권한 부여)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**클러스터 만들기**

* 이전 명령(ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* 새 명령
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**HTTP 액세스 사용**

* 이전 명령(ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* 새 명령
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**클러스터 삭제**

* 이전 명령(ASM)
  
        client.DeleteCluster(dnsName);
* 새 명령
  
        client.Clusters.Delete(resourceGroup, dnsname);

