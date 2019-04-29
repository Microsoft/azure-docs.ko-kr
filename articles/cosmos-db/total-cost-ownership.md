---
title: Azure Cosmos DB의 TCO(총 소유 비용)
description: 이 문서에서는 IaaS 및 온-프레미스 데이터베이스를 사용할 경우의 Azure Cosmos DB 총 소유 비용을 비교해서 설명합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: c3a3305197802906077dab330a6b51c1195c6c36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729117"
---
# <a name="total-cost-of-ownershiptco-with-azure-cosmos-db"></a>Azure Cosmos DB의 TCO(총 소유 비용)

Azure Cosmos DB은 미세 조정된 다중 테넌시 및 리소스 거버넌스를 사용하여 디자인되었습니다. 이 디자인을 사용하면 Azure Cosmos DB가 훨씬 더 낮은 비용으로 작동할 수 있으므로 비용 절감에 도움이 됩니다. 현재 Azure Cosmos DB는 밀도가 지속적으로 증가하고 단일 클러스터 내에 수천 개의 고객 워크로드가 존재하는 가운데, 단일 컴퓨터에서 280개가 넘는 고객 워크로드를 지원합니다. 이 기능은 데이터 센터 내에 있는 단일 클러스터 및 여러 클러스터 간의 여러 다른 컴퓨터에서 고객 워크로드 복제본의 부하를 분산시킵니다. 자세한 내용은 [Azure Cosmos DB: Pushing the frontier of globally distributed databases](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/)(Azure Cosmos DB: 전역 분산형 데이터베이스의 한계 극복)를 참조하세요. 리소스 거버넌스, 다중 테넌시 및 나머지 Azure 인프라 영역과의 네이티브 통합으로 인해 Azure Cosmos DB는 MongoDB, Cassandra 또는 IaaS에서 실행 중인 다른 OSS NoSQL보다 평균적으로 4~6배 더 저렴하고, 온-프레미스에서 실행되는 데이터베이스 엔진보다 최대 10배 더 저렴합니다. [The total cost of (non) ownership of a NoSQL database cloud service](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)(NoSQL 데이터베이스 클라우드 서비스의 총 소유 비용) 문서를 참조하세요.

Apache Cassandra, MongoDB, HBase와 같은 OSS NoSQL 데이터베이스 솔루션은 온-프레미스용으로 디자인되었습니다. 이러한 솔루션은 관리되는 서비스로 제공될 경우 프로비전된 클러스터를 관리하고 지원을 모니터링하기 위한 테넌트 데이터베이스가 있는 Resource Manager 템플릿과 동일합니다. OSS NoSQL 아키텍처에는 상당한 크기의 운영 오버헤드가 필요하며, 전문 지식을 찾기가 어렵고 비용도 많이 들 수 있습니다. 반면에 Azure Cosmos DB는 완전 관리형 클라우드 서비스로, 개발자가 데이터베이스 인프라를 유지 및 관리하기보다 비즈니스 혁신에 초점을 맞출 수 있도록 합니다. 

클라우드 기본 데이터베이스 서비스인 Azure Cosmos DB와 달리, OSS NoSQL 데이터베이스 엔진은 리소스 거버넌스 또는 세분화된 다중 테넌시를 기본 아키텍처 원칙으로 사용하여 디자인 및 구축되지 않았습니다. Cassandra 및 MongoDB와 같은 OSS NoSQL 데이터베이스 엔진에서는 기본적으로 엔진이 실행 중인 가상 머신의 모든 리소스를 해당 용도로 사용할 수 있다고 가정합니다. 이러한 데이터베이스 엔진 대부분은 리소스 양이 특정 임계값 아래로 떨어지면 작동할 수 없습니다. 예를 들어, 소규모 VM 인스턴스의 경우도 일반적으로 더 높은 비용의 대규모 VM에 적합한 공급업체 권장 구성에 따라 사용할 수 있습니다. 따라서 호스트는 OSS NoSQL 불가능 또는 모든 다른 온-프레미스 데이터베이스 엔진 및 두 번째 또는 사용 된 저장소 초당 요청 수 같은 사용량 기준 과금 모델을 사용 하 여 사용할 수 있도록 합니다.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Azure Cosmos DB의 총 소유 비용 

Azure Cosmos DB의 서버리스 프로비저닝 모델은 데이터베이스 인프라를 과도하게 프로비전할 필요가 없도록 합니다. Azure Cosmos DB 리소스는 특수한 구성 또는 라이선스가 필요 없는 상태로 제공됩니다. 결과적으로, Azure Cosmos DB 기반 애플리케이션은 OSS NoSQL 데이터베이스에 비해 총 소유 비용을 70%정도 절감하면서 실행할 수 있습니다. 실시간 몇 가지 예제를 보려면 [고객 사용 사례](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc)를 참조하세요. Azure Cosmos DB 가격 책정 모델의 다른 이점은 다음과 같습니다.

