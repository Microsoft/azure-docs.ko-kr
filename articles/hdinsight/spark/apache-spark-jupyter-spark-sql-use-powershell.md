---
title: '빠른 시작: Azure PowerShell을 사용하여 Azure HDInsight에서 Apache Spark 클러스터 만들기'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure HDInsight에서 Apache Spark 클러스터를 만들고 간단한 Spark SQL 쿼리를 실행하는 방법을 보여 줍니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: ea889f48aaf3218ad9f3f286d1cda4f8a761a7ac
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069972"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure HDInsight에서 Apache Spark 클러스터 만들기

이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure HDInsight에서 Apache Spark 클러스터를 만듭니다. 그런 다음, Jupyter Notebook을 만들고 이를 사용하여 Apache Hive 테이블에 대해 Spark SQL 쿼리를 실행합니다. Azure HDInsight는 엔터프라이즈를 위한 관리형의 전체 스펙트럼 오픈 소스 분석 서비스입니다. Azure HDInsight용 Apache Spark 프레임워크를 통해 메모리 내 처리 기능을 사용하여 데이터 분석 및 클러스터 컴퓨팅을 신속하게 처리합니다. Jupyter Notebook을 사용하면 데이터와 상호 작용하고, 코드를 markdown 텍스트와 결합하고, 간단한 시각화를 수행할 수 있습니다.

