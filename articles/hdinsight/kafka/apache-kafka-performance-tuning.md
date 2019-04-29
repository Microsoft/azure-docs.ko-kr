---
title: Apache Kafka HDInsight 클러스터에 대 한 성능 최적화
description: Azure HDInsight에서 Apache Kafka 워크 로드를 최적화 하기 위한 기술의 개요를 제공 합니다.
services: hdinsight
author: hrasheed-msft
ms.author: v-yiso
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
origin.date: 02/21/2019
ms.date: 04/29/2019
ms.openlocfilehash: 8226d1f49b8ba73870dba009e97ff2718a0eee27
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115030"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight 클러스터에 대 한 성능 최적화

이 문서에서는 HDInsight에서 Apache Kafka 워크 로드의 성능을 최적화 하기 위한 몇 가지 제안을 제공 합니다. 초점은 broker 구성과 생산자를 조정 합니다. 성능을 측정 하는 방법에 여러 가지 및 최적화를 적용 하는 비즈니스 요구 사항에 따라 달라 집니다.

## <a name="architecture-overview"></a>아키텍처 개요

Kafka 토픽은 레코드를 구성 하는 데 사용 됩니다. 레코드는 생산자에 의해 생성 되며 소비자가 사용 됩니다. 생산자는 다음 데이터를 저장 하는 Kafka 브로커에 레코드를 보냅니다. HDInsight 클러스터의 각 작업자 노드는 Kafka broker입니다.

토픽은 브로커 간에 레코드를 분할합니다. 레코드를 소비할 때 데이터의 병렬 처리를 위해 파티션당 최대 1개의 소비자를 사용할 수 있습니다.

복제는 노드 간에 파티션을 복제 하는 데 사용 됩니다. 이 노드 (브로커) 가동 중단 으로부터 보호합니다. 복제본의 그룹 중에서 단일 파티션에 파티션에 리더로 지정 됩니다. 생산자 트래픽은 ZooKeeper에서 관리하는 상태를 사용하여 각 노드의 선행부로 라우팅됩니다.

## <a name="identify-your-scenario"></a>시나리오 식별

Apache Kafka 성능에는 두 가지 주요 측면 – 처리량 및 대기 시간에 있습니다. 처리량은 데이터를 처리할 수 있는 최대 비율입니다. 높은 처리량이 것이 좋습니다. 대기 시간에 데이터를 저장 하거나 검색 하는 데 걸리는 시간입니다. 낮은 대기 시간은 일반적으로 더 나은입니다. 처리량, 대기 시간 및 응용 프로그램의 인프라 비용 간의 적절 한 균형을 찾기 어려울 수 있습니다. 성능 요구 사항을 다음 세 가지 일반적인 상황에서는 높은 처리량, 짧은 대기 시간, 또는 둘 다 필요 여부에 따라 중 하 나와 일치 가능성이 높습니다.

* 높은 처리량, 짧은 대기 시간입니다. 이 시나리오는 높은 처리량과 짧은 대기 시간 (~ 100 밀리초)에 필요합니다. 이 유형의 응용 프로그램의 예로 가용성 모니터링 서비스입니다.
* 높은 처리량, 높은 대기 시간입니다. 이 시나리오 (~1.5 GBps) 처리량이 높은 하는데 더 높은 대기 시간 (< 250ms)을 허용할 수 있습니다. 이 응용 프로그램 유형의 예에는 원격 분석 데이터 수집에 대 한 보안 및 침입 검색 응용 프로그램과 같은 프로세스를 실시간에 가까운입니다.
* 낮은 처리량, 짧은 대기 시간입니다. 이 시나리오 실시간 처리를 위한 짧은 대기 시간 (< 10ms) 이어야 하는데 더 낮은 처리량을 허용할 수 있습니다. 이 유형의 응용 프로그램의 예로 온라인 맞춤법 및 문법 검사 합니다.

## <a name="producer-configurations"></a>생산자 구성