* **가격에 비해 뛰어난 가치:** 시장 분석가, 고객 및 파트너는 고객이 이러한 솔루션을 직접 또는 다른 공급업체를 통해 구현할 때 얻을 수 있는 기능보다 Azure Cosmos DB가 훨씬 더 높은 가치의 기능을 제공한다는 사실을 확인했습니다. 이러한 전역 분산, 다중 마스터, 잘 정의되고 간편한 일관성 모델, 자동 인덱싱과 같은 데이터베이스 기능은 Azure Cosmos DB를 사용할 경우 복잡성, 오버헤드 또는 가동 중지 없이 크게 간소화됩니다.

* **NoSQL DevOps 관리가 필요 없음:** Azure Cosmos DB를 사용하면 배포를 관리하고, 유지 관리, 확장 또는 패치를 수행하기 위해 DevOps를 채용할 필요가 없습니다. OSS NoSQL 클러스터를 온-프레미스 또는 클라우드 인프라에 호스트한 상태로 수행하는 모든 워크로드를 실행할 수 있습니다.

![Azure Cosmos DB 소유 비용](./media/total-cost-ownership/tco.png)

* **탄력적으로 확장 가능:** Azure Cosmos DB 처리량 규모를 확장 및 축소할 수 있으므로 사용량이 많지 않은 시간 동안 총 소유 비용을 절감할 수 있습니다. 클라우드 인프라에 배포된 OSS NoSQL 클러스터는 제한된 탄력성을 제공하며, 온-프레미스 배포는 본래부터 탄력적이지 않습니다. Azure Cosmos DB에서는 더 많은 처리량을 프로비전하는 경우 처리량이 비례적으로 확장될 수 있습니다. 어떤 규모에서든지 재무 SLA를 통해 99번째 백분위수로 이러한 확장이 보장됩니다.

* **규모의 경제성:** Azure Cosmos DB와 같은 관리되는 서비스는 기본적으로 네트워킹, 스토리지 및 컴퓨팅과 통합되는 많은 수의 노드에서 작동합니다. Azure Cosmos DB가 대규모로 표준화되어 있으므로 비용을 절감할 수 있습니다.

* **클라우드에 맞게 최적화:** Azure Cosmos DB는 세분화된 다중 테넌시 및 성능 격리를 사용하여 처음부터 새로 디자인되었습니다. 따라서 수천 개의 테넌트와 해당 워크로드를 클러스터 및 데이터 센터에서 최적 상태로 배치, 실행하고, 부하를 분산할 수 있습니다. 반면, 현재의 OSS NoSQL 데이터베이스 세대는 전체 가상 머신이 단일 테넌트의 워크로드를 실행하는 것으로 간주되는 온-프레미스에서 작동합니다. 이러한 데이터베이스는 한 클라우드 공급자의 인프라 및 하드웨어를 최대한 활용하도록 디자인되지 않았습니다. 예를 들어, OSS NoSQL 데이터베이스 엔진은 가상 머신 작동 중단과 일상적인 이미지 업그레이드 간의 차이나 프리미엄 디스크가 이미 3방향으로 복제된다는 사실을 인식하지 못합니다. 이러한 이점을 활용할 수 없으며, 혜택 및 비용 절감을 고객에게 전달할 수 없습니다.

* **시간 단위로 비용 지불:** 대규모 워크로드의 경우, 언제든지 규모를 확장해야 할 경우 시간 단위로만 요금이 부과됩니다. 애플리케이션에 대한 워크로드는 일반적으로 연중 시간 및 쿼리되는 데이터에 따라 달라집니다. Azure Cosmos DB를 사용하는 경우 필요할 때 규모를 확장 및 축소할 수 있고 필요한 크기 만큼만 요금을 부과할 수 있습니다. 온-프레미스 또는 IaaS 호스트 하는 시스템을 사용 하 여 1 시간 마다 하드웨어를 서비스 해제 하는 방법을 하지 않기 때문에이 모델에서는 일치할 수 없습니다. 이러한 경우 Azure Cosmos DB를 사용하여 평균적으로 10~14배의 비용을 절감할 수 있습니다.

* **다양한 기능을 무료로 사용:** Azure Cosmos DB에서는 쓰기 워크로드가 대체 데이터베이스 서비스에 비해 상당히 저렴합니다. 또한 Azure Cosmos DB는 추가 요금 없이 [자동 인덱싱](indexing-policies.md), [TTL(Time to Live)](time-to-live.md), [변경 피드](change-feed.md)와 같은 기능 및 다른 데이터베이스 서비스에서는 일반적으로 요금이 청구되는 기능을 제공합니다.

