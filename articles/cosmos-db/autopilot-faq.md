---
title: Azure Cosmos DB autopilot 모드의 처리량에 대한 질문과 대답
description: Azure Cosmos DB 데이터베이스 및 컨테이너에 대한 autopilot 모드에 대한 질문과 대답
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483311"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Azure Cosmos DB autopilot 모드에서 프로 비전 된 처리량에 대한 질문과 대답 (미리 보기)
Autopilot 모드를 사용 하면 Azure Cosmos DB를 사용 하 여 컨테이너 또는 데이터베이스의 o s/s를 자동으로 관리 하 고 크기를 조정 합니다. 이 문서에서는 autopilot 모드에 대해 자주 묻는 질문에 답변 합니다. 

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

### <a name="is-autopilot-mode-supported-for-all-apis"></a>모든 Api에 대해 autopilot 모드가 지원 되나요?
예, autopilot 모드는 모든 Api에 대해 지원 됩니다. 코어 (SQL), Gremlin, 테이블, Cassandra 및 MongoDB 용 API입니다.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>다중 마스터 계정에 대해 autopilot 모드가 지원 되나요?
예, 다중 마스터 계정에 대해 autopilot 모드가 지원 됩니다. Cosmos 계정에 추가 된 각 지역에서 최대 r u/초를 사용할 수 있습니다. 

