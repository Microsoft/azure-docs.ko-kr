---
title: Azure HDInsight의 Apache Kafka에 대 한 FAQ
description: 관리 되는 Hadoop 클라우드 서비스인 Azure HDInsight의 Apache Kafka에 대 한 일반적인 질문에 대 한 답변을 받으세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: c8d2ef0330a32d5cab88355cc749322ec3a5ea30
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530938"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight의 Apache Kafka에 대 한 질문과 대답

이 문서에서는 Azure HDInsight에서 Apache Kafka를 사용 하는 방법에 대 한 몇 가지 일반적인 질문을 다룹니다.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>HDInsight에서 지원 되는 Kafka 버전은 무엇 인가요?

Hdinsight에 대 한 자세한 내용은 hdinsight에서 [사용할 수 있는 Apache Hadoop 구성 요소 및 버전은 무엇 인가요?](../hdinsight-component-versioning.md#supported-hdinsight-versions)를 사용 하 여 공식적으로 지원 됩니다. 최상의 성능 및 사용자 환경을 보장 하기 위해 항상 최신 버전을 사용 하는 것이 좋습니다.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>HDInsight Kafka 클러스터에 제공 되는 리소스 및 청구 되는 리소스는 무엇 인가요?

HDInsight Kafka 클러스터에는 다음 리소스가 포함 됩니다.

* 헤드 노드
* Zookeeper 노드
* Broker (작업자) 노드 
* Azure Managed Disks broker 노드에 연결 됨
* 게이트웨이 노드

이러한 모든 리소스는 게이트웨이 노드를 제외 하 고 [HDInsight 가격 책정 모델](https://azure.microsoft.com/pricing/details/hdinsight/)을 기준으로 요금이 청구 됩니다. 게이트웨이 노드에 대 한 요금은 청구 되지 않습니다.

다양 한 노드 유형에 대 한 자세한 설명은 [Azure HDInsight virtual network 아키텍처](../hdinsight-virtual-network-architecture.md)를 참조 하세요. 가격은 분당 노드당 사용량을 기준으로 합니다. 가격은 노드 크기, 노드 수, 사용 되는 관리 디스크의 유형 및 지역에 따라 달라 집니다.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Apache Kafka Api가 HDInsight에서 작동 하나요?

예, HDInsight는 네이티브 Kafka Api를 사용 합니다. 클라이언트 응용 프로그램 코드를 변경할 필요가 없습니다. [자습서: Apache Kafka 생산자 및 소비자 api를 사용](./apache-kafka-producer-consumer-api.md) 하 여 클러스터에서 Java 기반 생산자/consumer api를 사용 하는 방법을 참조 하세요.

## <a name="can-i-change-cluster-configurations"></a>클러스터 구성을 변경할 수 있나요?

예, Ambari 포털을 통해 합니다. 포털의 각 구성 요소에는 구성 요소 구성을 변경 하는 데 사용할 수 있는 **configs** 섹션이 있습니다. 일부 변경 내용은 broker를 다시 시작 해야 할 수 있습니다.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>HDInsight에서 Apache Kafka에 대해 지원 되는 인증 유형은 무엇입니까?

[Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md)를 사용 하 여 Kafka 클러스터에 대 한 토픽 수준 보안을 얻을 수 있습니다. 자세한 내용은 [자습서: Enterprise Security Package를 사용 하 여 HDInsight에서 Apache Kafka 정책 구성 (미리 보기)](../domain-joined/apache-domain-joined-run-kafka.md)을 참조 하세요.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>내 데이터가 암호화 되나요? 자신의 키를 사용할 수 있나요?

관리 디스크의 모든 Kafka 메시지는 [SSE (Azure Storage 서비스 암호화)](../../storage/common/storage-service-encryption.md)를 사용 하 여 암호화 됩니다. 데이터 전송 (예: 클라이언트에서 broker로 전송 되는 데이터 및 그 밖의 다른 방법)은 기본적으로 암호화 되지 않습니다. [자체에서 SSL을 설정](./apache-kafka-ssl-encryption-authentication.md)하 여 이러한 트래픽을 암호화할 수 있습니다. 또한 HDInsight를 사용 하 여 미사용 데이터를 암호화 하는 고유한 키를 관리할 수 있습니다. 자세한 내용은 [Azure HDInsight에서 Apache Kafka에 대 한 고유한 키 가져오기](apache-kafka-byok.md)를 참조 하세요.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>클러스터에 클라이언트를 연결 어떻게 할까요??

Kafka 클라이언트에서 Kafka broker와 통신 하려면 네트워크를 통해 broker에 연결할 수 있어야 합니다. HDInsight 클러스터의 경우 VNet (Virtual Network)은 보안 경계입니다. 따라서 클라이언트를 HDInsight 클러스터에 연결 하는 가장 쉬운 방법은 클러스터와 동일한 VNet 내에 클라이언트를 만드는 것입니다. 기타 시나리오는 다음과 같습니다.

* 다른 Azure VNet의 클라이언트 연결 – 클러스터 VNet 및 클라이언트 VNet을 피어 링 하 고 [IP 보급](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising)을 위한 클러스터를 구성 합니다. IP 광고를 사용 하는 경우 Kafka 클라이언트는 Fqdn (정규화 된 도메인 이름) 대신 브로커 IP 주소를 사용 하 여 브로커에 연결 해야 합니다.

* 온-프레미스 클라이언트 연결 – VPN 네트워크를 사용 하 고 [Azure HDInsight 용 가상 네트워크 계획](../hdinsight-plan-virtual-network-deployment.md)에 설명 된 대로 사용자 지정 DNS 서버를 설정 합니다.

* Kafka 서비스에 대 한 공용 엔드포인트 만들기 – 엔터프라이즈 보안 요구 사항에 따라 허용 되는 경우 Kafka broker에 대 한 공용 엔드포인트 또는 공용 엔드포인트이 있는 자체 관리 되는 오픈 소스 REST 엔드포인트을 배포할 수 있습니다.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>기존 클러스터에 더 많은 디스크 공간을 추가할 수 있나요?

Kafka 메시지에 사용할 수 있는 공간의 크기를 늘리려면 노드 수를 늘릴 수 있습니다. 현재 기존 클러스터에 디스크를 더 추가 하는 것은 지원 되지 않습니다.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kafka 클러스터가 Databricks에서 작동할 수 있나요? 

예, Kafka 클러스터는 동일한 VNet에 있는 한 Databricks에서 작동할 수 있습니다. Databricks와 함께 Kafka 클러스터를 사용 하려면 HDInsight Kafka 클러스터를 사용 하 여 VNet을 만든 다음 Databricks 작업 영역을 만들고 VNet 주입을 사용할 때 VNet을 지정 합니다. 자세한 내용은 [Azure Virtual Network(VNet 삽입)에서 Azure Databricks 배포](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)를 참조하세요. Databricks 작업 영역을 만들 때 Kafka 클러스터의 부트스트랩 broker 이름을 제공 해야 합니다. Kafka broker 이름을 검색 하는 방법에 대 한 자세한 내용은 [Apache 사육 아웃 및 broker 호스트 정보 가져오기](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo)를 참조 하세요.

## <a name="how-can-i-have-maximum-data-durability"></a>데이터 내구성을 극대화 하려면 어떻게 해야 하나요?

데이터 내구성을 통해 메시지 손실의 가장 낮은 위험을 달성할 수 있습니다. 최대 데이터 내구성을 얻기 위해 다음 설정을 권장 합니다.

* 대부분의 지역에서 최소 복제 인수 3 사용
* 두 개의 장애 도메인만 있는 지역에서 최소 복제 인수 4를 사용 합니다.
* unclean 리더 선거 사용 안 함
* **insync** 를 2 이상으로 설정-쓰기를 계속 하기 전에 리더와 완전히 동기화 해야 하는 복제본의 수를 변경 합니다.
* **ack** 속성을 **all** 로 설정-이 속성은 모든 복제본이 모든 메시지를 승인 하도록 요구 합니다.

더 높은 데이터 일관성을 위해 Kafka을 구성 하면 broker를 사용 하 여 요청을 생성할 수 있습니다.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>데이터를 여러 클러스터에 복제할 수 있나요?

예, Kafka MirrorMaker를 사용 하 여 여러 클러스터에 데이터를 복제할 수 있습니다. MirrorMaker 설정에 대 한 자세한 내용은 [미러 Apache Kafka 항목](apache-kafka-mirroring.md)에서 찾을 수 있습니다. 또한 [Brooklin](https://github.com/linkedin/Brooklin/)와 같은 여러 클러스터로의 복제를 구현 하는 데 도움이 되는 다른 자체 관리 오픈 소스 기술 및 공급 업체가 있습니다.

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>클러스터를 업그레이드할 수 있나요? 클러스터를 어떻게 업그레이드 해야 하나요?

현재 내부 클러스터 버전 업그레이드는 지원 되지 않습니다. 클러스터를 더 높은 Kafka 버전으로 업데이트 하려면 원하는 버전으로 새 클러스터를 만들고 새 클러스터를 사용 하도록 Kafka 클라이언트를 마이그레이션합니다.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>내 Kafka 클러스터를 모니터링할 어떻게 할까요? 있나요?

Azure monitor를 사용 하 여 [Kafka 로그](./apache-kafka-log-analytics-operations-management.md)를 분석 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight에서 Apache Kafka에 대 한 SSL(Secure Sockets Layer) (SSL) 암호화 및 인증 설정](./apache-kafka-ssl-encryption-authentication.md)
* [MirrorMaker를 사용하여 HDInsight에서 Kafka와 함께 Apache Kafka 토픽 복제](./apache-kafka-mirroring.md)
