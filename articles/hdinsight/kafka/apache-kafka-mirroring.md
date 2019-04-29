---
title: Apache Kafka 토픽 미러링 - Azure HDInsight
description: Apache Kafka의 미러링 기능으로 보조 클러스터에 토픽을 미러링하여 HDInsight 클러스터에서 Kafka 복제본을 유지하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: ba04ed7c95cbf00d5996ef237d3ac65053da0662
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115063"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>MirrorMaker를 사용하여 HDInsight에서 Kafka와 함께 Apache Kafka 토픽 복제

Apache Kafka의 미러링 기능을 사용하여 토픽을 보조 클러스터로 복제하는 방법에 대해 알아봅니다. 미러링은 연속적 프로세스로 실행되거나 한 클러스터에서 다른 클러스터로 데이터를 마이그레이션하는 방법으로 단속적으로 사용될 수 있습니다.

이 예제에서 미러링은 두 HDInsight 클러스터 간에 항목을 복제하는 데 사용됩니다. 두 클러스터 모두 동일한 지역의 Azure Virtual Network에 있습니다.

> [!WARNING]  
> 미러링이 내결함성을 달성하는 수단으로 간주되어서는 안됩니다. 토픽 내의 항목에 대한 오프셋은 원본 및 대상 클러스터 간에 서로 다르므로 클라이언트에서는 이 두 가지를 서로 교환하여 사용할 수 없습니다.
>
> 내결함성이 염려되는 경우 클러스터 내의 토픽에 대한 복제를 설정해야 합니다. 자세한 내용은 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)을 참조하세요.

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka 미러링 작동 방식

미러링은 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 도구(Apache Kafka의 일부)를 사용하여 원본 클러스터의 토픽에서 레코드를 소비한 다음, 대상 클러스터에 로컬 복사본을 만듭니다. MirrorMaker는 원본 클러스터에서 읽은 *소비자*(0개 이상)와 로컬(대상) 클러스터에 쓰는 *생산자*(1개)를 사용합니다.

미러링 프로세스를 보여 주는 다이어그램은 다음과 같습니다.

![미러링 프로세스 다이어그램](./media/apache-kafka-mirroring/kafka-mirroring.png)

HDInsight의 Apache Kafka는 공용 인터넷을 통한 액세스를 Kafka 서비스에 제공하지 않습니다. Kafka 생산자 또는 소비자는 Kafka 클러스터의 노드와 동일한 Azure 가상 네트워크에 있어야 합니다. 이 예제에서는 Kafka 원본 및 대상 클러스터가 모두 동일한 Azure 가상 네트워크에 있습니다. 클러스터 간의 통신 흐름을 보여 주는 다이어그램은 다음과 같습니다.

