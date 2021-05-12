---
title: Azure IoT Hub를 통해 HDInsight에서 Apache Kafka 사용
description: Azure IoT Hub를 통해 HDInsight에서 Apache Kafka를 사용하는 방법에 대해 알아봅니다. Kafka 연결 Azure IoT Hub 프로젝트는 Kafka에 대한 원본 및 싱크 커넥터를 제공합니다. 원본 커넥터는 IoT Hub에서 데이터를 읽을 수 있으며 싱크 커넥터는 IoT Hub에 기록합니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: feada70c7a461bb4a9cd621c76b5606a7f0e19d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865284"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Azure IoT Hub를 통해 HDInsight에서 Apache Kafka 사용

HDInsight의 Apache Kafka 및 Azure IoT Hub 간에 데이터를 이동하려면 [Apache Kafka 연결 Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) 커넥터를 사용하는 방법에 대해 알아봅니다. 이 문서에서는 클러스터의 에지 노드에서 IoT Hub 커넥터를 실행하는 방법을 설명합니다.

Kafka 연결 API를 사용하면 데이터를 계속 Kafka로 끌어오거나 Kafka에서 다른 시스템으로 데이터를 밀어넣기하는 커넥터를 구현할 수 있습니다. [Apache Kafka 연결 Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub)는 Azure IoT Hub에서 데이터를 Kafka로 끌어오는 커넥터입니다. Kafka에서 데이터를 IoT Hub로 밀어넣기할 수도 있습니다.

IoT Hub에서 끌어오는 경우 __원본__ 커넥터를 사용합니다. IoT Hub로 밀어넣기하는 경우 __싱크__ 커넥터를 사용합니다. IoT Hub 커넥터는 원본 및 싱크 커넥터 모두를 제공합니다.

다음 다이어그램에서는 커넥터를 사용하는 경우 HDInsight의 Kafka 및 Azure IoT Hub 간의 데이터 흐름을 표시합니다.

:::image type="content" source="./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png" alt-text="커넥터를 통해 IoT Hub에서 Kafka로 흐르는 데이터를 보여주는 이미지" border="false":::