[개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter Notebook](https://jupyter.org/)

여러 클러스터를 함께 사용하는 경우 가상 네트워크를 만들고, Spark 클러스터를 사용하는 경우 Hive Warehouse Connector도 사용하는 것이 좋습니다. 자세한 내용은 [Azure HDInsight에 대한 가상 네트워크 계획](../hdinsight-plan-virtual-network-deployment.md) 및 [Hive Warehouse Connector를 사용하여 Apache Spark 및 Apache Hive 통합](../interactive-query/apache-hive-warehouse-connector.md)을 참조하세요.

## <a name="prerequisite"></a>필수 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [PowerShell Az 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>HDInsight에서 Apache Spark 클러스터 만들기

> [!IMPORTANT]  
> HDInsight 클러스터에 대한 결제는 사용 여부에 관계없이 분마다 비례 배분됩니다. 사용한 후에 클러스터를 삭제해야 합니다. 자세한 내용은 이 문서의 [리소스 정리](#clean-up-resources) 섹션을 참조하세요.

다음과 같은 Azure 개체와 리소스 만들기를 포함하는 HDInsight 클러스터를 만듭니다.

- Azure 리소스 그룹. Azure 리소스 그룹은 Azure 리소스에 대한 컨테이너입니다.
- Azure 스토리지 계정 또는 Azure Data Lake Storage.  각 HDInsight 클러스터에는 종속 데이터 스토리지가 필요합니다. 이 빠른 시작에서는 Azure Storage Blob을 클러스터 스토리지로 사용하는 클러스터를 만듭니다. Data Lake Storage Gen2를 사용하는 방법에 대한 자세한 내용은 [빠른 시작: HDInsight에서 클러스터 설정](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.
- HDInsight에서 클러스터 유형이 서로 다른 클러스터.  이 빠른 시작에서는 Spark 2.3 클러스터를 만듭니다.

PowerShell 스크립트를 사용하여 리소스를 만듭니다. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell 스크립트를 실행하는 경우 다음 값을 입력하라는 메시지가 표시됩니다.

|매개 변수|값|
|------|------|
|Azure 리소스 그룹 이름 | 리소스 그룹에 사용할 고유한 이름을 입력합니다.|
|위치| 예를 들면 ‘미국 중부’와 같은 Azure 영역을 지정합니다. |
|기본 스토리지 계정 이름 | 스토리지 계정에 고유한 이름을 제공합니다. |
|클러스터 이름 | HDInsight 클러스터의 고유한 이름을 제공합니다.|
|클러스터 로그인 자격 증명 | 빠른 시작의 뒷부분에 나오는 클러스터 대시보드에 연결하려면 이 계정을 사용합니다.|
|SSH 사용자 자격 증명 | SSH 클라이언트를 사용하여 HDInsight의 클러스터와 원격 명령줄 세션을 만들 수 있습니다.|

1. 다음 코드 블록이 [Azure Cloud Shell](../../cloud-shell/overview.md)을 열도록 오른쪽 위 모서리에서 **시도**를 선택한 다음, 지침을 수행하여 Azure에 연결합니다.

2. 다음 PowerShell 스크립트를 Cloud Shell에 복사하여 붙여넣습니다.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
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

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   클러스터를 만드는 데 약 20분이 걸립니다. 다음 세션을 계속하려면 먼저 클러스터를 만들어야 합니다.

HDInsight 클러스터를 만드는 데 문제가 발생하는 경우 이를 수행하기 위한 적절한 사용 권한이 없을 수 있습니다. 자세한 내용은 [액세스 제어 요구 사항](../hdinsight-hadoop-customize-cluster-linux.md#access-control)을 참조하세요.

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook 만들기

[Jupyter Notebook](https://jupyter.org/)은 다양한 프로그래밍 언어를 지원하는 대화형 Notebook 환경입니다. Notebook을 사용하면 데이터와 상호 작용하고, 코드를 markdown 텍스트와 결합하고, 간단한 시각화를 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **HDInsight 클러스터**를 검색하고 선택합니다.
   
   ![Azure Portal에서 HDInsight 클러스터 열기](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png)
   
1. 목록에서 만든 클러스터를 선택합니다.
   
   ![Azure Portal에서 HDInsight 클러스터 열기](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png)
   
1. 클러스터 **개요**  페이지에서 **클러스터 대시보드**를 선택한 다음, **Jupyter Notebook**을 선택합니다. 메시지가 표시되면 클러스터에 대한 클러스터 로그인 자격 증명을 입력합니다.

   ![대화형 Spark SQL 쿼리 실행을 위해 Jupyter Notebook 열기](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook을 열어 대화형 Spark SQL 쿼리 실행")

1. **새로 만들기** > **PySpark**를 선택하여 Notebook을 만듭니다.

   ![대화형 Spark SQL 쿼리 실행을 위해 Jupyter Notebook 만들기](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "대화형 Spark SQL 쿼리 실행을 위해 Jupyter Notebook 만들기")

   새 Notebook이 만들어지고 Untitled(Untitled.pynb) 이름으로 열립니다.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL 문 실행

SQL(구조적 쿼리 언어)은 데이터 쿼리 및 정의에 가장 일반적이며 널리 사용되는 언어입니다. Spark SQL은 익숙한 SQL 구문을 사용하여 구조화된 데이터를 처리하기 위한 Apache Spark에 대한 확장으로 작동합니다.

1. 커널이 준비되었는지 확인합니다. Notebook의 커널 이름 옆에 속이 빈 원이 보이면 커널이 준비된 것입니다. 속이 찬 원은 커널이 사용 중이라는 뜻입니다.

    ![커널 상태](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "커널 상태")

    Notebook을 처음으로 시작하면 커널이 백그라운드에서 몇 가지 작업을 수행합니다. 커널이 준비될 때까지 기다립니다. 
1. 빈 셀에 다음 코드를 붙여 넣은 다음, **SHIFT + ENTER**를 눌러 코드를 실행합니다. 이 명령은 클러스터의 Hive 테이블을 나열합니다.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    HDInsight에서 클러스터와 함께 Jupyter Notebook을 사용하면 Spark SQL을 사용하여 Hive 쿼리를 실행하는 데 사용할 수 있는 미리 설정된 `sqlContext`를 얻게 됩니다. `%%sql`은 Jupyter Notebook에 미리 설정된 `sqlContext`를 사용하여 Hive 쿼리를 실행하도록 지시합니다. 쿼리는 기본적으로 모든 HDInsight 클러스터와 함께 제공되는 Hive 테이블(**hivesampletable**)에서 상위 10개의 행을 검색합니다. 결과를 얻는데 약 30초가 걸립니다. 출력은 다음과 같이 표시됩니다.

    ![HDInsight의 Spark에서 Apache Hive 쿼리](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png "HDInsight Spark의 Apache Hive 쿼리")

    Jupyter에서 쿼리를 실행할 때마다, 웹 브라우저 창 제목에 Notebook 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다.

1. 또 다른 쿼리를 실행하여 `hivesampletable`의 데이터를 봅니다.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    쿼리 출력이 표시되도록 화면이 새로 고쳐집니다.

    ![HDInsight의 Hive 쿼리 출력](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png "HDInsight의 Hive 쿼리 출력")

1. Notebook의 **파일** 메뉴에서 **닫기 및 중지**를 선택합니다. Notebook을 종료하면 클러스터 리소스가 릴리스됩니다.

## <a name="clean-up-resources"></a>리소스 정리

HDInsight는 Azure Storage 또는 Azure Data Lake Storage에 데이터를 저장하므로 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 스토리지에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다. [다음 단계](#next-steps)에 나열된 자습서를 즉시 수행하려는 경우 클러스터를 유지할 수 있습니다.

Azure Portal로 다시 전환하고, **삭제**를 선택합니다.

![Azure Portal에서 HDInsight 클러스터 삭제](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png "HDInsight 클러스터 삭제")

또한 리소스 그룹 이름을 선택하여 리소스 그룹 페이지를 연 다음, **리소스 그룹 삭제**를 선택할 수도 있습니다. 리소스 그룹을 삭제하여 HDInsight 클러스터와 기본 스토리지 계정을 삭제합니다.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>PowerShell Az 모듈로 부분 정리

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 HDInsight에서 Apache Spark 클러스터를 만들고 기본 Spark SQL 쿼리를 실행하는 방법을 알아보았습니다. 다음 자습서를 진행하여 샘플 데이터에서 대화형 쿼리를 실행하는 데 HDInsight 클러스터를 사용하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Apache Spark에서 대화형 쿼리 실행](./apache-spark-load-data-run-query.md)