* **다양한 워크로드에 대해 통합 통화 사용:** 다른 제품과 달리, Azure Cosmos DB에서는 워크로드를 읽기 및 쓰기 등으로 분류할 필요가 없습니다. 또는 워크로드 유형별로 읽기 처리량 및 쓰기 처리량에 해당하는 처리량을 프로비전합니다. Azure Cosmos DB에서 프로비전된 처리량은 요청 단위 또는 RU/초를 기준으로 통합되고 정규화된 통화를 사용하여 예약됩니다. Azure Cosmos DB는 강제로 워크로드에 우선 순위를 할당하도록 하거나, 용량 계획을 수행하거나, 용량 유형별로 요금을 부과하지 않습니다. 이러한 접근 방식을 통해 다양한 작업 및 워크로드 유형 간에 동일한 RU/초를 쉽게 교환할 수 있습니다.

* **확장을 위해 VM을 프로비전할 필요 없음:** 대부분의 운영 데이터베이스에서는 확장이 필요한 경우 주변의 노이즈 네트워크와 느슨한 리소스 거버넌스를 피하기 위해 대규모의 가상 머신을 사용해야 합니다. 이로 인해 고객은 부담을 얻게 되며 사전 비용 약정이 요구됩니다. Azure Cosmos DB를 사용할 경우 처음에는 소규모로 시작했다가, 가동 중지 시간 또는 데이터 가용성에 미치는 영향 없이, 대규모 워크로드 크기로 원활하게 확장할 수 있습니다.

* **프로비전된 처리량을 최대 한도까지 활용 가능:** Azure Cosmos DB의 하위 코어 멀티플렉싱 덕분에, 프로비전된 처리량 범위를 IaaS 호스티드 옵션 또는 타사 제품보다 훨씬 더 크게 제한할 수 있습니다. 이 방법은 대체 솔루션보다 훨씬 더 많은 비용 절감 효과를 가져옵니다.

* **Azure Cosmos DB와 다른 Azure 서비스의 심층 통합:** Azure Cosmos DB는 네트워킹, 컴퓨팅, Azure Functions(서버리스), Azure IoT 및 기타 Azure 서비스와 네이티브 통합됩니다. 이러한 통합을 통해 전 세계적으로 최상의 성능, 빠른 데이터 복제 속도가 보장됩니다. 타사 솔루션은 이와 같은 유용한 기능을 제공할 수 없으며, 일반적으로 이러한 기능을 사용하려면 프리미엄 요금이 청구됩니다.

* **기본적으로 10-20개 이상의 장애 도메인에 대한 고가용성 자동 보장:** Azure Cosmos DB는 장애 도메인에서 워크로드의 분산을 지원합니다. 이 기능은 고가용성에 매우 중요합니다. 전 세계 모든 위치에서 99번째 백분위수로, 읽기 및 쓰기에 대해 99.999의 고가용성을 제공합니다. 이와 같은 기능을 직접 또는 타사 솔루션을 통해 구현하게 되면 비용이 많이 듭니다.

* **추가 비용 없이 모든 엔터프라이즈 기능을 자동으로 가져오기:** Azure Cosmos DB는 경쟁 제품과 비교할 때 가장 포괄적인 규정 준수 인증, 보안, 미사용 데이터 및 이동 중인 데이터 암호화 기능 세트를 추가 비용 없이 제공합니다. 자동으로 전 세계에서 어디에서나 지역별 가용성을 얻을 수 있습니다. 데이터베이스를 여러 Azure 지역에 걸쳐 배치하고, 언제든지 지역을 추가 또는 제거할 수 있습니다.

* **예약 용량을 사용하여 최대 65%의 비용 절감 가능:** Azure Cosmos DB [예약 용량](cosmos-db-reserved-capacity.md)을 통해 1년 또는 3년 동안 Azure Cosmos DB 리소스 비용을 선불로 지불하면 경비를 절약할 수 있습니다. 1년 또는 3년 간의 사전 약정을 비용을 획기적으로 절감하고 정상 가격에 비해 20~65%의 할인 혜택을 받을 수 있습니다. 중요 업무용 워크로드의 경우, 용량 프로비전 측면에서 더 나은 SLA를 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 가격 책정 모델이 고객에게 비용 효율적인 방식](total-cost-ownership.md)에 대한 자세한 정보
* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](optimize-cost-queries.md)에 대한 자세한 정보
* [다중 지역 Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보
* [NoSQL 데이터베이스 클라우드 서비스의 총 (비)소유 비용](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)에 대한 자세한 정보
