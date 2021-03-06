---
title: Azure Cosmos DB의 자동 크기 조정 프로비전된 처리량 FAQ
description: Azure Cosmos DB 데이터베이스 및 컨테이너에 대한 자동 크기 조정 프로비전된 처리량 FAQ
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 31b96f03a8519b068eaa816443be0a0f374a4a8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247032"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Azure Cosmos DB의 자동 크기 조정 프로비전된 처리량 FAQ
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

자동 크기 조정 프로비전된 처리량을 사용하는 경우 Azure Cosmos DB는 사용량에 따라 데이터베이스 또는 컨테이너의 RU/s를 자동으로 관리하고 조정합니다. 이 문서에서는 자동 크기 조정에 대해 자주 묻는 질문에 답합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Azure Cosmos DB에서 "Autopilot"과 "자동 크기 조정"의 차이점은 무엇인가요?
"자동 크기 조정" 또는 "자동 크기 조정 프로비전된 처리량"은 이전에 "Autopilot"으로 알려진 기능의 업데이트된 이름입니다. 최신 릴리스의 자동 크기 조정에서는 사용자 지정 최대 RU/s 설정, 프로그래밍 방식 지원 등 새로운 기능을 추가했습니다. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>이전 Autopilot 계층 모델에서 만든 데이터베이스나 컨테이너는 어떻게 되나요?
이전 계층 모델을 사용하여 만든 리소스는 자동으로 새 자동 크기 조정 사용자 지정 최대 RU/s 모델에서 지원됩니다. 계층의 상한이 새 최대 RU/s가 되며, 이를 통해 동일한 크기 조정 범위가 생성됩니다. 