연결 API에 대한 자세한 내용은 [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* HDInsight의 Apache Kafka 클러스터. 자세한 내용은 [HDInsight의 Kafka 빠른 시작](apache-kafka-get-started.md) 문서를 참조하세요.

* Kafka 클러스터의 에지 노드입니다. 자세한 내용은 [HDInsight와 함께 에지 노드 사용](../hdinsight-apps-use-edge-node.md) 문서를 참조하세요.

* SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* Azure IoT Hub 및 디바이스. 이 문서의 경우 [Azure IoT Hub에 Raspberry Pi 온라인 시뮬레이터를 연결](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md)하는 것이 좋습니다.

* [Scala 빌드 도구](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>커넥터 구축

1. [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/)에서 커넥터에 대한 원본을 로컬 환경에 다운로드합니다.

2. 명령 프롬프트에서 `toketi-kafka-connect-iothub-master` 디렉터리로 이동합니다. 그런 다음, 다음 명령을 사용하여 프로젝트를 빌드하고 패키징합니다.

    ```cmd
    sbt assembly
    ```

    빌드를 완료하는 데 몇 분 정도 걸립니다. 이 명령은 프로젝트에 대한 `toketi-kafka-connect-iothub-master\target\scala-2.11` 디렉터리에서 `kafka-connect-iothub-assembly_2.11-0.7.0.jar`이라는 파일을 만듭니다.

## <a name="install-the-connector"></a>커넥터 설치

1. HDInsight 클러스터에서 Kafka의 에지 노드에 .jar 파일을 업로드합니다. `CLUSTERNAME`을 실제 클러스터의 이름으로 바꾸어 아래 명령을 편집합니다. SSH 사용자 계정 및 [에지 노드](../hdinsight-apps-use-edge-node.md#access-an-edge-node) 이름의 기본값은 아래와 같이 사용하며 필요에 따라 수정합니다.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. 파일 복사가 완료되면 SSH를 사용하여 에지 노드에 연결합니다.

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kafka `libs` 디렉터리에 커넥터를 설치하려면 다음 명령을 사용합니다.

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

나머지 단계에서 SSH 연결을 활성 상태로 유지합니다.

## <a name="configure-apache-kafka"></a>Apache Kafka 구성

SSH 연결에서 에지 노드까지 다음 단계를 사용하여 독립 실행형 모드에서 실행되도록 Kafka를 구성합니다.

1. 암호 변수를 설정합니다. PASSWORD를 클러스터 로그인 암호로 바꾼 다음, 다음 명령을 입력합니다.

    ```bash
    export password='PASSWORD'
    ```

1. [jq](https://stedolan.github.io/jq/) 유틸리티를 설치합니다. jq는 Ambari 쿼리에서 반환된 JSON 문서 처리를 더 쉽게 해줍니다. 다음 명령을 입력합니다.

    ```bash
    sudo apt -y install jq
    ```

1. Kafka broker의 주소를 가져옵니다. 클러스터에 많은 broker가 있을 수 있지만 하나 또는 두 개의 참조만 필요합니다. 두 broker 호스트의 주소를 가져오려면 다음 명령을 사용합니다.

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    나중에 사용하도록 이 값을 복사합니다. 반환되는 값은 다음 텍스트와 유사합니다.

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Apache Zookeeper 노드의 주소를 가져옵니다. 클러스터에 여러 Zookeeper 노드가 있지만 하나 또는 두 개의 참조만 필요합니다. 다음 명령을 사용하여 `KAFKAZKHOSTS` 변수에 주소를 저장합니다.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. 독립 실행형 모드에서 커넥터를 실행하는 경우 `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` 파일은 Kafka broker와 통신하는 데 사용됩니다. `connect-standalone.properties` 파일을 편집하려면 다음 명령을 사용합니다.

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. 다음과 같이 편집합니다.

    |현재 값 |새 값 | 의견 |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|`localhost:9092` 값을 이전 단계의 broker 호스트로 바꿉니다.|Kafka broker를 찾기 위해 에지 노드에 대한 독립 실행형 구성을 구성합니다.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|이 변경을 통해 Kafka에 포함된 콘솔 생산자를 사용하여 테스트할 수 있습니다. 다른 생산자와 소비자에 대한 다른 변환기가 필요할 수 있습니다. 다른 변환기 값의 사용에 대한 내용은 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)을 참조합니다.|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|위와 동일합니다.|
    |해당 없음|`consumer.max.poll.records=10`|파일의 끝에 추가합니다. 이 변경은 한 번에 10개의 레코드로 제한하여 싱크 커넥터에서 시간 제한을 방지하기 위함입니다. 자세한 내용은 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)을(를) 참조하세요.|

1. 파일을 저장하려면 __Ctrl + X__, __Y__ 및 __Enter__ 키를 사용합니다.

1. 커넥터에서 사용하는 항목을 만들려면 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` 및 `iotout` 항목이 있는지 확인하려면 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` 항목은 IoT Hub에서 메시지를 수신하는 데 사용됩니다. `iotout` 항목은 IoT Hub로 메시지를 보내는 데 사용됩니다.

## <a name="get-iot-hub-connection-information"></a>IoT Hub 연결 정보 가져오기

커넥터에서 사용하는 IoT Hub 정보를 검색하려면 다음 단계를 사용합니다.

1. Event Hub 호환 엔드포인트 및 IoT Hub에 대한 Event Hub 호환 엔드포인트 이름을 가져옵니다. 이 정보를 가져오려면 다음 방법 중 하나를 사용합니다.

   * __[Azure Portal](https://portal.azure.com/)__ 에서 다음 단계를 사용하세요.

     1. IoT Hub로 이동하고 __엔드포인트__ 를 선택합니다.
     2. __기본 제공 엔드포인트__ 에서 __이벤트__ 를 선택합니다.
     3. __속성__ 에서 다음 필드의 값을 복사합니다.

         * __Event Hub 호환 이름__
         * __Event Hub 호환 엔드포인트__
         * __파티션__

        > [!IMPORTANT]  
        > 포털에서 엔드포인트 값이 이 예제에서 필요하지 않은 추가 텍스트를 포함할 수 있습니다. 이 패턴 `sb://<randomnamespace>.servicebus.windows.net/`과 일치하는 텍스트를 추출합니다.

   * __[Azure CLI](/cli/azure/get-started-with-azure-cli)__ 에서 다음 명령을 입력합니다.

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       `myhubname`을 IoT Hub 이름으로 바꿉니다. 응답은 다음 텍스트와 비슷합니다.

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. __공유 액세스 정책__ 및 __키__ 를 가져옵니다. 이 예에서는 __서비스__ 키를 사용합니다. 이 정보를 가져오려면 다음 방법 중 하나를 사용합니다.

    * __[Azure Portal](https://portal.azure.com/)__ 에서 다음 단계를 사용하세요.

        1. __공유 액세스 정책__ 을 선택한 다음, __서비스__ 를 선택합니다.
        2. __기본 키__ 값을 복사합니다.
        3. __연결 문자열 - 기본 키__ 값을 복사합니다.

    * __[Azure CLI](/cli/azure/get-started-with-azure-cli)__ 에서 다음 명령을 입력합니다.

        1. 기본 키 값을 가져오려면 다음 명령을 사용합니다.

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            `myhubname`을 IoT Hub 이름으로 바꿉니다. 응답은 이 허브에 대한 `service` 정책의 기본 키입니다.

        2. `service` 정책에 대한 연결 문자열을 가져오려면 다음 명령을 사용합니다.

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            `myhubname`을 IoT Hub 이름으로 바꿉니다. 응답은 `service` 정책에 대한 연결 문자열입니다.

## <a name="configure-the-source-connection"></a>원본 연결 구성

IoT Hub와 작동하도록 원본을 구성하려면 SSH 연결에서 에지 노드까지 다음 작업을 수행합니다.

1. `/usr/hdp/current/kafka-broker/config/` 디렉터리에서 `connect-iot-source.properties` 파일의 복사본을 만듭니다. toketi-kafka-connect-iothub 프로젝트에서 파일을 다운로드하려면 다음 명령을 사용합니다.

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. `connect-iot-source.properties` 파일을 편집하고 IoT Hub 정보를 추가하려면 다음 명령을 사용합니다.

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. 편집기에서 다음 항목을 찾아 변경합니다.

    |현재 값 |편집|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|`PLACEHOLDER`을 `iotin`로 바꿉니다. IoT Hub에서 받은 메시지는 `iotin` 항목에 배치됩니다.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|`PLACEHOLDER`를 Event Hub 호환 이름으로 바꿉니다.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|`PLACEHOLDER`를 Event Hub 호환 엔드포인트로 바꿉니다.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|`PLACEHOLDER`을 `service`로 바꿉니다.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|`PLACEHOLDER`를 `service` 정책의 기본 키로 바꿉니다.|
    |`IotHub.Partitions=PLACEHOLDER`|`PLACEHOLDER`를 이전 단계의 파티션 수로 바꿉니다.|
    |`IotHub.StartTime=PLACEHOLDER`|`PLACEHOLDER`를 UTC 날짜로 바꿉니다. 이 날짜는 커넥터가 메시지에 대한 검사를 시작할 때입니다. 날짜 형식은 `yyyy-mm-ddThh:mm:ssZ`입니다.|
    |`BatchSize=100`|`100`을 `5`로 바꿉니다. 이 변경으로 인해 IoT Hub에 5개의 새 메시지가 있는 경우 커넥터가 메시지를 Kafka로 읽어 들일 수 있습니다.|

    구성 예제는 [Azure IoT Hub용 Kafka 연결 원본 커넥터](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)를 참조하세요.

1. 변경을 저장하려면 __Ctrl + X__, __Y__ 및 __Enter__ 키를 사용합니다.

커넥터 원본 구성에 대한 자세한 내용은 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)을 참조합니다.

## <a name="configure-the-sink-connection"></a>싱크 연결 구성

IoT Hub와 작동하도록 싱크 연결을 구성하려면 SSH 연결에서 에지 노드까지 다음 작업을 수행합니다.

1. `/usr/hdp/current/kafka-broker/config/` 디렉터리에서 `connect-iothub-sink.properties` 파일의 복사본을 만듭니다. toketi-kafka-connect-iothub 프로젝트에서 파일을 다운로드하려면 다음 명령을 사용합니다.

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. `connect-iothub-sink.properties` 파일을 편집하고 IoT Hub 정보를 추가하려면 다음 명령을 사용합니다.

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. 편집기에서 다음 항목을 찾아 변경합니다.

    |현재 값 |편집|
    |---|---|
    |`topics=PLACEHOLDER`|`PLACEHOLDER`을 `iotout`로 바꿉니다. `iotout` 항목에 기록된 메시지는 IoT Hub로 전달됩니다.|
    |`IotHub.ConnectionString=PLACEHOLDER`|`PLACEHOLDER`를 `service` 정책에 대한 연결 문자열로 바꿉니다.|

    구성 예제는 [Azure IoT Hub용 Kafka 연결 싱크 커넥터](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)를 참조하세요.

1. 변경을 저장하려면 __Ctrl + X__, __Y__ 및 __Enter__ 키를 사용합니다.

커넥터 싱크 구성에 대한 자세한 내용은 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)을 참조합니다.

## <a name="start-the-source-connector"></a>원본 커넥터 시작

1. 원본 커넥터를 시작하려면 SSH 연결에서 에지 노드까지 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    커넥터가 시작하면 해당 디바이스에서 IoT Hub로 메시지를 보냅니다. 커넥터는 IoT Hub에서 메시지를 읽고 Kafka 토픽에 저장하므로 콘솔에 정보를 기록합니다.

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > 커넥터가 시작할 때 여러 경고가 표시될 수 있습니다. 이러한 경고는 IoT Hub에서 메시지를 수신하는 문제를 일으키지 않습니다.

1. 몇 분 후에 **Ctrl + C** 를 두 번 사용하여 커넥터를 중지합니다. 커넥터를 중지하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="start-the-sink-connector"></a>싱크 커넥터 시작

SSH 연결에서 에지 노드까지 다음 명령을 사용하여 독립 실행형 모드에서 싱크 커넥터를 시작합니다.

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

커넥터가 실행될 때 다음 텍스트와 유사한 정보가 표시됩니다.

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> 커넥터가 시작할 때 여러 경고를 확인할 수 있습니다. 무시해도 됩니다.

## <a name="send-messages"></a>메시지 보내기

커넥터를 통해 메시지를 보내려면 다음 단계를 사용합니다.

1. Kafka 클러스터에 ‘두 번째’ SSH 세션을 엽니다.

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 새 ssh 세션의 Kafka broker 주소를 가져옵니다. PASSWORD를 클러스터 로그인 암호로 바꾼 다음, 다음 명령을 입력합니다.

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. `iotout` 항목에 메시지를 보내려면 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    이 명령은 기본 Bash 프롬프트로 반환하지 않습니다. 대신 `iotout` 항목에 키보드 입력을 보냅니다.

1. 디바이스에 메시지를 보내려면 `kafka-console-producer`에 대한 SSH 세션에 JSON 문서를 붙여넣습니다.

    > [!IMPORTANT]  
    > 디바이스 ID에 `"deviceId"` 항목 값을 설정해야 합니다. 다음 예제에서 디바이스는 `myDeviceId`이라고 합니다.

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    이 JSON 문서에 대한 스키마는 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)에 자세히 설명되어 있습니다.

    시뮬레이션된 Raspberry Pi 디바이스를 사용하고 디바이스가 실행 중인 경우 해당 디바이스에서 다음 메시지를 기록합니다.

    ```output
    Receive message: Turn On
    ```

    JSON 문서를 다시 보내지만 `"message"` 항목 값을 변경합니다. 디바이스에서 새 값을 기록합니다.

싱크 커넥터 사용에 대한 자세한 내용은 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight에서 IoT Kafka 커넥터를 시작하기 위해 Apache Kafka 연결 API를 사용하는 방법을 알아보았습니다. Kafka를 사용하는 다른 방법을 찾으려면 다음 링크를 사용하세요.

* [HDInsight에서 Apache Spark 및 Apache Kafka 사용](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight에서 Apache Storm 및 Apache Kafka 사용](../hdinsight-apache-storm-with-kafka.md)
