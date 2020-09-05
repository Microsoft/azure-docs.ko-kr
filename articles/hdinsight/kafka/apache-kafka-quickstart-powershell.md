---
title: '빠른 시작: Azure PowerShell을 사용하여 Apache Kafka 만들기 - HDInsight'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure HDInsight에서 Apache Kafka 클러스터를 만드는 방법에 대해 알아봅니다. 또한 Kafka 토픽, 구독자 및 소비자에 대해서도 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 6a01e86f4afe397ed78cd279231a2429b17c60a8
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651372"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure HDInsight에서 Apache Kafka 클러스터 만들기

[Apache Kafka](https://kafka.apache.org/)는 오픈 소스 분산형 스트리밍 플랫폼입니다. 게시-구독 메시지 큐와 유사한 기능을 제공하므로 메시지 브로커로 자주 사용됩니다. 

이 빠른 시작에서는 Azure PowerShell을 사용하여 [Apache Kafka](https://kafka.apache.org) 클러스터를 만드는 방법에 대해 알아봅니다. 또한 Kafka를 사용하여 메시지를 받거나 보내기 위해 제공되는 유틸리티를 사용하는 방법을 알아봅니다.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Kafka API는 동일한 가상 네트워크 내에서만 리소스에서 액세스할 수 있습니다. 이 빠른 시작에서는 직접 SSH를 사용하여 클러스터에 액세스합니다. 다른 서비스, 네트워크 또는 가상 머신을 Kafka에 연결하려면 먼저 가상 네트워크를 만든 다음, 네트워크 내에 리소스를 만듭니다. 자세한 내용은 [가상 네트워크를 사용하여 Apache Kafka에 연결](apache-kafka-connect-vpn-gateway.md) 문서를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* PowerShell [Az 모듈](https://docs.microsoft.com/powershell/azure/)이 설치되었습니다.

* SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzAccount` cmdlet으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurepowershell-interactive
# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 이름과 위치를 묻는 메시지를 표시한 다음 새 리소스 그룹을 만듭니다.

```azurepowershell-interactive
$resourceGroup = Read-Host -Prompt "Enter the resource group name"
$location = Read-Host -Prompt "Enter the Azure region to use"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

HDInsight의 Kafka는 Azure Managed 디스크를 사용하여 Kafka 데이터를 저장하는 한편 클러스터는 Azure Storage를 사용하여 로그와 같은 정보를 저장합니다. [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)를 사용하여 새 스토리지 계정을 만듭니다.

> [!IMPORTANT]  
> 스토리지 계정 종류 `BlobStorage`는 HDInsight 클러스터의 보조 스토리지로만 사용할 수 있습니다.

```azurepowershell-interactive
$storageName = Read-Host -Prompt "Enter the storage account name"

New-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $storageName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1
```

HDInsight는 BLOB 컨테이너의 스토리지 계정에 데이터를 저장합니다. [New-AzStorageContainer](/powershell/module/Az.Storage/New-AzStorageContainer)를 사용하여 새 컨테이너를 만듭니다.

```azurepowershell-interactive
$containerName = Read-Host -Prompt "Enter the container name"

$storageKey = (Get-AzStorageAccountKey `
                -ResourceGroupName $resourceGroup `
                -Name $storageName)[0].Value
$storageContext = New-AzStorageContext `
                    -StorageAccountName $storageName `
                    -StorageAccountKey $storageKey

New-AzStorageContainer -Name $containerName -Context $storageContext
```

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka 클러스터 만들기

[New-AzHDInsightCluster](/powershell/module/az.HDInsight/New-azHDInsightCluster)를 사용하여 HDInsight 클러스터에 Apache Kafka를 만듭니다.

```azurepowershell-interactive
# Create a Kafka 1.1 cluster
$clusterName = Read-Host -Prompt "Enter the name of the Kafka cluster"
$httpCredential = Get-Credential -Message "Enter the cluster login credentials" -UserName "admin"
$sshCredentials = Get-Credential -Message "Enter the SSH user credentials" -UserName "sshuser"

$numberOfWorkerNodes = "4"
$clusterVersion = "3.6"
$clusterType="Kafka"
$disksPerNode=2

$kafkaConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$kafkaConfig.Add("kafka", "1.1")

New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroup `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $numberOfWorkerNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $kafkaConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials `
        -DisksPerWorkerNode $disksPerNode
```

HDInsight 클러스터를 만드는 데 최대 20분이 걸릴 수 있습니다.

`-DisksPerWorkerNode` 매개 변수는 HDInsight의 Kafka 확장성을 구성합니다. HDInsight의 Kafka는 클러스터에서 가상 머신의 로컬 디스크를 사용하여 데이터를 저장합니다. Kafka는 입출력이 많으므로 높은 처리량과 노드당 더 많은 스토리지를 제공하기 위해 [Azure Managed Disks](../../virtual-machines/managed-disks-overview.md)를 사용합니다.

관리 디스크 유형은 __표준__ (HDD) 또는 __프리미엄__ (SSD)일 수 있습니다. 디스크 유형은 작업자 노드(Kafka broker)에서 사용하는 VM 크기에 따라 달라집니다. 프리미엄 디스크는 DS 및 GS 시리즈 VM에 자동으로 사용됩니다. 다른 모든 VM 유형은 표준을 사용합니다. `-WorkerNodeSize` 매개 변수를 사용하여 VM 유형을 설정할 수 있습니다. 매개 변수에 대한 자세한 내용은 [New-AzHDInsightCluster](/powershell/module/az.HDInsight/New-azHDInsightCluster) 설명서를 참조하세요.

클러스터를 생성할 때 또는 생성 후 클러스터를 확장할 때 32개 이상의 작업자 노드를 사용하려는 경우 최소한 8개의 노드와 14GB RAM으로 VM 크기를 지정하기 위해 `-HeadNodeSize` 매개 변수를 사용해야 합니다. 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

1. Kafka 클러스터의 기본 헤드 노드에 연결하려면 다음 명령을 사용합니다. `sshuser`를 SSH 사용자 이름으로 바꿉니다. `mykafka`를 Kafka 클러스터의 이름으로 바꿉니다.

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. 클러스터에 처음 연결할 때 호스트의 신뢰성을 설정할 수 없다는 경고가 SSH 클라이언트에 표시될 수도 있습니다. 메시지가 표시되면 __예__ 를 입력한 다음, __Enter__ 키를 눌러 SSH 클라이언트의 신뢰할 수 있는 서버 목록에 호스트를 추가합니다.

3. 확인 메시지가 표시되면 SSH 사용자의 암호를 입력합니다.

연결되면 다음 텍스트와 유사한 정보가 표시됩니다.

```output
Authorized uses only. All activity may be monitored and reported.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Apache Zookeeper 및 Broker 호스트 정보 가져오기

Kafka를 사용할 때는 *Apache Zookeeper* 및 *Broker* 호스트를 알고 있어야 합니다. 이러한 호스트는 Kafka API 및 Kafka와 함께 제공되는 다양한 유틸리티에서 사용됩니다.

이 섹션에서는 클러스터에서 Apache Ambari REST API에서 호스트 정보를 가져옵니다.

1. 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 `jq` 유틸리티를 설치합니다. 이 유틸리티는 JSON 문서를 구문 분석하는 데 사용되며 호스트 정보 검색에 유용합니다.
   
    ```bash
    sudo apt -y install jq
    ```

2. 환경 변수를 클러스터 이름으로 설정하려면 다음 명령을 사용합니다.

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    메시지가 표시되면 Kafka 클러스터 이름을 입력합니다.

3. Zookeeper 호스트 정보를 사용하여 환경 변수를 설정하려면 아래 명령을 사용합니다. 이 명령은 모든 Zookeeper 호스트를 검색한 다음, 처음 두 개의 항목만 반환합니다. 하나의 호스트에 연결할 수 없는 경우 일부 중복이 필요하기 때문입니다.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    메시지가 표시되면 클러스터 로그인 계정(SSH 계정 아님)에 대한 암호를 입력합니다.

4. 환경 변수가 올바르게 설정되었는지 확인하려면 다음 명령을 사용합니다.

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    이 명령은 다음 텍스트와 유사한 정보를 반환합니다.

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Kafka broker 호스트 정보를 사용하여 환경 변수를 설정하려면 다음 명령을 사용합니다.

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    메시지가 표시되면 클러스터 로그인 계정(SSH 계정 아님)에 대한 암호를 입력합니다.

6. 환경 변수가 올바르게 설정되었는지 확인하려면 다음 명령을 사용합니다.

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    이 명령은 다음 텍스트와 유사한 정보를 반환합니다.
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Apache Kafka 토픽 관리

Kafka는 *토픽*에 데이터 스트림을 저장합니다. 토픽을 관리하는 데 `kafka-topics.sh` 유틸리티를 사용할 수 있습니다.

* **토픽을 만들려면** SSH 연결에서 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    이 명령은 `$KAFKAZKHOSTS`에 저장된 호스트 정보를 사용하여 Zookeeper에 연결합니다. 그런 후 **test**라는 Kafka 토픽을 만듭니다. 

    * 이 토픽에 저장된 데이터는 8개의 파티션에 분할됩니다.

    * 각 파티션은 클러스터에서 세 개의 작업자 노드에 복제됩니다.

        세 개의 장애 도메인을 제공하는 Azure 지역에서 클러스터를 만든 경우 3의 복제 인수를 사용합니다. 그렇지 않으면 4의 복제 요소를 사용합니다.
        
        세 개의 장애 도메인 지역에서는 3의 복제 인수를 사용하면 복제본을 오류 도메인에 분산할 수 있습니다. 두 개의 장애 도메인 지역에서는 4의 복제 인수로 복제본을 도메인에 동일하게 분산할 수 있습니다.
        
        영역에서 장애 도메인의 수에 대한 자세한 내용은 [Linux 가상 머신의 가용성](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) 문서를 참조하세요.

        Kafka는 Azure 장애 도메인을 인식하지 않습니다. 항목에 대한 파티션 복제본을 만들 때 고가용성에 대해 복제본을 제대로 배포하지 않을 수 있습니다.

        고가용성을 보장하려면 [Apache Kafka 파티션 재조정 도구](https://github.com/hdinsight/hdinsight-kafka-tools)를 사용합니다. 이 도구는 Kafka 클러스터의 헤드 노드에 대한 SSH 연결에서 실행되어야 합니다.

        가장 높은 Kafka 데이터 가용성을 보장하려면 다음과 같은 경우에 토픽에 대한 파티션 복제본의 부하를 다시 조정해야 합니다.

        * 새 토픽 또는 파티션을 만들 때

        * 클러스터를 강화할 때

* **토픽을 나열하려면** 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    이 명령은 Kafka 클러스터에서 사용할 수 있는 토픽을 나열합니다.

* **토픽을 삭제하려면** 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    이 명령은 `topicname`이라고 명명된 토픽을 삭제합니다.

    > [!WARNING]  
    > 이전에 만든 `test` 토픽을 삭제하는 경우에는 다시 만들어야 합니다. 이는 이 문서의 뒷부분에서 사용됩니다.

`kafka-topics.sh` 유틸리티에서 사용할 수 있는 명령에 대한 자세한 정보는 다음 명령을 사용합니다.

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>레코드 생성 및 소비

Kafka는 토픽에 *레코드*를 저장합니다. *생산자*에서 레코드를 생성하고, *소비자*에서 이 레코드를 소비합니다. 생산자와 소비자는 *Kafka broker* 서비스와 통신합니다. HDInsight 클러스터의 각 작업자 노드는 Kafka broker 호스트입니다.

앞에서 만든 test 토픽에 레코드를 저장한 다음, 소비자를 통해 레코드를 읽으려면 다음 단계를 사용합니다.

1. 토픽에 레코드를 작성하려면 SSH 연결에서 `kafka-console-producer.sh` 유틸리티를 사용합니다.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    이 명령 후 빈 줄에 도달합니다.

2. 빈 줄에 문자 메시지를 입력하고 Enter 키를 누릅니다. 이와 같은 방식으로 메시지 몇 개를 입력한 후 **Ctrl + C** 키를 사용하여 일반 프롬프트로 돌아갑니다. 각 줄은 별도의 레코드로 Kafka 토픽에 전송됩니다.

3. 토픽에서 레코드를 읽으려면 SSH 연결에서 `kafka-console-consumer.sh` 유틸리티를 사용합니다.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    이 명령은 토픽에서 레코드를 검색하여 표시합니다. `--from-beginning`을 사용하면 스트림 시작 부분부터 시작하도록 소비자에 지시하여 모든 레코드를 검색합니다.

    이전 버전의 Kafka를 사용하는 경우 `--bootstrap-server $KAFKABROKERS`를 `--zookeeper $KAFKAZKHOSTS`로 바꿉니다.

4. __Ctrl+C__ 를 사용하여 소비자를 중지합니다.

또한 프로그래밍 방식으로 생산자와 소비자를 만들 수 있습니다. 이 API를 사용하는 예제는 [HDInsight의 Apache Kafka 생산자 및 소비자 API](apache-kafka-producer-consumer-api.md) 문서를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, HDInsight 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

> [!WARNING]  
> 클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다.
> 
> HDInsight 클러스터의 Kafka를 삭제하면 Kafka에 저장된 데이터가 모두 삭제됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Apache Kafka에서 Apache Spark 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)
