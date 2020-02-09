---
title: 질문과 대답 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에 대한 FAQ(질문과 대답) 목록 및 그에 대한 답변을 제공합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 3b46c574ea47622ec97e70c0d2f2cdc3aa54ec0d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706388"
---
# <a name="event-hubs-frequently-asked-questions"></a>Event Hubs 질문과 대답

## <a name="general"></a>일반

### <a name="what-is-an-event-hubs-namespace"></a>Event Hubs 네임스페이스란?
네임스페이스는 Event Hub/Kafka 토픽에 대한 범위 지정 컨테이너입니다. 고유한 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)을 제공합니다. 네임스페이스는 여러 Event Hub/Kafka 토픽을 저장할 수 있는 애플리케이션 컨테이너로 사용됩니다. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>새 네임 스페이스를 만들고 기존 네임 스페이스를 사용 하는 경우
용량 할당 ([tu) (처리량 단위](#throughput-units))은 네임 스페이스 수준에서 청구 됩니다. 네임 스페이스는 영역에도 연결 되어 있습니다.

다음 시나리오 중 하나에서 기존 항목을 사용 하는 대신 새 네임 스페이스를 만들 수 있습니다. 

- 새 지역과 연결 된 Event Hub가 필요 합니다.
- 다른 구독과 연결 된 이벤트 허브가 필요 합니다.
- 고유한 용량 할당이 있는 이벤트 허브가 필요 합니다. 즉, 추가 된 이벤트 허브가 포함 된 네임 스페이스의 용량 요구는 40 TU 임계값을 초과 하 고 전용 클러스터에 대해 이동 하지 않으려고 합니다.  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Event Hubs 기본 및 표준 계층 간의 차이는 무엇입니까?

Azure Event Hubs의 표준 계층은 기본 계층에서 제공할 수 있는 것보다 많은 기능을 제공합니다. 다음 기능이 표준에 포함됩니다.

* 더 길어진 이벤트 보존 기간
* 추가 조정된 연결, 포함된 수보다 많은 경우 초과분 요금 있음
* 단일 [소비자 그룹](event-hubs-features.md#consumer-groups) 초과
* [캡처](event-hubs-capture-overview.md)
* [Kafka 통합](event-hubs-for-kafka-ecosystem-overview.md)

전용 Event Hubs를 비롯한 가격 책정 계층에 대한 자세한 내용은 [Event Hubs 가격 책정 정보](https://azure.microsoft.com/pricing/details/event-hubs/)를 참조하세요.

### <a name="where-is-azure-event-hubs-available"></a>어디에서 Azure Event Hubs를 사용할 수 있나요?

Azure Event Hubs는 지원되는 모든 Azure 지역에서 사용할 수 있습니다. 목록은 [Azure 지역](https://azure.microsoft.com/regions/)를 참조하세요.  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>단일 AMQP 연결을 사용하여 여러 이벤트 허브에서 보내고 받을 수 있나요?

예, 모든 이벤트 허브가 동일한 네임스페이스에 있는 경우 가능합니다.

### <a name="what-is-the-maximum-retention-period-for-events"></a>이벤트의 최대 보존 기간은 어떻게 되나요?

Event Hubs 표준 계층은 현재 최대 7일의 보존 기간을 지원합니다. Event hubs는 영구 데이터 저장소로 사용 되지 않습니다. 24 시간 보다 큰 보존 기간은 이벤트 스트림을 동일한 시스템으로 재생 하는 데 편리한 시나리오를 위한 것입니다. 예를 들어 기존 데이터에서 새로운 기계 학습 모델을 학습 하거나 확인 합니다. 7일을 초과하여 메시지를 보존해야 하는 경우 Event Hubs에서 [Event Hubs 캡처](event-hubs-capture-overview.md)를 사용하도록 설정하면 Event Hubs의 데이터를 선택한 Storage 계정 또는 Azure Data Lake 서비스 계정으로 가져옵니다. 캡처를 사용하도록 설정하면 구매한 처리량 단위에 따라 요금이 부과됩니다.

저장소 계정에서 캡처된 데이터의 보존 기간을 구성할 수 있습니다. Azure Storage의 **수명 주기 관리** 기능은 범용 v2 및 blob Storage 계정에 대한 다양 한 규칙 기반 정책을 제공 합니다. 정책을 사용하여 데이터를 적절한 액세스 계층으로 전환하거나 데이터의 수명 주기 후에 만료합니다. 자세한 내용은 [Azure Blob 저장소 수명 주기 관리](../storage/blobs/storage-lifecycle-management-concepts.md)를 참조 하세요. 

### <a name="how-do-i-monitor-my-event-hubs"></a>내 Event Hubs를 모니터링하려면 어떻게 할까요?
Event Hubs는 [Azure Monitor](../azure-monitor/overview.md)에 리소스 상태를 제공하는 자세한 메트릭을 내보냅니다. 또한 네임스페이스 수준뿐만 아니라 엔터티 수준에서도 Event Hubs 서비스의 전반적인 상태를 평가할 수 있습니다. [Azure Event Hubs](event-hubs-metrics-azure-monitor.md)에 제공되는 모니터링에 대해 알아보세요.

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>방화벽에서 열어야 하는 포트는 무엇 인가요? 
Azure Service Bus에서 다음 프로토콜을 사용 하 여 메시지를 보내고 받을 수 있습니다.

- AMQP(고급 메시지 큐 프로토콜)
- http
- Apache Kafka

이러한 프로토콜을 사용 하 여 Azure Event Hubs와 통신 하기 위해 열어야 하는 아웃 바운드 포트는 다음 표를 참조 하세요. 

| 프로토콜 | 포트 | 세부 정보 | 
| -------- | ----- | ------- | 
| AMQP | 5671 및 5672 | [Amqp 프로토콜 가이드](../service-bus-messaging/service-bus-amqp-protocol-guide.md) 를 참조 하세요. | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | [Kafka 응용 프로그램에서 Event Hubs 사용을](event-hubs-for-kafka-ecosystem-overview.md) 참조 하세요.

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>허용 목록 하는 데 필요한 IP 주소는 무엇 인가요?
연결에 대한 적절 한 IP 주소 목록을 찾으려면 다음 단계를 수행 합니다.

1. 명령 프롬프트에서 다음 명령을 실행 합니다. 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. `Non-authoritative answer`에서 반환 된 IP 주소를 적어둡니다. 다른 클러스터에 네임 스페이스를 복원 하는 경우에만 변경 될 수 있습니다.

네임 스페이스에 영역 중복성을 사용 하는 경우 몇 가지 추가 단계를 수행 해야 합니다. 

1. 먼저 네임 스페이스에서 nslookup을 실행 합니다.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. 다음 형식 중 하나에 해당 하는 **신뢰할 수 없는 응답** 섹션의 이름을 적어둡니다. 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. 접미사 s1, s2 및 s 3이 포함 된 각 항목에 대해 nslookup을 실행 하 여 세 개의 가용성 영역에서 실행 되는 세 인스턴스의 IP 주소를 가져옵니다. 

## <a name="apache-kafka-integration"></a>Apache Kafka 통합

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>기존 Kafka 애플리케이션을 Event Hubs와 통합하려면 어떻게 할까요?
Event Hubs는 기존 Apache Kafka 기반 애플리케이션에서 사용할 수 있는 Kafka 엔트포인트를 제공합니다. PaaS Kafka 환경을 갖추려면 구성만 변경하면 됩니다. 이 구성은 사용자 고유의 Kafka 클러스터를 실행하기 위한 대안을 제공합니다. Event Hubs는 Apache Kafka 1.0 및 최신 클라이언트 버전을 지원하고, 기존 Kafka 애플리케이션, 도구 및 프레임워크와 작동합니다. 자세한 내용은 [Kafka용 Event Hubs 리포지토리](https://github.com/Azure/azure-event-hubs-for-kafka)를 참조하세요.

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>기존 애플리케이션에서 Event Hubs와 통신하려면 어떤 구성을 변경해야 하나요?
Kafka 사용 Event Hub에 연결하려면 Kafka 클라이언트 구성을 업데이트해야 합니다. 이 작업을 수행 하려면 Event Hubs 네임 스페이스를 만들고 [연결 문자열](event-hubs-get-connection-string.md)을 가져옵니다. Event Hubs FQDN과 포트에서 9093을 가리키도록 bootstrap.servers를 변경합니다. 다음과 같이 올바른 인증을 사용 하 여 Kafka 클라이언트를 Kafka 사용 Event Hubs 엔드포인트 (가져온 연결 문자열)으로 보내도록 sasl을 업데이트 합니다.

bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

예제:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

참고: 사용자의 프레임 워크에서 sasl. n e t. .config가 지원 되는 구성이 아닌 경우 SASL 사용자 이름 및 암호를 설정 하는 데 사용 되는 구성을 찾아 대신 사용 합니다. 사용자 이름은 $ConnectionString으로 설정하고, 암호는 Event Hubs 연결 문자열로 설정합니다.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Kafka 사용 Event Hubs의 메시지/이벤트 크기는 어떻게 되나요?
Kafka 사용 Event Hubs에 허용 되는 최대 메시지 크기는 1mb입니다.

## <a name="throughput-units"></a>처리량 단위

### <a name="what-are-event-hubs-throughput-units"></a>Event Hubs 처리량 단위는 무엇입니까?
Event Hubs의 처리량은 Event Hubs를 통해 들어오고 나가는 1KB 이벤트의 데이터 양(메가바이트 단위) 또는 수(1,000개 단위)를 정의합니다. 이 처리량은 TU(처리량 단위)로 측정됩니다. Event Hubs 서비스를 사용하려면 먼저 TU를 구입해야 합니다. 포털 또는 Event Hubs Resource Manager 템플릿을 사용하여 Event Hubs TU를 명시적으로 선택할 수 있습니다. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>처리량 단위는 네임스페이스의 모든 이벤트 허브에 적용되나요?
예, TU(처리량 단위)는 Event Hubs 네임스페이스의 모든 이벤트 허브에 적용됩니다. 즉, 네임스페이스 수준에서 TU를 구입하고, 해당 네임스페이스에 속한 이벤트 허브 간에 공유됩니다. 각 TU에서 네임스페이스에 제공하는 기능은 다음과 같습니다.

- 초당 최대 1MB의 수신 이벤트(이벤트 허브로 전송된 이벤트), 단 초당 1,000개 이하의 수신 이벤트, 관리 작업 또는 제어 API 호출
- 초당 최대 2MB의 송신 이벤트(이벤트 허브에서 사용된 이벤트), 단 4096개를 초과하지 않는 송신 이벤트.
- 최대 84GB의 이벤트 스토리지(기본 24시간 보존 기간에 충분).

### <a name="how-are-throughput-units-billed"></a>처리량 단위는 어떻게 청구되나요?
TU(처리량 단위)는 시간 단위로 청구됩니다. 청구는 지정된 시간 동안 선택한 최대 단위 수를 기준으로 합니다. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>내 처리량 단위에서 사용량을 최적화하려면 어떻게 해야 하나요?
1TU(처리량 단위)와 같이 낮은 단위에서 시작하고 [자동 팽창](event-hubs-auto-inflate.md)을 설정하면 됩니다. 자동 팽창 기능을 사용하면 트래픽/페이로드가 증가함에 따라 TU를 늘릴 수 있습니다. 또한 TU 수에 대한 상한값도 설정할 수 있습니다.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Event Hubs의 자동 팽창 기능은 어떻게 작동하나요?
자동 팽창 기능을 사용하여 TU(처리량 단위)를 강화할 수 있습니다. 즉, 낮은 TU를 구입하고 수신이 증가함에 따라 자동 팽창을 통해 TU를 강화할 수 있습니다. 비용 효율적인 옵션과 관리할 TU 수에 대한 완벽한 제어를 제공합니다. 이 기능은 **강화 전용** 기능이며, TU 수를 업데이트하여 규모 축소를 완전히 제어할 수 있습니다. 

예를 들어 2TU와 같이 낮은 TU(처리량 단위)로 시작하는 것이 좋습니다. 트래픽이 15TU까지 증가할 수 있다고 예상하는 경우 네임스페이스에서 자동 팽창 기능을 설정하고 최대 제한을 15TU로 설정합니다. 이제 트래픽이 증가함에 따라 TU를 자동으로 늘릴 수 있습니다.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>자동 팽창 기능을 설정하면 관련 비용이 발생하나요?
이 기능과 관련 된 **비용은 없습니다** . 

### <a name="how-are-throughput-limits-enforced"></a>처리량 제한은 어떻게 적용되나요?
전체 수신 처리량 또는 네임스페이스 내 모든 이벤트 허브에서의 전체 수신 이벤트 비율이 집계 처리량 단위 허용 한도를 초과하면, 발신자가 제한되고 수신 할당량을 초과했음을 나타내는 오류가 표시됩니다.

전체 송신 처리량 또는 네임스페이스 내 모든 이벤트 허브에서의 전체 송신 이벤트 비율이 집계 처리량 단위 허용 한도를 초과하면, 수신자가 제한되고 송신 할당량을 초과했음을 나타내는 오류가 표시됩니다. 수신 및 송신 할당량은 별도로 적용되므로 발신자가 이벤트 사용 속도를 저하시키지 않으며 수신자가 이벤트 허브로의 이벤트 전송을 차단하지도 않습니다.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>예약/선택할 수 있는 TU(처리량 단위) 수에 제한이 있나요?
다중 테넌트 제안에서 처리량 단위는 최대 40TU까지 늘릴 수 있습니다(포털에서 최대 20TU를 선택하고, 지원 티켓을 제출하여 동일한 네임스페이스에서 40TU로 늘릴 수 있음). 40TU를 초과하는 경우 Event Hubs는 **Event Hubs Dedicated 클러스터**라고 하는 리소스/용량 기반 모델을 제공합니다. Dedicated 클러스터는 CU(용량 단위)로 판매됩니다.

## <a name="dedicated-clusters"></a>Dedicated 클러스터

### <a name="what-are-event-hubs-dedicated-clusters"></a>Event Hubs Dedicated 클러스터란?
Event Hubs Dedicated 클러스터는 요구 사항이 가장 까다로운 고객을 위한 단일 테넌트 배포를 제공합니다. 이 제안은 처리량 단위로 구속되지 않는 용량 기반 클러스터를 구축합니다. 클러스터의 CPU 및 메모리 사용량에 따라 결정 된 대로 클러스터를 사용 하 여 데이터를 수집 하 고 스트리밍할 수 있음을 의미 합니다. 자세한 내용은 [Event Hubs Dedicated 클러스터](event-hubs-dedicated-overview.md)를 참조하세요.

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>단일 용량 단위로 획득할 수 있는 성능은 어떻게 되나요?
전용 클러스터의 경우 수집 하 고 스트리밍할 수 있는 양은 생산자, 소비자, 수집 및 처리 속도 등 다양 한 요인에 따라 달라 집니다. 

다음 표에는 테스트를 통해 실현한 벤치마크 결과가 나와 있습니다.

| 페이로드 셰이프 | 수신기 | 수신 대역폭| 수신 메시지 | 송신 대역폭 | 송신 메시지 | 총 TU | CU당 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB의 일괄 처리 | 2 | 400MB/초 | 초당 400k 메시지 | 800MB/초 | 800k messages/sec | 400TU | 100TU | 
| 10x10KB의 일괄 처리 | 2 | 666MB/초 | 66.6 k messages/sec | 1.33GB/초 | 133k 메시지/초 | 666TU | 166TU |
| 6x32KB의 일괄 처리 | 1 | 1.05GB/초 | 34k 메시지/초 | 1.05GB/초 | 34k 메시지/초 | 1,000TU | 250TU |

테스트에 사용된 조건은 다음과 같습니다.

- 4CU(용량 단위)의 Event Hubs 전용 클러스터가 사용되었습니다. 
- 수집에 사용된 이벤트 허브에는 200개의 파티션이 있었습니다. 
- 데이터는 모든 파티션으로부터 받는 두 개의 수신기 애플리케이션에서 수집되었습니다.

결과는 Event Hubs 전용 클러스터로 달성할 수 있는 것에 대한 아이디어를 제공합니다. 또한 전용 클러스터는 마이크로 일괄 처리 및 장기 보존 시나리오에 Event Hubs 캡처를 사용하도록 설정된 상태로 제공됩니다.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Event Hubs Dedicated 클러스터를 만들려면 어떻게 할까요?
[할당량 증가 지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 제출하거나 [Event Hubs 팀](mailto:askeventhubs@microsoft.com)에 문의하여 Event Hubs Dedicated 클러스터를 만듭니다. 일반적으로 클러스터를 배포하고 전달하여 사용자가 사용할 수 있도록 하는 데 약 2주가 걸립니다. 이 프로세스는 클러스터를 배포 하는 데 약 2 시간이 소요 되는 Azure Portal 또는 Azure Resource Manager 템플릿을 통해 전체 셀프 서비스를 사용할 수 있게 될 때까지 일시적입니다.

## <a name="best-practices"></a>모범 사례

### <a name="how-many-partitions-do-i-need"></a>얼마나 많은 파티션이 필요한가요?
파티션 수는 만들 때 지정되며 2와 32 사이여야 합니다. 파티션 수는 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해 야 합니다. 파티션은 애플리케이션을 사용하는 데 필요한 다운스트림 병렬 처리와 관련된 데이터 구성 메커니즘입니다. Event Hub의 파티션 수는 예상되는 동시 판독기의 수와 직접적으로 관련이 있습니다. 파티션에 대한 자세한 내용은 [파티션](event-hubs-features.md#partitions)을 참조 하세요.

만들 때 가장 높은 값 (32)으로 설정 해야 할 수 있습니다. 두 개 이상의 파티션이 있는 경우에는 두 개 이상의 파티션이 중복 된 상태를 유지 32 하도록 발신자를 구성 하지 않는 한, 순서를 유지 하지 않고 여러 파티션으로 이벤트를 보냅니다. 이전 경우에는 모든 32 파티션에서 이벤트를 읽어야 합니다. 후자의 경우에는 이벤트 프로세서 호스트에서 수행 해야 하는 추가 구성에 대한 명확한 추가 비용이 없습니다.

Event Hubs는 소비자 그룹당 단일 파티션 판독기를 허용하도록 설계되었습니다. 대부분의 경우 기본 설정인 4개의 파티션만으로도 충분합니다. 이벤트 처리 크기를 조정 하려는 경우 추가 파티션을 추가 하는 것이 좋습니다. 파티션에는 특정 처리량 제한이 없지만 네임 스페이스의 집계 처리량은 처리량 단위 수로 제한 됩니다. 네임스페이스에서 처리량 단위 수를 늘리면 추가 파티션을 통해 동시 판독기가 고유한 최대 처리량을 달성하도록 할 수 있습니다.

그러나 애플리케이션에 특정 파티션에 대한 선호도가 있는 모델인 경우 파티션 수를 늘리게 되면 사용자에게 혜택이 돌아오지 않습니다. 자세한 내용은 [가용성 및 일관성](event-hubs-availability-and-consistency.md)을 참조하세요.

## <a name="pricing"></a>가격

### <a name="where-can-i-find-more-pricing-information"></a>추가 가격 책정 정보는 어디에서 찾을 수 있나요?

Event Hubs 가격 책정에 대한 전체 내용은 [Event Hubs 가격 책정 정보](https://azure.microsoft.com/pricing/details/event-hubs/)를 참조하세요.

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>24시간 이상 Event Hubs 이벤트를 유지 하려면 비용이 청구됩니까?

Event Hubs 표준 계층은 최대 7일 동안 24시간을 초과하는 메시지 보존 기간을 허용합니다. File Storage에 대한 자세한 내용은 파일 서비스 REST API를 참조하세요. 처리량 단위가 최대 수신 허용 한도까지 사용되었더라도 각 처리량 단위의 스토리지 허용 한도는 24 시간(기본값) 동안의 보존 기간에 대한 모든 스토리지 비용이 포함됩니다.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Event Hubs 스토리지 크기가 계산 및 청구되는 방법

모든 이벤트 허브의 디스크 스토리지 구조 또는 이벤트 헤더의 내부 오버헤드를 포함하여, 저장된 모든 이벤트의 전체 크기가 하루 종일 측정됩니다. 하루가 끝날 때 최고 스토리지 크기가 계산됩니다. 일별 스토리지 허용량은 하루 동안 선택된 처리량 단위의 최소 수를 기준으로 계산됩니다. 각 처리량 단위는 84GB의 허용량을 제공합니다. 총 크기가 계산된 일일 스토리지 허용 한도를 초과하면, 초과 스토리지는 Azure Blob Storage 가격을 사용하여 청구됩니다( **로컬 중복 스토리지** 속도로).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Event Hubs 수신 이벤트 계산하는 방법

이벤트 허브에 전송된 각 이벤트는 청구 가능한 메시지로 계산됩니다. *수신 이벤트* 는 64KB보다 작은 데이터 단위로 정의됩니다. 크기가 64KB 이하인 모든 이벤트는 하나의 청구 가능한 이벤트로 간주됩니다. 이벤트가 64KB보다 큰 경우 64KB의 배수로 이벤트 크기에 따라 청구 가능한 이벤트 수가 계산됩니다. 예를 들어 이벤트 허브에 전송된 8KB 이벤트는 하나의 이벤트로 청구되지만, 이벤트 허브에 전송된 96KB 메시지는 두 개의 이벤트로 청구됩니다.

이벤트 허브에서 사용된 이벤트와 검사점 등의 제어 호출 및 관리 작업은 청구 가능한 수신 이벤트로 계산되지 않고 처리량 단위 허용 한도까지 누적됩니다.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>조정된 연결 요금이 Event Hubs에 적용됩니까?

AMQP 프로토콜을 사용하는 경우에 연결 요금이 적용됩니다. 전송하는 시스템 또는 디바이스 수와 관계없이 HTTP를 사용하여 이벤트를 전송하기 위한 연결 요금은 부과되지 않습니다. AMQP를 사용하려는 경우(예: 보다 효율적인 이벤트 스트리밍을 달성하거나 IoT 명령 및 제어 시나리오에서 양방향 통신 설정), 각 서비스 계층에 포함된 연결 수에 대한 자세한 내용은 [Event Hubs 가격 책정 정보](https://azure.microsoft.com/pricing/details/event-hubs/) 페이지를 참조하세요.

### <a name="how-is-event-hubs-capture-billed"></a>Event Hubs 캡처는 어떻게 청구되나요?

캡처는 네임스페이스에 캡처 옵션이 설정된 이벤트 허브가 하나라도 있을 경우 사용됩니다. Event Hubs 캡처는 구매한 처리량 단위를 기준으로 시간당 청구됩니다. 처리량 단위 수가 증가하거나 감소하면 Event Hubs 캡처 청구 시 전체 증가 시간에 이러한 변경 내용이 반영됩니다. Event Hubs 캡처 청구에 대한 자세한 내용은 [Event Hubs 가격 정보](https://azure.microsoft.com/pricing/details/event-hubs/)를 참조하세요.

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Event Hubs 캡처에 대해 선택한 스토리지 계정에 요금이 청구되나요?

이벤트 허브에서 설정된 경우 캡처는 사용자가 제공한 스토리지 계정을 사용합니다. 사용자의 스토리지 계정이므로 이 구성에 대한 모든 변경은 사용자의 Azure 구독에 청구됩니다.

## <a name="quotas"></a>할당량

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Event Hubs와 관련된 할당량이 있나요?

Event Hubs 할당량의 목록은 [할당량](event-hubs-quotas.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>네임스페이스를 다른 구독에서 삭제한 후 만들 수 없는 이유는 무엇인가요? 
구독에서 네임스페이스를 삭제한 후에 다른 구독에서 동일한 이름으로 다시 만들려면 4시간 정도 기다려야 합니다. 그렇지 않으면 다음 오류 메시지가 표시될 수 있습니다. `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Event Hubs에 의해 생성된 일부 예외 및 해당 제안된 작업은 무엇인가요?

가능한 Event Hubs 예외의 목록은 [예외 개요](event-hubs-messaging-exceptions.md)를 참조하세요.

### <a name="diagnostic-logs"></a>진단 로그

Event Hubs는 두 가지 유형의 [진단 로그](event-hubs-diagnostic-logs.md)인 캡처 오류 로그와 작업 로그를 지원합니다. 두 로그 모두 json으로 표시되며 Azure Portal을 통해 설정할 수 있습니다.

### <a name="support-and-sla"></a>지원 및 SLA

Event Hubs에 대한 기술 지원은 [커뮤니티 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus)을 통해 사용할 수 있습니다. 청구 및 구독 관리 지원은 무료로 제공됩니다.

SLA에 대한 자세한 내용에 대해 알아보려면 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs 자동 확장](event-hubs-auto-inflate.md)