예를 들어 이전에 400~4,000RU/s 범위에서 크기 조정된 계층을 선택하는 경우 데이터베이스 또는 컨테이너는 이제 4,000RU/s의 최대 RU/s를 갖는 것으로 표시되고 400~4,000RU/s 범위에서 크기 조정됩니다. 여기에서 워크로드에 맞게 최대 RU/s를 사용자 지정 값으로 변경할 수 있습니다. 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>트래픽 급증을 기준으로 크기 자동 조정의 규모는 얼마나 되나요?
자동 크기 조정을 사용하는 경우 시스템은 수신 트래픽에 따라 처리량(RU/s) `T`를 `0.1 * Tmax` 및 `Tmax` 범위 내에서 조정합니다. 크기 조정은 언제든지 자동으로 또한 즉각적으로 진행되므로 지연 없이 프로비전된 `Tmax`까지 사용할 수 있습니다. 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>시스템이 현재 크기 조정된 RU/s는 어떻게 확인하나요?
[Azure Monitor 메트릭](how-to-choose-offer.md#measure-and-monitor-your-usage)을 사용하여 프로비전된 자동 크기 조정 최대 RU/s와 시스템이 크기 조정된 현재 처리량(RU/s)을 모두 모니터링할 수 있습니다. 

### <a name="what-is-the-pricing-for-autoscale"></a>자동 크기 조정은 가격이 얼마인가요?
매 시간마다 시스템이 해당 시간 내에 크기 조정된 최고 처리량 `T`에 대해 요금이 청구됩니다. 해당 시간 동안 리소스에 요청이 없거나 `0.1 * Tmax` 이상으로 확장되지 않은 경우 최소값 `0.1 * Tmax`에 대해 요금이 청구됩니다. 자세한 내용은 Azure Cosmos DB [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요. 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>자동 크기 조정은 청구서에 어떻게 표시되나요?
단일 쓰기 지역 계정에서 100 r u/초 당 자동 크기 조정 속도는 1.5 x 표준 (수동) 프로 비전 된 처리량의 속도입니다. 청구서에는 기존 표준 프로비전된 처리량 미터가 표시됩니다. 이 미터의 수량에 1.5를 곱합니다. 예를 들어 시스템이 특정 시간 중에 최고 6,000RU/s까지 크기 조정되었다면 해당 시간에 대해 미터의 60 * 1.5 = 90단위가 청구됩니다.

여러 쓰기 지역이 있는 계정에서 100 r u/초 당 자동 크기 조정 속도는 표준 (수동) 프로 비전 된 여러 쓰기 지역 처리량과 동일 합니다. 청구서에 기존 다중 쓰기 지역 측정기가 표시 됩니다. 요금은 동일하므로 자동 크기 조정을 사용하는 경우에도 표준 처리량과 동일한 수량이 표시됩니다.

### <a name="does-autoscale-work-with-reserved-capacity"></a>자동 크기 조정 기능은 예약된 용량에 적용되나요?
예. 단일 쓰기 지역이 있는 계정에 대해 예약 된 용량을 구매 하는 경우 자동 크기 조정 리소스에 대 한 예약 할인은 [특정 지역의 비율](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region)을 기준으로 미터 1.5 사용에 적용 됩니다. 

다중 쓰기 지역 예약 용량은 자동 크기 조정 및 표준 (수동) 프로 비전 된 처리량에 대해 동일 하 게 작동 합니다. [Azure Cosmos DB 예약된 용량](cosmos-db-reserved-capacity.md)을 참조하세요.

### <a name="does-autoscale-work-with-free-tier"></a>자동 크기 조정은 무료 계층에 적용되나요?
예. 무료 계층에서는 컨테이너에서 자동 크기 조정 처리량을 사용할 수 있습니다. 사용자 지정 최대 RU/s를 사용하는 자동 크기 조정 공유 처리량 데이터베이스에 대한 지원은 아직 사용할 수 없습니다. [무료 계층 청구가 자동 크기 조정에 적용되는 방식](understand-your-bill.md#azure-free-tier)을 참조하세요.

### <a name="is-autoscale-supported-for-all-apis"></a>모든 API에 대해 자동 크기 조정이 지원되나요?
예, 자동 크기 조정은 모든 API에 대해 지원됩니다. Core(SQL), Gremlin, Table, Cassandra 및 API for MongoDB.

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>다중 지역 쓰기 계정에 대해 자동 크기 조정이 지원 되나요?
예. 최대 RU/s는 Azure Cosmos DB 계정에 추가된 각 지역에서 사용할 수 있습니다. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>새 데이터베이스 또는 컨테이너에서 자동 크기 조정을 사용하려면 어떻게 해야 하나요??
이 문서에서 [자동 크기 조정을 사용하도록 설정하는 방법](how-to-provision-autoscale-throughput.md)을 참조하세요.

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>기존 데이터베이스 또는 컨테이너에서 자동 크기 조정을 사용할 수 있나요?
예. 필요에 따라 자동 크기 조정과 표준(수동) 프로비전된 처리량 사이를 전환할 수도 있습니다. 현재 모든 API에서 이러한 작업을 수행하려면 [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container)만 사용할 수 있습니다.

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>자동 크기 조정과 표준(수동) 프로비전된 처리량 간의 마이그레이션은 어떻게 이루어지나요?
개념적으로 처리량 유형 변경은 2단계 프로세스입니다. 먼저 자동 크기 조정 또는 수동 프로비전된 처리량을 사용하도록 처리량 설정을 변경하는 요청을 보냅니다. 두 경우 모두 시스템이 현재 처리량 설정 및 스토리지에 따라 초기 RU/s 값을 자동으로 확인하여 설정합니다. 이 단계를 수행하는 동안에는 사용자 제공 RU/s 값이 허용되지 않습니다. 그런 다음 업데이트가 완료되면 워크로드에 맞게 [RU/s를 변경](#can-i-change-the-max-rus-on-the-database-or-container)할 수 있습니다. 

**표준(수동) 프로비전된 처리량에서 자동 크기 조정으로 마이그레이션**

컨테이너의 경우 수식 ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)``을 사용하여 초기 자동 크기 조정 최대 RU/s를 추정합니다(1,000RU/s 단위로 반올림). 실제 초기 자동 크기 조정 최대 RU/s는 계정 구성에 따라 다를 수 있습니다.

예제 1: 수동 프로비전된 처리량이 10,000RU/s이고 스토리지가 25GB인 컨테이너가 있다고 가정합니다. 자동 크기 조정을 사용하도록 설정하면 초기 자동 크기 조정 최대 RU/s는 다음과 같습니다. 10,000RU/s(1,000~10,000RU/s 범위에서 크기 조정). 

예제 2: 수동 프로비전된 처리량이 50,000RU/s이고 스토리지가 2,500GB인 컨테이너가 있다고 가정합니다. 자동 크기 조정을 사용하도록 설정하면 초기 자동 크기 조정 최대 RU/s는 다음과 같습니다. 250,000RU/s(25,000~250,000RU/s 범위에서 크기 조정). 

**자동 크기 조정에서 표준(수동) 프로비전된 처리량으로 마이그레이션**

초기 수동 프로비전된 처리량은 현재 자동 크기 조정 최대 RU/s와 같아집니다. 

예제: 최대 RU/s가 20,000RU/s(2,000~20,000RU/s 범위에서 크기 조정)인 자동 크기 조정 데이터베이스 또는 컨테이너가 있다고 가정합니다. 수동 프로비전된 처리량을 사용하도록 업데이트하면 초기 처리량은 20,000RU/s입니다. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>자동 크기 조정을 사용하는 데이터베이스 또는 컨테이너를 관리할 수 있는 Azure CLI 또는 PowerShell 지원이 있나요?
현재 Azure Portal, .NET V3 SDK, Java V4 SDK 및 Azure Resource Manager에서 자동 크기 조정을 사용하여 리소스를 만들고 관리할 수 있습니다. Azure CLI, PowerShell 및 기타 SDK에서는 아직 지원되지 않습니다.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>공유 처리량 데이터베이스에 대해 자동 크기 조정이 지원되나요?
예, 자동 크기 조정은 공유 처리량 데이터베이스에 대해 지원됩니다. 이 기능을 사용하려면 데이터베이스를 만들 때 자동 크기 조정을 선택하고 **처리량 프로비전** 옵션을 선택합니다. 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>자동 크기 조정을 사용하는 경우 공유 처리량 데이터베이스당 허용된 컨테이너 수는 몇 개인가요?
Azure Cosmos DB는 공유 처리량 데이터베이스에서 최대 25개의 컨테이너를 허용하며, 이는 자동 크기 조정 또는 표준(수동) 처리량을 사용하는 데이터베이스에 적용됩니다. 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>데이터베이스 일관성 수준에 대 한 자동 크기 조정의 영향은 무엇 인가요?
데이터베이스의 일관성 수준에 대 한 자동 크기 조정에는 영향을 주지 않습니다.
사용 가능한 일관성 수준에 대 한 자세한 내용은 [일관성 수준](consistency-levels.md) 문서를 참조 하세요.

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>각 최대 RU/s 옵션과 관련된 스토리지 제한은 무엇인가요?  
각 최대 RU/s에 대한 스토리지 제한(GB)은 다음과 같습니다. 데이터베이스 또는 컨테이너의 최대 RU/s / 100. 예를 들어 최대 RU/s가 20,000RU/s이면 리소스가 200GB 스토리지를 지원할 수 있습니다. 사용 가능한 최대 RU/s 및 스토리지 옵션은 [자동 크기 조정 제한](provision-throughput-autoscale.md#autoscale-limits) 문서를 참조하세요. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>내 최대 처리량과 관련된 스토리지 제한을 초과하면 어떻게 되나요?
데이터베이스 또는 컨테이너의 최대 처리량과 관련된 스토리지 제한을 초과하는 경우 Azure Cosmos DB는 최대 처리량을 해당 스토리지 수준을 지원할 수 있는 다음으로 가장 높은 RU/s로 자동으로 증가시킵니다.

예를 들어 최대 RU/s를 50,000RU/s(5,000~50,000RU/s 범위에서 크기 조정)에서 시작하는 경우 최대 500GB까지 데이터를 저장할 수 있습니다. 500GB를 초과하는 경우(예: 이제 스토리지가 600GB) 새 최대 RU/s는 60,000RU/s(6,000~60,000RU/s 범위에서 크기 조정)가 됩니다.

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>데이터베이스 또는 컨테이너에서 최대 RU/s를 변경할 수 있나요? 
예. 최대 RU/s를 변경하는 방법은 이 [문서](how-to-provision-autoscale-throughput.md)를 참조하세요. 최대 RU/s를 변경하는 경우 요청된 값에 따라 이 작업은 완료하는 데 다소 시간이 걸릴 수 있는 비동기 작업일 수 있습니다(선택 사항에 따라 최대 4~6시간이 걸릴 수 있음).

#### <a name="increasing-the-max-rus"></a>최대 RU/s 증가
최대 RU/s `Tmax`를 높이는 요청을 보낼 경우 선택한 최대 RU/s에 따라 서비스가 더 많은 리소스를 프로비전하여 더 높은 최대 RU/s를 지원합니다. 이 프로세스가 발생하는 동안 기존 워크로드 및 작업에는 영향을 주지 않습니다. 시스템은 새로운 크기 조정 범위 `0.1*Tmax_new`~`Tmax_new`가 준비될 때까지 데이터베이스 또는 컨테이너를 이전의 `0.1*Tmax`~`Tmax` 범위에서 계속 크기 조정합니다.

#### <a name="lowering-the-max-rus"></a>최대 RU/s 감소
최대 RU/s를 낮출 때 설정할 수 있는 최소값은 `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`입니다(1,000RU/s 단위로 반올림). 

예제 1: 최대 RU/s가 20,000RU/s(2,000~20,000RU/s 범위에서 크기 조정)이고 스토리지가 50GB인 자동 크기 조정 컨테이너가 있다고 가정합니다. 최대 RU/s를 가장 낮게 설정할 수 있는 최소값은 다음과 같습니다. MAX(4,000, 20,000 / 10, **50 * 100**) = 5,000RU/s(500~5,000RU/s 범위에서 크기 조정).

예제 2: 최대 RU/s가 100,000RU/s이고 스토리지가 100GB인 자동 크기 조정 컨테이너가 있다고 가정합니다. 이제 최대 RU/s를 최대 150,000RU/s(15,000~150,000RU/s 범위에서 크기 조정)로 조정합니다. 이제 최대 RU/s를 가장 낮게 설정할 수 있는 최소값은 다음과 같습니다. MAX(4,000, **150,000 / 10**, 100 * 100) = 15,000RU/s(1,500~15,000RU/s 범위에서 크기 조정). 

공유 처리량 데이터베이스의 경우 최대 RU/s를 낮출 때 설정할 수 있는 최소값은 `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`입니다(1,000RU/s 단위로 반올림).  

위의 수식 및 예제는 설정할 수 있는 최소 자동 크기 조정 최대 RU/s와 관련이 있으며, 시스템이 자동으로 크기를 조정하는 범위 `0.1 * Tmax`~`Tmax`와는 구별됩니다. 최대 RU/s에 관계 없이 시스템은 항상 `0.1 * Tmax`~`Tmax` 범위에서 크기 조정합니다. 

### <a name="how-does-ttl-work-with-autoscale"></a>TTL은 자동 크기 조정에서 어떻게 작동하나요?
자동 크기 조정을 사용하는 경우 TTL 연산은 RU/s의 크기 조정에 영향을 주지 않습니다. TTL로 인해 사용된 모든 RU/s는 자동 크기 조정 컨테이너의 청구된 RU/s에 포함되지 않습니다. 

예를 들어 400~4,000RU/s를 사용하는 자동 크기 조정 컨테이너가 있다고 가정합니다.
- 시간 1: T=0: 컨테이너에 사용량이 없습니다(TTL 또는 워크로드 요청이 없음). 청구 가능 RU/s는 400RU/s입니다.
- 시간 1: T=1: TTL이 사용됩니다.
- 시간 1: T=2: 컨테이너가 요청을 가져오기 시작하며 초당 1,000RU를 사용합니다. 또한 발생해야 하는 200RU 상당 TTL이 있습니다. 청구 가능 RU/s는 여전히 1,000RU/s입니다. TTL은 발생 시점과 관계없이 자동 크기 조정의 크기 조정 논리에 영향을 주지 않습니다.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>최대 RU/s와 실제 파티션 간의 매핑은 무엇입니까?
최대 RU/s를 처음 선택하면 Azure Cosmos DB가 다음을 프로비전합니다. 최대 RU/s / 10,000RU/s = 실제 파티션 수. 각 [실제 파티션](partitioning-overview.md#physical-partitions)은 최대 10,000RU/s 및 50GB 스토리지를 지원할 수 있습니다. 스토리지 크기가 증가함에 따라 Azure Cosmos DB는 스토리지 증가를 처리하기 위해 파티션을 자동으로 분할하여 더 많은 실제 파티션을 추가하거나, 스토리지가 [연결된 제한을 초과](#what-is-the-storage-limit-associated-with-each-max-rus-option)하는 경우 최대 RU/s를 늘립니다. 

데이터베이스 또는 컨테이너의 최대 RU/s가 모든 실제 파티션으로 균등하게 분배됩니다. 따라서 하나의 실제 파티션이 크기 조정될 수 있는 총 처리량은 다음과 같습니다. 데이터베이스 또는 컨테이너의 최대 RU/s / 실제 파티션 수. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>수신 요청이 데이터베이스 또는 컨테이너의 최대 RU/s를 초과하면 어떻게 되나요?
사용된 전체 RU/s가 데이터베이스 또는 컨테이너의 최대 RU/s를 초과하는 경우 최대 RU/s를 초과하는 요청은 제한되며 429 상태 코드를 반환합니다. 100% 정규화된 사용률을 초과하게 되는 요청도 제한됩니다. 정규화된 사용률은 모든 실제 파티션에서 RU/s 사용률의 최대값으로 정의됩니다. 예를 들어 최대 처리량이 20,000RU/s이고 각각 10,000RU/s까지 확장될 수 있는 두 개의 실제 파티션 P_1 및 P_2가 있다고 가정합니다. 특정 초에 P_1이 6,000RU를 사용하고, P_2가 8,000RU를 사용한 경우 정규화된 사용률은 MAX(6,000RU / 10,000RU, 8,000RU / 10,000RU) = 0.8입니다.

> [!NOTE]
> Azure Cosmos DB 클라이언트 SDK 및 데이터 가져오기 도구(Azure Data Factory, 대량 실행기 라이브러리)는 429에서 자동으로 다시 시도하므로 이따금 표시되는 429는 무방합니다. 429 횟수가 지속적으로 높은 경우 최대 RU/s를 늘리거나 [핫 파티션](#autoscale-rate-limiting)에 대한 분할 전략을 검토해야 함을 나타낼 수 있습니다.

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> 자동 크기 조정을 사용하는 경우에도 429(제한/속도 제한)가 표시될 수 있나요? 
예. 두 가지 시나리오에서 429가 표시될 수 있습니다. 첫째, 사용된 전체 RU/s가 데이터베이스 또는 컨테이너의 최대 RU/s를 초과하는 경우 서비스가 적절히 요청을 제한합니다. 

둘째, 핫 파티션(즉 다른 파티션 키 값과 비교하여 과도하게 요청이 많은 논리 파티션 키 값)이 있는 경우 기본 실제 파티션이 RU/s 예산을 초과할 수 있습니다. 핫 파티션을 방지하려면 스토리지와 처리량을 모두 균등하게 분산하는 [우수한 파티션 키](partitioning-overview.md#choose-partitionkey)를 선택합니다. 

예를 들어 20,000RU/s 최대 처리량 옵션을 선택하고, 4개의 실제 파티션이 있는 200GB 스토리지를 사용하는 경우 각 실제 파티션은 최대 5,000RU/s로 자동 크기 조정될 수 있습니다. 특정 논리 파티션 키에 핫 파티션이 있는 경우에는 기본 실제 파티션이 5,000RU/s를 초과하는 경우(즉, 100% 정규화된 사용률을 초과하는 경우) 429가 표시됩니다.


## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 데이터베이스 또는 컨테이너에서 자동 크기 조정을 사용하도록 설정](how-to-provision-autoscale-throughput.md)하는 방법을 알아봅니다.
* [자동 크기 조정을 사용하는 프로비전된 처리량의 이점](provision-throughput-autoscale.md#benefits-of-autoscale)에 대해 알아봅니다.
* [논리적 및 물리적 파티션](partitioning-overview.md)에 대해 자세히 알아봅니다.
                        