### <a name="what-is-the-pricing-for-autopilot"></a>Autopilot에 대한 가격은 무엇 인가요?
자세한 내용은 Azure Cosmos DB [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 를 참조 하세요. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>내 컨테이너 또는 데이터베이스에 대해 autopilot를 사용 하도록 설정 하는 어떻게 할까요??
Azure Portal를 사용 하 여 만든 새 컨테이너 및 데이터베이스에서 Autopilot 모드를 사용 하도록 설정할 수 있습니다. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Autopilot 모드를 사용 하 여 컨테이너 또는 데이터베이스를 만들 수 있는 CLI 또는 SDK 지원이 있나요?
현재 미리 보기 릴리스에서는 Azure Portal autopilot 모드로만 리소스를 만들 수 있습니다. CLI 및 SDK에 대한 지원은 아직 사용할 수 없습니다.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>기존 컨테이너 또는 데이터베이스에서 autopilot을 사용 하도록 설정할 수 있나요?
현재는 새 컨테이너와 데이터베이스를 만들 때 autopilot를 사용 하도록 설정할 수 있습니다. 기존 컨테이너 및 데이터베이스에서 autopilot 모드를 사용 하도록 설정 하는 지원은 아직 사용할 수 없습니다. [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 또는 [변경 피드](change-feed.md)를 사용 하 여 기존 컨테이너를 새 컨테이너로 마이그레이션할 수 있습니다. 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>컨테이너 또는 데이터베이스에서 autopilot 모드를 해제할 수 있나요?
예, 프로 비전 된 처리량에 대한 ' 수동 ' 옵션으로 전환 하 여 autopilot을 해제할 수 있습니다. 미리 보기 릴리스에서는 autopilot 모드에서 수동 모드로 전환한 후 동일한 리소스에 대해 autopilot를 다시 사용 하도록 설정할 수 없습니다. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>공유 처리량 데이터베이스에 대해 autopilot 모드가 지원 되나요?
예, autopilot 모드는 공유 처리량 데이터베이스에 대해 지원 됩니다. 이 기능을 사용 하려면 데이터베이스를 만들 때 autopilot 모드와 **처리량 프로 비전** 옵션을 선택 합니다. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Autopilot를 사용 하는 경우 공유 처리량 데이터베이스당 허용 되는 컬렉션 수는 얼마 인가요?
Autopilot 모드가 사용 하도록 설정 된 공유 처리량 데이터베이스의 경우 허용 되는 컬렉션 수는 MIN (25, Max o s/s의 데이터베이스/1000)입니다. 예를 들어 데이터베이스의 최대 처리량이 2만 r u/초 이면 데이터베이스에 MIN (25, 2만 r u/s/1000) = 20 개의 컬렉션이 있을 수 있습니다. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>각 max o s/s 옵션과 관련 된 저장소 제한은 무엇 인가요?  
각 max r u/s의 저장소 한도 (GB)는 데이터베이스 또는 컨테이너/100의 최대 r u/초입니다. 예를 들어 최대 r u/s가 2만 r u/초 이면 리소스가 200 GB의 저장소를 지원할 수 있습니다. 사용 가능한 최대 r u/s 및 저장소 옵션은 [autopilot 제한](provision-throughput-autopilot.md#autopilot-limits) 문서를 참조 하세요. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>최대 처리량과 관련 된 저장소 제한을 초과 하면 어떻게 되나요?
데이터베이스 또는 컨테이너의 최대 처리량과 관련 된 저장소 제한을 초과 하는 경우 Azure Cosmos DB는 해당 저장소 수준을 지원할 수 있는 가장 높은 다음 계층으로 최대 처리량을 자동으로 증가 시킵니다. 예를 들어 데이터베이스 또는 컨테이너가 4000 r u/초 최대 처리량 옵션으로 프로 비전 되 고 저장소 한도가 50 GB 인 경우 리소스의 저장소가 100 GB로 증가 하는 경우 데이터베이스 또는 컨테이너의 최대 r u/s가 자동으로 2만/s로 증가 하 여 최대 200 GB를 지원할 수 있습니다. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>트래픽 급증을 기준으로 얼마나 신속 하 게 수평 확장 및 autopilot?
Autopilot는 들어오는 트래픽에 따라 최소 및 최대 r u/초 범위 내에서 r u/초를 즉시 확장 하거나 축소 합니다. 요금은 1 시간 단위로 수행 되며, 여기서는 특정 시간에 최고/초에 대해 요금이 청구 됩니다. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Autopilot 모드에 대해 사용자 지정 최대 처리량 (r u/초) 값을 지정할 수 있나요?
현재 미리 보기 릴리스에서는 최대 처리량 (r u/초)에 대해 [네 가지 옵션](provision-throughput-autopilot.md#autopilot-limits) 중에서 선택할 수 있습니다.

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>데이터베이스 또는 컨테이너에서 최대 r u/초 (상위 계층으로 이동)를 늘릴 수 있나요? 
예. 컨테이너에 대한 **크기 조정 & 설정** 옵션 또는 데이터베이스에 대한 **크기 조정** 옵션을 선택 하 여 autopilot에 대해 더 높은 최대 r u/초를 선택할 수 있습니다. 이는 서비스에서 더 높은 규모를 지원 하기 위해 더 많은 리소스를 프로 비전 하기 때문에 완료 하는 데 시간이 오래 걸릴 수 있는 비동기 확장 작업 (일반적으로 선택 된 r u/s에 따라 4-6 시간)입니다. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>데이터베이스 또는 컨테이너에서 최대 r u/초 (하위 계층으로 이동)를 줄일 수 있나요?
예. 데이터베이스 또는 컨테이너의 현재 저장소가 규모를 축소 하려는 최대 r u/s 계층에 연결 된 [저장소 제한](#what-is-the-storage-limit-associated-with-each-max-rus-option) 보다 낮은 경우 해당 계층에 대한 최대 r u/초를 줄일 수 있습니다. 예를 들어 최대 r u/초로 2만 o s/s를 선택한 경우 50 GB 저장소 (4000 r u/s와 관련 된 저장소 용량) 보다 작은 경우 최대 r u/초를 4000 r u/초로 축소할 수 있습니다.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>최대 o s/s와 실제 파티션 간의 매핑은 무엇입니까?
최대 o s/s를 처음 선택 하는 경우 Azure Cosmos DB은 (는) 최대 r u/s/1만 r u/s = 실제 파티션 수 각 [실제 파티션은](partition-data.md#physical-partitions) 최대 1만 r u/초 및 50 GB 저장소를 지원할 수 있습니다. 저장소 크기가 Azure Cosmos DB 증가 함에 따라 저장소 증가를 처리 하는 데 더 많은 실제 파티션을 추가 하도록 파티션을 자동으로 분할 하거나, 저장소가 [연결 된 제한을 초과](#what-is-the-storage-limit-associated-with-each-max-rus-option)하는 경우 최대 r u/s 계층을 늘릴 수 있습니다. 

데이터베이스 또는 컨테이너의 최대 o s/s가 모든 실제 파티션에서 균등 하 게 분할 됩니다. 따라서 단일 실제 파티션의 총 처리량은 데이터베이스 또는 컨테이너/# 물리적 파티션의 최대 r u/초입니다. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>들어오는 요청이 데이터베이스 또는 컨테이너의 최대 r u/초를 초과 하면 어떻게 되나요?
전체 사용 된 r u/s가 컨테이너 또는 데이터베이스의 최대 r u/초를 초과 하는 경우 최대 u s/s를 초과 하는 요청은 제한 되며 429 상태 코드를 반환 합니다. 100%의 정규화 된 사용률을 반환 하는 요청도 제한 됩니다. 정규화 된 사용률은 모든 실제 파티션에서의 최고/초 사용률으로 정의 됩니다. 예를 들어 최대 처리량이 2만 r u/초이 고 각각 1만 r u/s로 크기를 조정할 수 있는 두 개의 실제 파티션 P_1와 P_2 있다고 가정 합니다. 지정 된 초당 6000 RUs와 P_2 8000 RUs를 사용 하는 P_1 경우 정규화 된 사용률은 MAX (6000 r u/1만 r u, 8000 r u/1만) = 0.8입니다.

> [!NOTE]
> Azure Cosmos DB 클라이언트 Sdk 및 데이터 가져오기 도구 (Azure Data Factory, 대량 실행자 라이브러리)는 429s를 자동으로 다시 시도 하므로 가끔 429s가 적절 합니다. 지속적으로 높은 수의 수는 최대 r u/초를 늘리거나 [핫 파티션에](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled)대한 분할 전략을 검토 해야 함을 나타낼 수 있습니다.

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Autopilot를 사용 하는 경우에도 여전히 429s (제한/전송률 제한)를 볼 수 있나요? 
예. 두 가지 시나리오에서 429s를 볼 수 있습니다. 첫째, 전체 사용 된 r u/s가 컨테이너 또는 데이터베이스의 최대 r u/초를 초과 하면 서비스에서 요청을 제한 합니다. 

둘째, 핫 파티션이 있는 경우 (예: 다른 파티션 키 값과 비교 하 여 더 많은 양의 요청을 초과 하는 논리 파티션 키 값) 기본 실제 파티션이 r u/초 예산을 초과할 수 있습니다. 모범 사례에 따라, 저장소와 처리량을 균등 하 게 분산 하는 [좋은 파티션 키를 선택](partitioning-overview.md#choose-partitionkey) 하는 것이 좋습니다. 

예를 들어 2만 r u/초 최대 처리량 옵션을 선택 하 고, 4 개의 실제 파티션이 있는 200 GB의 저장소를 사용 하는 경우 각 실제 파티션은 5000 r u/s로 자동 조정 되 수 있습니다. 특정 논리 파티션 키에 핫 파티션이 있는 경우에는 기본 실제 파티션이 5000 r u/초를 초과 하는 경우 (즉,가 100% 정규화 된 사용률을 초과 하는 경우) 4 x 29s가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos 컨테이너 또는 데이터베이스에서 autopilot를 사용 하도록 설정](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode)하는 방법에 대해 알아봅니다.
* [Autopilot의 이점](provision-throughput-autopilot.md#benefits-of-autopilot-mode)에 대해 알아봅니다.
* [논리 및 실제 파티션에](partition-data.md)대해 자세히 알아보세요.
