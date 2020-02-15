---
title: '자습서: Apache Kafka 생산자 및 소비자 API - Azure HDInsight'
description: HDInsight의 Kafka에서 Apache Kafka 생산자 및 소비자 API를 사용하는 방법에 대해 알아봅니다. 이 자습서에서는 Java 애플리케이션에서 HDInsight의 Kafka와 함께 이러한 API를 사용하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 10/08/2019
ms.openlocfilehash: 65fc3259b0bc5fce61ccd1ceb8df30f1bba49b19
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161718"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>자습서: Apache Kafka 생산자 및 소비자 API 사용

HDInsight의 Kafka에서 Apache Kafka 생산자 및 소비자 API를 사용하는 방법에 대해 알아봅니다.

애플리케이션에서 Kafka 생산자 API를 통해 Kafka 클러스터에 데이터 스트림을 보낼 수 있습니다. 애플리케이션에서 Kafka 소비자 API를 통해 클러스터에서 데이터 스트림을 읽을 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 사전 요구 사항
> * 코드 이해
> * 애플리케이션 빌드 및 배포
> * 클러스터에서 애플리케이션 실행

API에 대한 자세한 내용은 [생산자 API](https://kafka.apache.org/documentation/#producerapi) 및 [소비자 API](https://kafka.apache.org/documentation/#consumerapi)에서 Apache 설명서를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight 3.6의 Apache Kafka HDInsight 클러스터에 Kafka를 만드는 방법을 알아보려면 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)을 참조하세요.

* OpenJDK 같은 [JDK(Java 개발자 키트) 버전 8](https://aka.ms/azure-jdks) 또는 그와 동등한 프로그램

* Apache에 따라 올바르게 [설치된](https://maven.apache.org/install.html)[Apache Maven](https://maven.apache.org/download.cgi)  Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

* SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="understand-the-code"></a>코드 이해

예제 애플리케이션은 `Producer-Consumer` 하위 디렉터리의 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에 있습니다. 애플리케이션은 주로 4개의 파일로 구성됩니다.

* `pom.xml`: 이 파일은 프로젝트 종속성, Java 버전 및 패키징 메서드를 정의합니다.
* `Producer.java`: 이 파일은 생산자 API를 사용하여 Kafka에 임의의 문장을 보냅니다.
* `Consumer.java`: 이 파일은 소비자 API를 사용하여 Kafka에서 데이터를 읽고 STDOUT에 내보냅니다.
* `Run.java`: 생산자 및 소비자 코드를 실행하는 데 사용되는 명령줄 인터페이스입니다.

### <a name="pomxml"></a>Pom.xml

`pom.xml` 파일에서 이해할 중요한 사항은 다음과 같습니다.

* 종속성: 이 프로젝트는 `kafka-clients` 패키지에서 제공하는 Kafka 생산자 및 소비자 API에 의존합니다. 다음 XML 코드는 이 종속성을 정의합니다.

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    `${kafka.version}` 항목은 `pom.xml`의 `<properties>..</properties>` 섹션에서 선언되며, HDInsight 클러스터의 Kafka 버전으로 구성됩니다.

* 플러그 인: Maven 플러그 인은 다양한 기능을 제공합니다. 이 프로젝트에서는 다음 플러그 인이 사용됩니다.

    * `maven-compiler-plugin`: 프로젝트에서 사용하는 Java 버전을 8로 설정하는 데 사용됩니다. HDInsight 3.6에서 사용하는 Java 버전입니다.
    * `maven-shade-plugin`: 이 애플리케이션 및 모든 종속성을 포함하는 uber jar를 생성하는 데 사용됩니다. 또한 기본 클래스를 지정하지 않고 Jar 파일을 직접 실행할 수 있도록 애플리케이션의 진입점을 설정하는 데 사용됩니다.

### <a name="producerjava"></a>Producer.java

생산자는 Kafka broker 호스트(작업자 노드)와 통신하고 Kafka 토픽에 데이터를 보냅니다. 다음 코드 조각은 [GitHub 리포지토리](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)의 [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) 파일에서 가져온 것이며 프로시저 속성을 설정하는 방법을 보여줍니다.

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

소비자는 Kafka broker 호스트(작업자 노드)와 통신하고, 루프에서 레코드를 읽습니다. [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) 파일의 다음 코드 조각은 소비자 속성을 설정합니다.

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

이 코드에서 소비자는 토픽의 시작에서 읽도록 구성됩니다(`auto.offset.reset`이 `earliest`로 설정됨).

### <a name="runjava"></a>Run.java

[Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) 파일은 생산자 또는 소비자 코드를 실행하는 명령줄 인터페이스를 제공합니다. 매개 변수로 Kafka broker 호스트 정보를 제공해야 합니다. 필요에 따라 소비자 프로세스에 사용되는 그룹 ID 값을 포함할 수 있습니다. 동일한 그룹 ID를 사용하여 여러 소비자 인스턴스를 만드는 경우 항목에서 읽는 부하를 분산합니다.

## <a name="build-and-deploy-the-example"></a>예제 빌드 및 배포

1. [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에서 예제를 다운로드하여 압축을 풉니다.

2. 현재 디렉터리를 `hdinsight-kafka-java-get-started\Producer-Consumer` 디렉터리 위치로 설정하고 다음 명령을 사용합니다.

    ```cmd
    mvn clean package
    ```

    이 명령은 `kafka-producer-consumer-1.0-SNAPSHOT.jar`라는 파일이 포함된 `target`이라는 디렉터리를 만듭니다.

3. `sshuser`은 클러스터의 SSH 사용자로, `CLUSTERNAME`은 클러스터 이름으로 바꿉니다. 다음 명령을 입력하여 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 파일을 HDInsight 클러스터에 복사합니다. 메시지가 표시되면 SSH 사용자의 암호를 입력합니다.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> 예제 실행

1. `sshuser`은 클러스터의 SSH 사용자로, `CLUSTERNAME`은 클러스터 이름으로 바꿉니다. 다음 명령을 입력하여 클러스터에 대한 SSH 연결을 엽니다. 메시지가 표시되면 SSH 사용자 계정의 암호를 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 간단한 명령줄 JSON 프로세서인 [jq](https://stedolan.github.io/jq/)를 설치합니다. 열린 SSH 연결에서 다음 명령을 실행하여 `jq`를 설치합니다.

    ```bash
    sudo apt -y install jq
    ```

1. 암호 변수를 설정합니다. `PASSWORD`를 클러스터 로그인 암호로 바꾼 다음, 다음 명령을 입력합니다.

    ```bash
    export password='PASSWORD'
    ```

1. 대/소문자가 올바르게 입력된 클러스터 이름을 추출합니다. 클러스터 생성 방법에 따라 클러스터 이름의 실제 대/소문자가 예상과 다를 수 있습니다. 이 명령은 실제 대/소문자를 가져온 다음, 변수에 저장합니다. 다음 명령을 입력합니다.

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```
    > [!Note]  
    > 클러스터 외부에서 이 프로세스를 수행하는 경우 클러스터 이름을 저장하는 다른 절차가 있습니다. Azure Portal에서 소문자로 클러스터 이름을 가져옵니다. 그런 다음, 다음 명령에서 `<clustername>`에 대한 클러스터 이름을 대체하고 `export clusterName='<clustername>'`을 실행합니다.  

1. Kafka broker 호스트를 가져오려면 다음 명령을 사용합니다.

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > 이 명령에는 Ambari 액세스 권한이 필요합니다. 클러스터가 NSG 뒤에 있는 경우 Ambari에 액세스할 수 있는 머신에서 이 명령을 실행합니다.

1. 다음 명령을 입력하여 Kafka 토픽 `myTest`를 만듭니다.

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. 생산자를 실행하고 토픽에 데이터를 쓰려면 다음 명령을 사용합니다.

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. 생산자가 완료되면 다음 명령을 사용하여 토픽에서 읽습니다.

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    레코드 수와 함께 읽은 레코드가 표시됩니다.

1. __Ctrl+C__ 를 사용하여 소비자를 종료합니다.

### <a name="multiple-consumers"></a>여러 소비자

Kafka 소비자는 레코드를 읽을 때 소비자 그룹을 사용합니다. 여러 소비자와 같은 그룹을 사용하면 부하가 분산되어 토픽에서 읽습니다. 그룹의 각 소비자는 레코드의 일부를 받습니다.

소비자 애플리케이션은 그룹 ID로 사용되는 매개 변수를 허용합니다. 예를 들어, 다음 명령은 그룹 ID `myGroup`을 사용하여 소비자를 시작합니다.

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

__Ctrl+C__ 를 사용하여 소비자를 종료합니다.

동작 중인 이 프로세스를 확인하려면 다음 명령을 사용합니다.

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

이 명령은 `tmux`를 사용하여 터미널을 두 개의 열로 분할합니다. 소비자는 동일한 그룹 ID 값으로 각 열에서 시작됩니다. 소비자가 읽기를 완료하면 각 읽기는 레코드의 일부입니다. __Ctrl + C__ 를 두 번 사용하여 `tmux`를 종료합니다.

동일한 그룹 내에서 클라이언트에 의한 소비는 토픽에 대한 파티션을 통해 처리됩니다. 이 코드 샘플에서, 앞에서 만든 `test` 토픽에는 8개의 파티션이 있습니다. 8명의 소비자를 시작하는 경우 각 소비자는 토픽에 대한 단일 파티션에서 레코드를 읽습니다.

> [!IMPORTANT]  
> 소비자 그룹에는 파티션보다 더 많은 소비자 인스턴스가 있을 수 없습니다. 이 예제에서 하나의 소비자 그룹은 토픽의 파티션 수이기 때문에 최대 8개 소비자를 포함할 수 있습니다. 또는 소비자가 8개 이하인 소비자 그룹이 여러 개 있을 수 있습니다.

Kafka에 저장된 레코드는 파티션 내에서 받은 순서대로 저장됩니다. *파티션 내의* 레코드에 대해 순서대로 전달하려면 파티션 수와 일치하는 소비자 인스턴스가 있는 소비자 그룹을 만듭니다. *토픽 내의* 레코드에 대해 순서대로 전달하려면 하나의 소비자 인스턴스만 사용하는 소비자 그룹을 만듭니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 정리하려면 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 연결된 HDInsight 클러스터 및 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

Azure Portal을 사용하여 리소스 그룹을 제거하려면:

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 연 다음 __리소스 그룹__ 을 선택하여 리소스 그룹 목록을 표시합니다.
2. 삭제할 리소스 그룹을 찾은 다음 목록 오른쪽에 있는 __자세히__ 단추(...)를 마우스 오른쪽 단추로 클릭합니다.
3. __리소스 그룹 삭제__ 를 선택한 다음 확인합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight의 Kafka에서 Apache Kafka 생산자 및 소비자 API를 사용하는 방법에 대해 알아보았습니다. Kafka 작업에 대해 자세히 알아보려면 다음을 사용하세요.

> [!div class="nextstepaction"]
> [Apache Kafka 로그 분석](apache-kafka-log-analytics-operations-management.md)
