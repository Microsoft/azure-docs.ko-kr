---
title: YARN에서 고가용성 Spark Streaming 작업 만들기 - Azure HDInsight
description: 고가용성 시나리오에 대한 Spark Streaming을 설정하는 방법
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/26/2018
ms.openlocfilehash: 79a36ad39284dc66467ba7c500a363668f78b893
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098831"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>YARN을 사용하여 고가용성 Apache Spark Streaming 작업 만들기

[Apache Spark](https://spark.apache.org/) Streaming을 사용하여 데이터 스트림 처리를 위한 확장 가능하고 처리량이 높은 내결함성 애플리케이션을 구현할 수 있습니다. HDInsight Spark 클러스터의 Spark Streaming 애플리케이션을 Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), 원시 TCP 소켓과 같은 다양한 데이터 원본에 연결할 수 있고 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) 파일 시스템을 모니터링해서 변경을 확인할 수 있습니다. Spark Streaming은 노드 실패가 발생하더라도 지정된 이벤트가 정확히 1번 처리되도록 함으로써 내결함성을 지원합니다.

Spark Streaming은 데이터에 변환을 적용한 다음, 해당 결과를 파일 시스템, 데이터베이스, 대시보드 및 콘솔에 푸시할 수 있는 장기 실행 작업을 만듭니다. Spark Streaming은 정의된 시간 간격 동안 이벤트 일괄 처리를 우선 수집하여 데이터를 마이크로 단위로 일괄로 처리합니다. 다음으로, 처리 및 출력을 위해 해당 일괄 처리가 전송됩니다. 일괄 처리 시간 간격은 일반적으로 소수 초 단위로 정의됩니다.

![Spark 스트리밍](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Spark Streaming은 *불연속화 스트림*(DStream)을 사용하여 데이터의 연속 스트림을 나타냅니다. 이 DStream은 Event Hubs 또는 Kafka와 같은 입력 원본에서 또는 다른 DStream에 변환을 적용하여 만들 수 있습니다. 이벤트가 Spark Streaming 애플리케이션에 도착하면 이벤트는 신뢰할 수 있는 방식으로 저장됩니다. 즉, 이벤트 데이터는 복제되어 여러 노드에 저장됩니다. 따라서 단일 노드 실패로 인한 이벤트 손실이 발생되지 않게 됩니다.

Spark 코어는 RDD(*복원력 있는 분산 데이터 세트*)를 사용합니다. RDD는 클러스터의 여러 노드에 걸쳐 데이터를 배포하여, 각 노드가 최상의 성능을 위해 메모리 내에 데이터를 완전한 상태로 유지하도록 합니다. 각 RDD는 일괄 처리 간격 동안 수집된 이벤트를 나타냅니다. 일괄 처리 간격이 지나면 Spark Streaming은 해당 간격의 모든 데이터를 포함하는 새 RDD을 생성합니다. RDD의 연속 집합은 DStream으로 수집됩니다. Spark Streaming 애플리케이션은 각 일괄 처리의 RDD에 저장된 데이터를 처리합니다.

![Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark Structured Streaming 작업

Spark Structured Streaming은 구조화된 데이터를 스트리밍하는 데 사용하기 위한 분석 엔진으로 Spark 2.0에 도입되었습니다. Spark Structured Streaming은 SparkSQL 일괄 처리 엔진 API를 사용합니다. Spark Streaming과 마찬가지로, Spark Structured Streaming은 연속해서 도착하는 데이터의 마이크로 일괄 처리에 대해 계산을 실행합니다. Spark Structured Streaming은 데이터 스트림을 무제한 행이 포함된 입력 테이블로 나타냅니다. 즉, 입력 테이블은 새 데이터가 도착하면 계속 커집니다. 이 입력 테이블은 장기 실행 쿼리를 통해 연속해서 처리되며, 결과는 출력 테이블에 기록됩니다.

![Spark Structured Streaming](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Structured Streaming에서 데이터는 시스템에 도착하는 즉시, 입력 테이블에 수집됩니다. 이 입력 테이블에 대해 작업을 수행하는 쿼리를 작성합니다. 쿼리 결과는 결과 테이블이라는 또 다른 테이블을 생성합니다. 결과 테이블은 쿼리의 결과를 포함합니다. 사용자는 여기에서 데이터를 인출하여 관계형 데이터베이스와 같은 외부 데이터 저장소로 전송하게 됩니다. *트리거 간격*은 입력 테이블에서 데이터거 처리되는 타이밍을 설정합니다. 기본적으로 Structured Streaming은 데이터가 도착하는 즉시, 처리합니다. 그러나 좀 더 긴 간격으로 실행되도록 트리거를 구성하여 스트리밍 데이터가 시간에 따라 일괄로 처리되도록 할 수도 있습니다. 스트리밍 쿼리가 시작된 이후의 모든 출력 데이터를 포함하거나(*완료 모드*), 쿼리가 마지막으로 처리된 이후에 새로 추가된 데이터만 포함하도록(*추가 모드*) 하여 새 데이터가 있을 때마다 결과 테이블의 데이터를 완전히 새로 고칠 수 있습니다 .

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>내결함성 Spark Streaming 작업 만들기

Spark Streaming 작업에 대한 고가용성 환경을 만들려면, 먼저 오류 발생 시 복구할 수 있게 개별 작업을 코딩합니다. 이러한 자동 복구 작업은 내결함성을 갖습니다.

RDD는 고가용성의 내결함성 Spark Streaming 작업을 지원하는 다음과 같은 몇 가지 속성을 갖습니다.

* DStream으로 RDD에 저장된 입력 데이터 일괄 처리는 내결함성을 위해 메모리에 자동으로 복제됩니다.
* 작업자 실패로 인해 손실된 데이터는 해당 작업자 노드를 사용할 수 있기만 하면, 다른 작업자의 복제된 입력 데이터에서 다시 계산될 수 있습니다.
* 메모리 내 계산을 통해 오류/지연에서 복구되면 1초 이내에 빠른 오류 복구가 발생할 수 있습니다.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Spark Streaming을 사용하여 정학히 한 번만 처리하는 의미 체계

각 이벤트를 한 번만 처리하는 애플리케이션을 만들려면 모든 시스템 실패 지점이 문제 발생 후 다시 시작되는 방식과 데이터 손실을 피할 수 있는 방식을 고려하세요. 정확하게 한 번만 처리하는 의미 체계에서는 어느 순간에도 데이터 손실이 없으며 오류 발생 위치에 관계 없이 메시지 처리는 다시 시작 가능해야 합니다. [이벤트를 정확하게 한 번만 처리하는 Spark 스트리밍 작업 만들기](apache-spark-streaming-exactly-once.md)를 참조하세요.

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark 스트리밍 및 Apache Hadoop YARN

HDInsight에서 클러스터 작업은 YARN(*Yet Another Resource Negotiator*)에 의해 조정됩니다. Spark Streaming에 대한 고가용성을 디자인할 때는 Spark Streaming 기법과 YARN 구성 요소에 대한 기술이 사용됩니다.  YARN을 사용하는 예제 구성은 아래와 같습니다. 

![YARN 아키텍처](./media/apache-spark-streaming-high-availability/yarn-arch.png)

다음 섹션에서는 이 구성에 대한 디자인 고려 사항을 설명합니다.

### <a name="plan-for-failures"></a>오류에 대한 계획

고가용성을 위한 YARN 구성을 만들려면 가능한 실행기 또는 드라이버 실패에 대비해서 계획해야 합니다. 일부 Spark Streaming 작업에는 추가 구성 및 설정이 필요한 데이터 보장 요구 사항도 포함되어 있습니다. 예를 들어, 스트리밍 애플리케이션은 호스팅 스트리밍 시스템 또는 HDInsight 클러스터에서 발생하는 오류에도 불구하고, 제로 데이터 손실을 보장하기 위한 비즈니스 요구 사항이 있을 수 있습니다.

**실행기**가 실패하면 Spark에서 해당 작업 및 수신기를 자동으로 다시 시작하므로, 필요한 구성 변경이 없습니다.

그러나 **드라이버**가 실패하면 관련 실행기가 모두 실패하고, 수신된 모든 블록 및 계산 결과가 손실됩니다. 드라이버 오류에서 복구하려면 [이벤트를 정확하게 한 번만 처리하는 Spark 스트리밍 작업 만들기](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers)에 설명되어 있는 *DStream 검사점*을 사용합니다. DStream 검사점은 DStream의 DAG(*방향성 비순환 그래프*)를 Azure Storage와 같은 내결함성 있는 스토리지에 주기적으로 저장합니다.  검사점 기능은 Spark Structured Streaming이 검사점 정보에서 실패한 드라이버를 다시 시작할 수 있도록 합니다.  이러한 드라이버 다시 시작으로 인해 새 실행기가 시작되고 수신기도 다시 시작됩니다.

DStream 검사점을 사용하여 드라이버를 복구하려면

* 구성 설정 `yarn.resourcemanager.am.max-attempts`를 사용하여 YARN에서 드라이버 자동 다시 시작을 구성합니다.
* `streamingContext.checkpoint(hdfsDirectory)`를 사용하여 HDFS 호환 파일 시스템에서 검사점 디렉터리를 설정합니다.
* 복구를 위해 검사점을 사용하도록 소스 코드를 재구성합니다. 예를 들면 다음과 같습니다.

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`를 사용하여 WAL(미리 쓰기 로그)을 사용하도록 설정하여 손실된 데이터 복구를 구성하고, `StorageLevel.MEMORY_AND_DISK_SER`을 사용하여 입력 DStream에 대한 메모리 내 복제를 사용하지 않도록 설정합니다.

요약하면, 검사점 + WAL + 신뢰할 수 있는 수신기를 사용하여, 다음과 같은 "최소 한 번의" 데이터 복구를 전달할 수 있습니다.

* 정확히 한 번. 단, 수신된 데이터가 손실되지 않고 출력이 idempotent 또는 트랜잭션 방식이어야 합니다.
* 정확히 한 번. 수신기 또는 WAL을 사용하지 않고, Kafka를 복제된 로그로 사용하는 새로운 Kafka 직접 방식을 사용합니다.

### <a name="typical-concerns-for-high-availability"></a>고가용성에 대한 일반적인 문제

* 일괄 처리 작업보다 스트리밍 작업을 모니터링하는 것이 좀 더 어렵습니다. Spark Streaming 작업은 일반적으로 오랜 기간 실행되며, YARN은 작업이 완료될 때까지 로그를 집계하지 않습니다.  Spark 검사점은 애플리케이션 또는 Spark 업그레이드 동안 손실되며, 업그레이드하는 동안 검사점 디렉터리를 지워야 합니다.

* 클라이언트가 실패하는 경우에 드라이버를 실행하도록 YARN 클러스터 모드를 구성합니다. 드라이버의 자동 다시 시작을 설정하려면

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark 및 Spark Streaming UI에는 구성 가능한 메트릭 시스템이 있습니다. Graphite/Grafana와 같은 추가 라이브러리를 사용하여 '처리된 숫자 메트릭', '드라이브 및 실행기의 메모리/GC 사용량', '총 지연', '클러스터 활용률' 등의 대시보드 메트릭을 다운로드할 수도 있습니다. Structured Streaming 버전 2.1 이상에서는 `StreamingQueryListener`를 사용하여 추가 메트릭을 수집할 수 있습니다.

* 장기 실행 작업은 분할해야 합니다.  Spark Streaming 애플리케이션이 클러스터에 제출될 때 작업이 실행되는 YARN 큐를 정의해야 합니다. [YARN Capacity Scheduler](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)를 사용하여 장기 실행 작업을 별도 큐에 제출할 수 있습니다.

* 스트리밍 애플리케이션을 정상적으로 종료합니다. 오프셋을 알고 있으며, 모든 애플리케이션 상태가 외부에 저장되는 경우 적절한 위치에서 프로그래밍 방식으로 스트리밍 애플리케이션을 중지할 수 있습니다. 한 가지 방법은 *n*초 간격으로 외부 플래그를 확인하여 Spark에서 "스레드 후크"를 사용하는 것입니다. 애플리케이션을 시작할 때 HDFS에서 만들어진 후, 사용자가 중지하려고 할 때 제거되는 *마커 파일*을 사용할 수도 있습니다. 마커 파일 방식을 위해서는, 다음과 비슷한 코드를 호출하는 별도 스레드를 Spark 애플리케이션에서 사용하세요.

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>다음 단계

* [Apache Spark Streaming 개요](apache-spark-streaming-overview.md)
* [이벤트를 정확하게 한 번만 처리하는 Apache Spark 스트리밍 작업 만들기](apache-spark-streaming-exactly-once.md)
* [YARN의 장기 실행 Apache Spark Streaming 작업](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [구조적 스트리밍: 내결함성 의미 체계](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [불연속화 스트림: 확장 가능한 스트림 처리를 위한 내결함성 모델](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
