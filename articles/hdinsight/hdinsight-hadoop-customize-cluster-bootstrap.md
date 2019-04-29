---
title: 부트스트랩을 사용 하 여 Azure HDInsight 클러스터 구성을 사용자 지정
description: 프로그래밍 방식으로.Net, PowerShell 및 Resource Manager를 사용 하 여 HDInsight 클러스터 구성을 사용자 지정 하는 방법을 알아봅니다 템플릿.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: b1bc0a68a9cf52e886c0664a474a4dbb75126698
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735966"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정

부트스트랩 스크립트를 사용 하면 설치 하 고 Azure HDInsight에서 구성 요소를 프로그래밍 방식으로 구성할 수 있습니다. 

HDInsight 클러스터가 만들어질 때 구성 파일 설정을 설정 하는 방법은 세 가지가 있습니다.

* Azure PowerShell 사용
* .NET SDK 사용
* Azure Resource Manager 템플릿 사용

예를 들어 프로그래밍 방식으로 이러한 메서드를 사용 하면 옵션을 구성할 수 이러한 파일에:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties(kafka-broker 구성)

만든 시간 동안 HDInsight 클러스터에서 추가 구성 요소 설치에 대 한 내용은 참조 하세요 [사용자 지정 HDInsight 스크립트 동작 (Linux)를 사용 하 여 클러스터](hdinsight-hadoop-customize-cluster-linux.md)합니다.

## <a name="prerequisites"></a>필수 조건

* PowerShell을 사용 하는 경우는 [Az 모듈](https://docs.microsoft.com/powershell/azure/overview)합니다.

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

다음 PowerShell 코드는 [Apache Hive](https://hive.apache.org/) 구성을 사용자 지정합니다.

> [!IMPORTANT]  
> 매개 변수 `Spark2Defaults` 사용 해야 할 수 있습니다 [추가-AzHDInsightConfigValues](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalues)합니다. 아래 코드 예제에 표시 된 대로 매개 변수에 빈 값을 전달할 수 있습니다.


```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValues `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

완전히 작동하는 PowerShell 스크립트는 [부록](#appendix-powershell-sample)에서 찾을 수 있습니다.

**변경을 확인하려면:**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터**를 클릭합니다. 표시되지 않으면 먼저 **모든 서비스**를 클릭합니다.
3. PowerShell 스크립트를 사용하여 방금 만든 클러스터를 클릭합니다.
4. 블레이드 맨 위에서 **대시보드** 를 클릭하여 Ambari UI를 엽니다.
5. 왼쪽 메뉴에서 **Hive** 를 클릭합니다.
6. **요약**에서 **HiveServer2**를 클릭합니다.
7. **Configs** 탭을 클릭합니다.
8. 왼쪽 메뉴에서 **Hive** 를 클릭합니다.
9. **고급** 탭을 클릭합니다.
10. 아래로 스크롤한 다음 **고급 hive 사이트**를 확장합니다.
11. 섹션에서 **hive.metastore.client.socket.timeout** 을 찾습니다.

다른 구성 파일을 사용자 지정하는 추가 샘플:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>.NET SDK 사용
[.NET SDK를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap)를 참조하세요.

## <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용
Resource Manager 템플릿에서 부트스트랩을 사용할 수 있습니다.

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop 사용자 지정 클러스터 부트스트랩 Azure Resource Manager 템플릿](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>참고 항목
* [HDInsight의 Apache Hadoop 클러스터 만들기][hdinsight-provision-cluster]에서는 다른 사용자 지정 옵션을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 지침을 제공합니다.
* [HDInsight용 스크립트 작업 스크립트 개발][hdinsight-write-script]
* [HDInsight 클러스터에서 Apache Spark 설치 및 사용][hdinsight-install-spark]
* [HDInsight 클러스터에 Apache Giraph 설치 및 사용](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "클러스터를 만드는 동안의 단계"

## <a name="appendix-powershell-sample"></a>부록: PowerShell 샘플

이 PowerShell 스크립트는 HDInsight 클러스터를 만들고 Hive 설정을 사용자 지정 합니다. 에 대 한 값을 입력 해야 `$nameToken`, `$httpPassword`, 및 `$sshPassword`합니다.

> [!IMPORTANT]  
> 에 대 한 값 `DefaultStorageAccount`, 및 `DefaultStorageContainer` 에서 반환 되지 않습니다 [Get AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) 때 [보안 전송](../storage/common/storage-require-secure-transfer.md) 저장소 계정에서 사용 하도록 설정 됩니다.

> [!WARNING]  
> 저장소 계정 종류 `BlobStorage` HDInsight 클러스터에 사용할 수 없습니다.


```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>' 

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>' 
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValues `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
