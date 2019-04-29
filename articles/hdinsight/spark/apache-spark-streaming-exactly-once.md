---
title: 이벤트를 정확하게 한 번만 처리하는 Spark 스트리밍 작업 만들기 - Azure HDInsight
description: 이벤트를 오직 한 번만 처리하도록 Spark 스트리밍을 설정하는 방법.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 388723624fde73899809b95ff8ae4ee23cf49a9d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108969"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>이벤트를 정확하게 한 번만 처리하는 Apache Spark 스트리밍 작업 만들기

스트림 처리 애플리케이션은 시스템에 일부 오류가 발생한 후에 재처리 메시지를 어떻게 다룰지에 대해 여러 다른 방법을 취합니다.

* 최소 한 번: 각 메시지는 확실히 처리되지만 두 번 이상 처리될 수도 있습니다.
* 최대 한 번: 각 메시지는 처리될 수도 있고 처리되지 않을 수도 있습니다. 메시지가 처리되는 경우 한 번만 처리됩니다.
* 정확히 한 번만: 각 메시지는 오직 한 번만 처리되도록 보장됩니다.

이 아티클은 정확히 한 번만 처리하도록 하기 위해 Spark Streaming을 구성하는 방법을 보여줍니다.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Apache Spark Streaming을 사용하여 정확히 한 번만 처리하는 의미 체계

먼저, 모든 시스템의 오류 발생 시점에 문제가 발생한 후 어떻게 다시 시작하는지와 데이터 손실을 어떻게 방지할 수 있는지에 대해 고려합니다. Spark 스트리밍 애플리케이션에는 다음과 같은 구성 성분이 있습니다.

* 입력 원본
* 입력 원본에서 데이터를 끌어올 하나 이상의 수신기 프로세스
* 데이터를 처리하는 작업
* 출력 싱크
* 장기 실행 작업을 관리하는 드라이버 프로세스

정확하게 한 번만 처리하는 의미 체계에서는 어느 순간에도 데이터 손실이 없으며 오류 발생 위치에 관계 없이 메시지 처리는 다시 시작 가능해야 합니다.

### <a name="replayable-sources"></a>재생 가능한 원본

Spark Streaming 애플리케이션이 이벤트를 읽어 들이는 원본은 *재생 가능*해야 합니다. 즉, 메시지가 검색되지만 메시지를 유지하거나 처리할 수 있기도 전에 시스템이 실패하는 경우 원본은 같은 메시지를 다시 제공해야 합니다.