다음 섹션에 Kafka 생산자의 성능을 최적화 하는 가장 중요 한 구성 속성 중 일부는 강조 표시 됩니다. 에 대 한 자세한 설명은 모든 구성 속성을 참조 하세요 [Apache Kafka 생산자 구성에 대 한 설명서](https://kafka.apache.org/documentation/#producerconfigs)합니다.

### <a name="batch-size"></a>Batch 크기

Apache Kafka 생산자는 단일 저장소 파티션에 저장 될 단위로 전송한 메시지 (일괄 처리 라고 함)의 그룹을 조합 합니다. 일괄 처리 크기는 해당 그룹에 전송 되기 전에 존재 해야 하는 바이트 수를 의미 합니다. 증가 된 `batch.size` 매개 변수 오버 헤드를 네트워크 IO 요청 처리 줄어들기 때문에 처리량을 늘릴 수 있습니다. 부하가 낮은 생산자 준비 일괄 처리에 대 한 대기로 증가 된 일괄 처리 크기 Kafka 송신 대기 시간이 늘어날 수 있습니다. 부하가 것이 좋습니다 처리량 및 대기 시간을 개선 하기 위해 일괄 처리 크기를 늘려야 합니다.

### <a name="producer-required-acknowledgements"></a>필요한 생산자 승인

필요한 생산자 `acks` 쓰기 요청으로 간주 되기 전에 파티션 리더가 필요한 승인 개수를 결정 하는 구성을 완료 합니다. 이 설정은 데이터 안정성 및의 값을 걸리는 `0`, `1`, 또는 `-1`합니다. 변수의 `-1` 쓰기 완료 되기 전에 승인을 모든 복제본에서 수신 되어야 하 의미 합니다. 설정 `acks = -1` 결과 제공 하지만 데이터 손실에 대 한 더 강력한 보증이 더 높은 대기 시간과 낮은 처리량에서. 응용 프로그램 요구 사항에 더 높은 처리량을 요청 하는 경우에 설정 해 보세요 `acks = 0` 또는 `acks = 1`합니다. 모든 복제본을 승인 하지 데이터 안정성을 줄일 수 있는 염두에 둡니다.

### <a name="compression"></a>압축

Kafka 생산자는 메시지 브로커에 게 보내기 전에 압축을 구성할 수 있습니다. `compression.type` 설정은 사용할 압축 코덱입니다. 지원 되는 압축 코덱은 "gzip" "snappy" 및 "lz4." 압축 유용 하며 디스크 용량에 대 한 제한이 없는 경우 고려해 야 합니다.

두 가지 자주 사용 되는 압축 코덱, 간에 `gzip` 하 고 `snappy`, `gzip` 결과적으로 더 높은 CPU 로드 하는 대신 디스크 사용률이 더 높은 압축 비율에 합니다. `snappy` 코덱 CPU 오버 헤드를 덜 사용 하 여 작은 압축을 제공 합니다. 사용 하는 코덱을 broker 디스크 또는 생산자 CPU 제한 사항에 기반을 결정할 수 있습니다. `gzip` 보다 5 배 높은 속도로 데이터를 압축할 수 `snappy`입니다.

데이터 압축을 사용 하 여 디스크에 저장할 수 있는 레코드 수가 증가 합니다. 증가할 수 있습니다도 CPU 오버 헤드의 경우 여기서 생산자와 브로커에서 사용 하 고 압축 형식 간에 일치 하지 않습니다. 데이터를 보내기 전에 압축 하 고 처리 하기 전에 다음 압축을 해제 해야 합니다.

## <a name="broker-settings"></a>브로커 설정

다음 섹션에 Kafka 브로커의 성능을 최적화 하기 위해 가장 중요 한 설정 중 일부를 강조 표시 됩니다. 자세한 설명은 모든 broker 설정을 참조 하세요 [Apache Kafka 생산자 구성에 대 한 설명서](https://kafka.apache.org/documentation/#producerconfigs)합니다.


### <a name="number-of-disks"></a>디스크 수

저장소 디스크 IOPS (입/출력 작업 수 / 초) 제한 및 읽기/쓰기 바이트 수 / 초입니다. 새 파티션을 만들면 Kafka는 각각의 새 파티션을 사용할 수 있는 디스크에 걸쳐 분산에 최소한의 기존 파티션 사용 하 여 디스크에 저장 합니다. 저장소 전략을 수백 개의 각 디스크에 파티션 복제본을 처리 하는 경우에 불구 하 고 Kafka 사용 가능한 디스크 처리량을 쉽게 사용 수 있습니다. 처리량 및 비용 간의 여기 절충이 필요 합니다. 응용 프로그램에 큰 처리량이 필요한 경우 클러스터를 관리 되는 더 만들 broker 당 디스크 수. HDInsight 현재 지원 하지 않습니다 실행 중인 클러스터에 관리 디스크를 추가 합니다. 관리 디스크의 수를 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [HDInsight의 Apache Kafka에 대 한 저장소 및 확장성 구성](apache-kafka-scalability.md)합니다. 클러스터의 노드에 대 한 저장 공간을 늘리거나 비용 문제를 이해 합니다.

### <a name="number-of-topics-and-partitions"></a>항목 및 파티션 수

Kafka 생산자는 항목을 작성합니다. Kafka 소비자는 토픽에서 읽습니다. 항목을 로그를 디스크에 데이터 구조와 연결 됩니다. Kafka는 producer(s) 항목에서는 로그의 끝에 레코드를 추가합니다. 여러 파일에 분산 되어 있는 파티션 수의 항목에서는 로그 구성 됩니다. 이러한 파일에 차례로 여러 Kafka 클러스터 노드에 분산 됩니다. 소비자가 주기로 Kafka 토픽에서 읽어 오며 항목에서는 로그의 위치 (오프셋)를 선택할 수 있습니다.

각 Kafka 파티션은 시스템에 로그 파일 및 생산자 스레드가 동시에 여러 로그에 쓸 수 있습니다. 마찬가지로, 각 소비자 스레드는 한 파티션에서 메시지를 읽고, 이후도 병렬로 처리 됩니다 여러 파티션에서 사용.

파티션 밀도가 (broker 당 파티션 수) 관련 메타 데이터 작업 및 요청/응답 파티션 리더와 해당 팔로 워 간에 파티션 당 오버 헤드를 추가 합니다. 통해 흐르는 데이터가 없을 경우에도 파티션 복제본 송신에 대 한 추가 처리로 인해 네트워크를 통해 요청을 수신 하며 리더 로부터 데이터를 가져옵니다.

Apache Kafka 클러스터 1.1 및 위에 HDInsight, 좋습니다 복제본을 포함 하 여 broker 당 1000 파티션 개까지 포함할 수 있습니다. Broker 당 파티션 수를 늘리면 처리량을 감소 시키고 항목 사용 하지 못하게 될 수도 있습니다. Kafka 파티션 지원에 대 한 자세한 내용은 참조 하세요. [1.1.0 버전에서 지원 되는 파티션의 수를 늘리는에서 공식 Apache Kafka 블로그 게시물](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)합니다. 항목 수정에 대 한 내용은 참조 하세요 [Apache Kafka: 항목 수정](https://kafka.apache.org/documentation/#basic_ops_modify_topic)합니다.

### <a name="number-of-replicas"></a>복제본 수

더 높은 복제 계수 파티션 리더와 팔로 워 추가 요청이 발생합니다. 더 높은 복제 계수로 자세한 디스크 및 추가 요청을 처리 하기 위해 CPU를 소비 하는 결과적으로, 대기 시간 및 처리량을 감소 작성 증가 합니다.

Azure HDInsight에서 Kafka에 대 한 최소 3 배의 복제를 사용 하는 것이 좋습니다. 대부분의 Azure 지역 세 개의 장애 도메인에 있지만 사용자가 두 개의 장애 도메인을 사용 하 여 지역에서 4 배의 복제를 사용 해야 합니다.

복제에 대 한 자세한 내용은 참조 하세요. [Apache Kafka: 복제](https://kafka.apache.org/documentation/#replication) 하 고 [Apache Kafka: 복제 계수를 늘리면](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Apache Kafka를 사용하여 매일 엄청난 수의 이벤트 처리](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [HDInsight의 Apache Kafka란?](apache-kafka-introduction.md)
