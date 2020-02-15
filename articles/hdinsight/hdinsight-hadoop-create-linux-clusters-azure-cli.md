---
title: Azure CLI를 사용 하 여 Apache Hadoop 클러스터 만들기-Azure HDInsight
description: 플랫폼 간 Azure CLI를 사용 하 여 Azure HDInsight 클러스터를 만드는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199044"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Azure CLI를 사용하여 HDInsight 클러스터 만들기

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

이 문서의 단계는 Azure CLI을 사용 하 여 HDInsight 3.6 클러스터를 만드는 과정을 안내 합니다.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure CLI. Azure CLI를 설치하지 않은 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)에 나온 단계를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>클러스터 만들기

1. Azure 구독에 로그인합니다. Azure Cloud Shell를 사용 하려는 경우 코드 블록의 오른쪽 위 모퉁이에서 **시도** 를 선택 합니다. 그렇지 않은 경우 아래 명령을 입력합니다.

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 환경 변수를 설정합니다. 이 문서에서 변수를 사용 하는 것은 Bash를 기반으로 합니다. 다른 환경에서는 약간의 변형이 필요합니다. 클러스터 만들기에 사용할 수 있는 매개 변수의 전체 목록은 [az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) 를 참조 하세요.

    |매개 변수 | Description |
    |---|---|
    |`--workernode-count`| 클러스터의 작업자 노드 수입니다. 이 문서에서는 `clusterSizeInNodes` 변수를 `--workernode-count`에 전달 된 값으로 사용 합니다. |
    |`--version`| HDInsight 클러스터 버전입니다. 이 문서에서는 `clusterVersion` 변수를 `--version`에 전달 된 값으로 사용 합니다. 참고 항목: [지원 되는 HDInsight 버전](./hdinsight-component-versioning.md#supported-hdinsight-versions)|
    |`--type`| HDInsight 클러스터 유형 (예: hadoop, interactivehive, hbase, kafka, 폭풍, spark, rserver, mlservices).  이 문서에서는 `clusterType` 변수를 `--type`에 전달 된 값으로 사용 합니다. 참고 항목: [클러스터 형식 및 구성](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type)|
    |`--component-version`|' Component = version ' 형식의 공백으로 구분 된 버전에 있는 다양 한 Hadoop 구성 요소의 버전입니다. 이 문서에서는 `componentVersion` 변수를 `--component-version`에 전달 된 값으로 사용 합니다. 참고 항목: [Hadoop 구성 요소](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)|

    `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`및 `PASSWORD`을 원하는 값으로 바꿉니다. 다른 변수의 값을 원하는 대로 변경 합니다. 그런 다음 CLI 명령을 입력 합니다.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. 아래 명령을 입력 하 여 [리소스 그룹을 만듭니다](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) .

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    유효한 위치 목록을 보려면 `az account list-locations` 명령을 사용한 다음 `name` 값의 위치 중 하나를 사용 합니다.

4. 아래 명령을 입력 하 여 [Azure Storage 계정을 만듭니다](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) .

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Azure Storage 계정에서 기본 키를 추출](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) 하 고 아래 명령을 입력 하 여 변수에 저장 합니다.

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. 아래 명령을 입력 하 여 [Azure Storage 컨테이너를 만듭니다](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) .

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. 다음 명령을 입력 하 여 [HDInsight 클러스터를 만듭니다](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) .

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight 클러스터는 워크로드 또는 클러스터에 대한 튜닝 기술에 해당하는 다양한 형식을 제공합니다. 하나의 클러스터에서 Storm 및 HBase 등의 여러 유형을 결합하는 클러스터를 만들기 위해 지원되는 메서드가 없습니다.

    클러스터 만들기 프로세스가 완료 되는 데 몇 분 정도 걸릴 수 있습니다. 일반적으로 약 15분이 걸립니다.

## <a name="clean-up-resources"></a>리소스 정리

이 문서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 스토리지에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

리소스를 제거하려면 다음 명령의 전체 또는 일부를 입력합니다.

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](./hdinsight-hadoop-customize-cluster-linux.md#access-control)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용 하 여 HDInsight 클러스터를 성공적으로 만들었으므로 다음을 사용 하 여 클러스터 작업을 수행 하는 방법을 알아보세요.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 클러스터

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight와 함께 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 클러스터

* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 Apache HBase용 Java 애플리케이션 개발](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 클러스터

* [HDInsight에서 Apache Storm용 Java 토폴로지 개발](storm/apache-storm-develop-java-topology.md)
* [HDInsight의 Apache Storm에서 Python 구성 요소 사용](storm/apache-storm-develop-python-topology.md)
* [HDInsight에서 Apache Storm을 사용하는 토폴로지 배포 및 모니터링](storm/apache-storm-deploy-monitor-topology-linux.md)