Azure에서 HDInsight의 Azure Event Hubs와 [Apache Kafka](https://kafka.apache.org/)는 재생 가능한 원본을 제공합니다. 재생 가능한 원본의 또 다른 예는 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), Azure Storage Blob 또는 Azure Data Lake Storage와 같은 내결함성 파일 시스템으로, 이곳에 모든 데이터가 영원히 보관되고 언제든지 데이터 전체를 다시 읽을 수 있습니다.

### <a name="reliable-receivers"></a>신뢰할 수 있는 수신기

Spark 스트리밍에서 Event Hubs나 Kafka 같은 원본에는 *신뢰할 수 있는 수신기*가 있으며, 각 수신기는 원본을 읽는 진행을 추적합니다. 신뢰할 수 있는 수신기는 HDFS에 기록되는 [Apache ZooKeeper](https://zookeeper.apache.org/) 또는 Spark Streaming 검사점 내에 있는 내결함성 스토리지에 그 상태를 유지합니다. 그같은 수신기가 실패하고 나중에 다시 시작된다면 중지된 부분으로 계속할 수 있습니다.

### <a name="use-the-write-ahead-log"></a>Write-Ahead Log 사용

Spark 스트리밍은 Write-Ahead Log의 사용을 지원하는데 받은 이벤트는 각기 먼저 내결함성 저장소에 있는 Spark의 검사점 디렉터리에 기록된 다음 Resilient Distributed Dataset(RDD)에 저장됩니다. Azure에서 내결함성이 있는 스토리지는 Azure Storage 또는 Azure Data Lake Storage에서 지원하는 HDFS입니다. Spark 스트리밍 애플리케이션에서 Write-Ahead Log는 `spark.streaming.receiver.writeAheadLog.enable` 구성 설정을 `true`에 설정하여 모든 수신기에 대해 사용하도록 설정됩니다. Write-Ahead Log는 드라이버와 실행기의 오류에 대한 내결함성을 제공합니다.

이벤트 데이터에 대한 작업을 실행하는 작업자의 경우 각 RDD는 그 자체로 여러 작업자에게 복제되고 배포됩니다. 그것을 실행하는 작업자가 크래시했기 때문에 작업이 실패하면, 작업은 이벤트 데이터의 복제본이 있는 또 다른 작업자에서 다시 시작될 것이므로 이벤트는 손실되지 않습니다.

### <a name="use-checkpoints-for-drivers"></a>드라이버에 대한 검사점 사용

작업 드라이버는 다시 시작 가능해야 합니다. Spark 스트리밍 애플리케이션을 실행하는 드라이버가 크래시할 경우, 모든 실행 중인 수신기, 작업 및 이벤트 데이터를 저장하는 모든 RDD가 작동 중지됩니다. 이 경우 나중에 다시 시작할 수 있도록 작업의 진행 상태를 저장할 수 있어야 합니다. 이 작업은 DStream의 방향성 비순환 그래프(DAG)에 검사점을 내결함성 저장소에 주기적으로 설정하여 수행합니다. DAG 메타데이터는 스트리밍 애플리케이션, 애플리케이션을 정의하는 작업 및 완료되지 않고 대기 중인 모든 일괄 처리를 만드는 데 사용되는 구성을 포함합니다. 이 메타데이터는 실패한 드라이버를 검사점 정보로부터 다시 시작될 수 있도록 할 수 있습니다. 드라이버가 다시 시작될 때 스스로 이벤트 데이터를 Write-Ahead Log에서 RDD로 복구하는 새 수신기를 시작합니다.

검사점은 Spark 스트리밍에서 두 단계를 거쳐 사용할 수 있습니다. 

1. StreamingContext 개체에서 검사점에 대한 저장소 경로를 구성합니다.

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    HDInsight에서 이러한 검사점은 클러스터에 연결된 기본 스토리지인 Azure Storage 또는 Azure Data Lake Storage에 저장해야 합니다.

2. 그런 다음 DStream에서 검사점 간격(초)을 지정합니다. 각 간격에서 입력 이벤트에서 파생된 상태 데이터는 저장소에 유지합니다. 지속된 상태 데이터는 소스 이벤트에서 상태를 다시 빌드할 때 필요한 계산을 줄일 수 있습니다.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Idempotent 싱크 사용

작업이 결과를 작성하는 대상 싱크는 같은 결과가 두 번 이상 주어지는 상황을 처리할 수 있어야 합니다. 싱크는 그러한 중복 결과를 감지하여 무시할 수 있어야 합니다. *idempotent* 싱크는 상태 변경 없이 동일한 데이터로 여러 번 호출할 수 있습니다.

데이터 저장소에 들어오는 결과가 있는지 먼저 확인하는 논리를 구현하여 idempotent 싱크를 만들 수 있습니다. 결과가 이미 존재한다면 쓰기가 Spark 작업의 관점에서 성공한 것으로 나타나야 하지만 실제로 데이터 저장소는 중복 데이터를 무시합니다. 결과가 존재하지 않으면 싱크는 이 새로운 결과를 저장소에 삽입해야 합니다. 

예를 들어 이벤트를 테이블에 삽입하는 Azure SQL Database와 함께 저장 프로시저를 사용할 수 있습니다. 이 저장 프로시저는 먼저 키 필드로 이벤트를 찾아보고 일치하는 이벤트가 없을 경우에만 레코드가 테이블에 삽입됩니다.

또 다른 예는 Azure Storage Blob이나 Azure Data Lake Storage와 같은 분할된 파일 시스템을 사용하는 것입니다. 이 경우 싱크 논리는 파일의 존재 여부를 확인할 필요가 없습니다. 이벤트를 나타내는 파일이 존재하면 단순히 동일한 데이터로 덮어씁니다. 그렇지 않은 경우 새 파일이 지정된 경로에 만들어집니다.

## <a name="next-steps"></a>다음 단계

* [Apache Spark Streaming 개요](apache-spark-streaming-overview.md)
* [Apache Hadoop YARN에서 고가용성 Apache Spark Streaming 작업 만들기](apache-spark-streaming-high-availability.md)