![Azure 가상 네트워크에 있는 원본 및 대상 Kafka 클러스터 다이어그램](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

원본 및 대상 클러스터는 노드와 파티션 수에 따라 다를 수 있으며 토픽 내의 오프셋도 다릅니다. 미러링은 분할에 사용되는 키 값을 유지하므로 레코드 순서는 키 기준으로 유지됩니다.

### <a name="mirroring-across-network-boundaries"></a>네트워크 경계를 넘은 미러링

다른 네트워크의 Kafka 클러스터 간에 미러링해야 하는 경우 다음과 같은 추가 고려 사항이 있습니다.

* **게이트웨이**: 네트워크는 TCPIP 수준에서 통신할 수 있어야 합니다.

* **이름 확인**: 각 네트워크의 Kafka 클러스터는 호스트 이름을 사용하여 서로 연결할 수 있어야 합니다. 이렇게 하려면 요청을 다른 네트워크로 전달하도록 구성된 DNS(도메인 이름 시스템) 서버가 각 네트워크에 필요할 수 있습니다.

    Azure Virtual Network를 만들 때 네트워크에서 제공되는 자동 DNS를 사용하는 대신 사용자 지정 DNS 서버와 해당 서버의 IP 주소를 지정해야 합니다. 가상 네트워크를 만든 후에는 해당 IP 주소를 사용하는 Azure Virtual Machine을 만든 다음 DNS 소프트웨어를 설치하고 구성해야 합니다.

    > [!WARNING]  
    > HDInsight를 Virtual Network에 설치하기 전에 사용자 지정 DNS 서버를 만들고 구성합니다. Virtual Network에 구성된 DNS 서버를 사용하기 위해 HDInsight를 추가로 구성할 필요는 없습니다.

두 개의 Azure Virtual Network 연결에 대한 자세한 내용은 [VNet 간 연결 구성](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요.

## <a name="create-apache-kafka-clusters"></a>Apache Kafka 클러스터 만들기

Azure 가상 네트워크와 Kafka 클러스터를 수동으로 만들 수 있지만 Azure Resource Manager 템플릿을 사용하는 것이 더 쉽습니다. 다음 단계에 따라 Azure 가상 네트워크와 두 Kafka 클러스터를 Azure 구독에 배포합니다.

1. Azure에 로그인하고 Azure Portal에서 템플릿을 열려면 다음 단추를 사용합니다.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager 템플릿의 위치는 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**입니다.

    > [!WARNING]  
    > HDInsight에서 Kafka의 사용 가능성을 보장하려면 클러스터에 작업자 노드가 3개 이상 포함되어야 합니다. 이 템플릿은 세 개의 작업자 노드를 포함하는 Kafka 클러스터를 만듭니다.

2. 다음 정보를 사용하여 **사용자 지정 배포** 블레이드의 항목을 채웁니다.
    
    ![HDInsight 사용자 지정 배포](./media/apache-kafka-mirroring/parameters.png)
    
    * **리소스 그룹**: 그룹을 만들거나 기존 그룹을 선택합니다. 이 그룹에는 HDInsight 클러스터가 포함됩니다.

    * **위치**: 지리적으로 가까운 위치를 선택합니다.
     
    * **기본 클러스터 이름**: 이 값은 Kafka 클러스터의 기본 이름으로 사용됩니다. 예를 들어 **hdi**를 입력하면 **source-hdi** 및 **dest-hdi** 클러스터가 만들어집니다.

    * **클러스터 로그인 사용자 이름**: 원본 및 대상 Kafka 클러스터의 관리 사용자 이름입니다.

    * **클러스터 로그인 암호**: 원본 및 대상 Kafka 클러스터의 관리 사용자 암호입니다.

    * **SSH 사용자 이름**: 원본 및 대상 Kafka 클러스터에 만들 SSH 사용자입니다.

    * **SSH 암호**: 원본 및 대상 Kafka 클러스터에 대한 SSH 사용자의 암호입니다.

3. **사용 약관**을 읽은 다음 **위에 명시된 사용 약관에 동의함**을 선택합니다.

4. 마지막으로 **대시보드에 고정**을 선택한 다음 **구매**를 선택합니다. 클러스터를 만드는 데 약 20분이 걸립니다.

> [!IMPORTANT]  
> HDInsight 클러스터의 이름은 **source-BASENAME** 및 **dest-BASENAME**이며, 여기서 BASENAME은 템플릿에 제공한 이름입니다. 이후 단계에서 클러스터에 연결할 때 이러한 이름을 사용합니다.

## <a name="create-topics"></a>토픽 만들기

1. SSH를 사용하여 **원본** 클러스터에 연결합니다.

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    **sshuser**은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. **BASENAME**은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 사용하여 원본 클러스터에 대한 Apache Zookeeper 호스트를 찾습니다.

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    `$CLUSTERNAME`을 원본 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터 로그인(관리자) 계정에 대한 암호를 입력합니다.

3. `testtopic`이라는 토픽을 만들려면 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. 다음 명령을 사용하여 토픽이 만들어졌는지 확인합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    응답에 `testtopic`이 있습니다.

4. 다음 명령을 사용하여 이 클러스터(**원본**)에 대한 Zookeeper 호스트 정보를 봅니다.

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    이 명령은 다음 텍스트와 비슷한 정보를 반환합니다.

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    이 정보를 저장합니다. 해당 정보는 다음 섹션에서 사용됩니다.

## <a name="configure-mirroring"></a>미러링 구성

1. 다른 SSH 세션을 사용하여 **대상** 클러스터에 연결합니다.

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    **sshuser**은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. **BASENAME**은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. `consumer.properties` 파일은 **원본** 클러스터와의 통신을 구성하는 데 사용됩니다. 파일을 만들려면 다음 명령을 사용합니다.

    ```bash
    nano consumer.properties
    ```

    `consumer.properties` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    **SOURCE_ZKHOSTS**를 **원본** 클러스터의 Zookeeper 호스트 정보로 바꿉니다.

    이 파일은 원본 Kafka 클러스터에서 읽을 때 사용할 소비자 정보를 설명합니다. 소비자 구성에 대한 자세한 내용은 kafka.apache.org의 [소비자 구성](https://kafka.apache.org/documentation#consumerconfigs)(영문)을 참조하세요.

    파일을 저장하려면 **Ctrl + X**, **Y** 및 **Enter** 키를 사용합니다.

3. 대상 클러스터와 통신하는 생산자를 구성하기 전에 **대상** 클러스터에 대한 broker 호스트를 찾아야 합니다. 다음 명령을 사용하여 이 정보를 검색합니다.

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    `$CLUSTERNAME`을 대상 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터 로그인(관리자) 계정에 대한 암호를 입력합니다.

    `echo` 명령은 다음 텍스트와 비슷한 정보를 반환합니다.

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. `producer.properties` 파일은 __대상__ 클러스터와 통신하는 데 사용됩니다. 파일을 만들려면 다음 명령을 사용합니다.

    ```bash
    nano producer.properties
    ```

    `producer.properties` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    **DEST_BROKERS**를 이전 단계의 broker 정보로 바꿉니다.

    생산자 구성에 대한 자세한 내용은 kafka.apache.org의 [생산자 구성](https://kafka.apache.org/documentation#producerconfigs)(영문)을 참조하세요.

5. 다음 명령을 사용하여 대상 클러스터에 대한 Zookeeper 호스트를 찾습니다.

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    `$CLUSTERNAME`을 대상 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터 로그인(관리자) 계정에 대한 암호를 입력합니다.

7. HDInsight의 Kafka에 대한 기본 구성이 토픽의 자동 만들기를 허용하지 않습니다. 미러링 프로세스를 시작하기 전에 다음 옵션 중 하나를 사용해야 합니다.

    * **대상 클러스터에 토픽 만들기**: 이 옵션을 사용하면 파티션 및 복제 요소 수를 설정할 수 있습니다.

        다음 명령을 사용하여 사전에 토픽을 만들 수 있습니다.

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        `testtopic`을 만들려는 토픽의 이름으로 바꿉니다.

    * **자동 토픽을 만들기 위한 클러스터 구성**: 이 옵션을 사용하면 MirrorMaker에서 자동으로 토픽을 만들 수 있지만, 원본 토픽과는 다른 파티션 수 또는 복제 계수로 만들 수 있습니다.

        자동으로 토픽을 만들도록 대상 클러스터를 구성하려면 이러한 단계를 수행합니다.

        1. [Azure Portal](https://portal.azure.com)에서 대상 Kafka 클러스터를 선택합니다.
        2. 클러스터 개요에서 __클러스터 대시보드__를 선택합니다. 그런 다음 __HDInsight 클러스터 대시보드__를 선택합니다. 메시지가 표시되면 클러스터에 대한 로그인(관리자) 자격 증명을 사용하여 인증을 받습니다.
        3. 페이지 왼쪽 목록에서 __Kafka__ 서비스를 선택합니다.
        4. 페이지 중간의 __구성__을 선택합니다.
        5. __필터__ 필드에 `auto.create` 값을 입력합니다. 이렇게 하면 속성 목록이 필터링되고 `auto.create.topics.enable` 설정이 표시됩니다.
        6. `auto.create.topics.enable` 값을 true로 변경하고 __저장__을 선택합니다. 메모를 추가하고 __저장__을 다시 선택합니다.
        7. __Kafka__ 서비스를 선택하고 __다시 시작__을 선택한 후 __영향을 받는 모든 서비스 다시 시작__을 선택합니다. 메시지가 나타나면 __모두 다시 시작 확인__을 선택합니다.

## <a name="start-mirrormaker"></a>MirrorMaker 시작

1. **대상** 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 MirrorMaker 프로세스를 시작합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    이 예제에 사용된 매개 변수는 다음과 같습니다.

    * **--consumer.config**: 소비자 속성이 포함된 파일을 지정합니다. 이러한 속성은 *원본* Kafka 클러스터에서 읽는 소비자를 만드는 데 사용됩니다.

    * **--producer.config**: 생산자 속성이 포함된 파일을 지정합니다. 이러한 속성은 *대상* Kafka 클러스터에 쓰는 생산자를 만드는 데 사용됩니다.

    * **--whitelist**: MirrorMaker를 통해 원본 클러스터에서 대상 클러스터로 복제하는 항목의 목록입니다.

    * **--num.streams**: 생성할 소비자 스레드 수입니다.

   시작할 때 MirrorMaker는 다음 텍스트와 비슷한 정보를 반환합니다.

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. SSH 연결부터 **원본** 클러스터까지, 다음 명령을 사용하여 Producer를 시작하고 토픽에 메시지를 전송합니다.

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    `$CLUSTERNAME`을 원본 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터 로그인(관리자) 계정에 대한 암호를 입력합니다.

     커서로 빈 라인에 도달하면 몇 개의 텍스트 메시지를 입력합니다. 메시지는 **원본** 클러스터의 토픽으로 보내집니다. 완료되면 **Ctrl + C**를 클릭하여 프로듀서 프로세스를 종료합니다.

3. **대상** 클러스터에 대한 SSH 연결에서 **Ctrl + C**를 사용하여 MirrorMaker 프로세스를 종료합니다. 이 프로세스를 종료하는 데 몇 초 정도 걸릴 수 있습니다. 메시지가 대상에 복제되었는지 확인하려면 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    `$CLUSTERNAME`을 대상 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터 로그인(관리자) 계정에 대한 암호를 입력합니다.

    이제 항목 목록에 MirrorMaster가 원본 클러스터에서 대상으로 토픽을 미러링할 때 만들어진 `testtopic`이(가) 포함됩니다. 이 항목에서 검색한 메시지는 원본 클러스터에 입력한 것과 동일합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 단계를 수행하면 동일한 Azure 리소스 그룹에 두 클러스터가 모두 만들어지므로 Azure Portal에서 해당 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹을 삭제하면 이 문서의 단계를 수행하여 만들어진 모든 리소스, Azure Virtual Network 및 클러스터에서 사용하는 저장소 계정이 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 사용하여 [Apache Kafka](https://kafka.apache.org/) 클러스터 복제본을 만드는 방법을 알아보았습니다. Kafka를 사용하는 다른 방법을 찾으려면 다음 링크를 사용하세요.

* [Apache Kafka MirrorMaker 문서](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)(cwiki.apache.org)
* [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)
* [HDInsight에서 Apache Spark 및 Apache Kafka 사용](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight에서 Apache Storm 및 Apache Kafka 사용](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network를 통해 Apache Kafka에 연결](apache-kafka-connect-vpn-gateway.md)
