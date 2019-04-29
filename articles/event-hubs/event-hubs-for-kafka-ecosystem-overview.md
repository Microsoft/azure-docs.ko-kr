---
title: Apache Kafka 앱에서 이벤트 허브 사용 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에서 지원하는 Apache Kafka에 정보를 제공합니다.
services: event-hubs
documentationcenter: .net
author: shvija
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 8bf381e7c66e06bbaa140ed865f0f7c9b4f001af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821710"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Apache Kafka 애플리케이션에서 Azure Event Hubs 사용
Event Hubs는 기존 Kafka 기반 애플리케이션이 사용자 고유의 Kafka 클러스터 실행에 대한 대안으로 사용할 수 있는 Kafka 끝점을 제공합니다. Event Hubs는 [Apache Kafka 프로토콜 1.0 이상](https://kafka.apache.org/documentation/)을 지원하고 MirrorMaker를 포함하여 기존 Kafka 애플리케이션과 작동합니다.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Kafka용 Event Hubs는 무엇을 제공하나요?

Kafka용 Event Hubs 기능은 Kafka 버전 1.0과 호환가능한 이진이며 나중에 Kafka 토픽에서 읽기 및 쓰기를 위한 Azure Event Hubs에 프로토콜 헤드를 제공합니다. 코드 변경 없이 최소한의 구성 변경으로 애플리케이션에서 Kafka 엔드포인트를 사용하여 시작할 수 있습니다. Kafka 클러스터를 가리키는 대신 이벤트 허브에서 노출된 Kafka 엔드포인트를 가리키도록 구성에서 연결 문자열을 업데이트합니다. 그런 다음, Kafka 프로토콜을 사용하는 애플리케이션에서 Event Hubs로 이벤트 스트리밍을 시작할 수 있습니다. 또한 이 통합은 현재 미리 보기로 제공되는 [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) 같은 프레임워크를 지원합니다. 

개념적으로 Kafka와 Event Hubs는 거의 동일합니다. 둘 다 스트리밍 데이터를 위해 빌드한 분할된 로그입니다. 다음 표에서 Kafka 및 Event Hubs 간 개념을 매핑합니다.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 및 Event Hubs의 개념적 매핑

| Kafka 개념 | Event Hubs 클러스터|
| --- | --- |
| 프로비전 | 네임스페이스 |
| 항목 | 이벤트 허브 |
| 파티션 | 파티션|
| 소비자 그룹 | 소비자 그룹 |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka 및 Event Hubs의 주요 차이점

[Apache Kafka](https://kafka.apache.org/)는 선택한 어디서든 실행할 수 있는 소프트웨어인 반면 Event Hubs는 Azure Blob Storage와 유사한 클라우드 서비스입니다. 관리할 네트워크나 서버가 없으며 구성할 브로커가 없습니다. 항목이 포함된 FQDN인 네임스페이스를 만든 다음, 해당 네임스페이스 내에 항목 또는 Event Hubs를 만듭니다. Event Hubs 및 네임스페이스에 대한 자세한 내용은 [Event Hubs 기능](event-hubs-features.md#namespace)을 참조하세요. 클라우드 서비스로서 Event Hubs는 안정적인 단일 가상 IP 주소를 엔드포인트로 사용하므로 클라이언트는 클러스터 내에서 브로커 또는 컴퓨터에 대해 알 필요가 없습니다. 

Event Hubs에서 확장은 구매한 처리량 단위 수에 의해 제어되며, 각 처리량 단위는 초당 1MB 또는 초당 이벤트 1000개를 수신할 수 있습니다. 기본적으로 Event Hubs는 [자동 확장](event-hubs-auto-inflate.md) 기능으로 한계에 도달할 경우 처리량 단위를 확장합니다. 이 기능은 Kafka용 Event Hubs 기능과 함께 작동합니다. 

### <a name="security-and-authentication"></a>보안 및 인증

Azure Event Hubs는 모든 통신에 SSL 또는 TLS를 필요로 하며 인증을 위해 SAS(공유 액세스 서명)를 사용합니다. 이 요구 사항은 Event Hubs 내의 Kafka 엔드포인트에 대해서도 마찬가지입니다. Kafka와 호환성을 위해 Event Hubs는 인증에 SASL PLAIN 그리고 전송 보안에 SASL SSL을 사용합니다. Event Hubs의 보안에 대한 자세한 내용은 [Event Hubs 인증 및 보안](event-hubs-authentication-and-security-model-overview.md)을 참조합니다.

## <a name="other-event-hubs-features-available-for-kafka"></a>Kafka에 사용할 수 있는 기타 Event Hubs 기능

Kafka용 Event Hubs 기능을 사용하면 한 프로토콜로 읽고 다른 프로토콜로 쓸 수 있으므로 현재 Kafka 생산자는 Kafka를 통해 게시를 계속할 수 있으며 Azure Stream Analytics 또는 Azure Functions 같은 Event Hubs를 사용하여 판독기를 추가할 수도 있습니다. 또한 [캡처](event-hubs-capture-overview.md) 및 [지역 재해 복구](event-hubs-geo-dr.md) 같은 Event Hubs 기능은 Kafka용 Event Hubs 기능과 함께 작동합니다.

## <a name="features-that-are-not-yet-supported"></a>아직 지원되지 않는 기능 

다음은 아직 지원되지 않는 Kafka 기능 목록입니다.

*   Idempotent 생산자
*   트랜잭션
*   압축
*   크기 기반 보존
*   로그 압축
*   파티션을 기존 항목에 추가
*   HTTP Kafka API 지원
*   Kafka 스트림

## <a name="next-steps"></a>다음 단계

이 문서는 Kafka용 Event Hubs에 대한 소개를 제공합니다. 자세한 알아보려면 다음 링크를 참조하세요.

- [Kafka 사용 Event Hubs 만드는 방법](event-hubs-create-kafka-enabled.md)
- [Kafka 애플리케이션에서 이벤트 허브로 스트리밍](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka 지원 이벤트 허브에서 Kafka broker 미러링](event-hubs-kafka-mirror-maker-tutorial.md)
- [Kafka 지원 이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md)
- [Kafka 지원 이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md)
- [Kafka 지원 이벤트 허브에 Kafka Connect 통합](event-hubs-kafka-connect-tutorial.md)
- [Kafka 지원 이벤트 허브에 Akka Streams 연결](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub에서 더 많은 샘플 탐색](https://github.com/Azure/azure-event-hubs-for-kafka)


