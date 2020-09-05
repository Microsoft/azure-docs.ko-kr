---
title: Microsoft Azure Cosmos DB 분석 저장소(미리 보기)란?
description: Microsoft Azure Cosmos DB 트랜잭션(행 기반) 및 분석(열 기반) 저장소에 대해 알아봅니다. 분석 저장소의 이점, 대규모 워크로드 성능에 미치는 영향 및 트랜잭션 저장소에서 분석 저장소로 데이터 자동 동기화
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: rosouz
ms.openlocfilehash: b3d1371f486a73b40d352007e3681fd451a8a8b7
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815830"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>Microsoft Azure Cosmos DB 분석 저장소(미리 보기)란?

> [!IMPORTANT]
> Microsoft Azure Cosmos DB 분석 저장소는 현재 미리 보기입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 Microsoft Azure Preview에 대한 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Microsoft Azure Cosmos DB 분석 저장소는 트랜잭션 워크로드에 영향을 주지 않고 Microsoft Azure Cosmos DB의 작동 데이터에 대한 대규모 분석을 사용할 수 있게 하는 완전히 격리된 열 저장소입니다.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>작동 데이터의 대규모 분석 문제

Microsoft Azure Cosmos DB 컨테이너의 다중 모델 작동 데이터는 인덱싱된 행 기반 "트랜잭션 저장소"에 내부적으로 저장됩니다. 행 저장소 형식은 밀리초 단위의 응답 시간과 운영 쿼리를 통해 빠른 트랜잭션 읽기 및 쓰기를 지원하도록 설계되었습니다. 데이터 집합이 큰 경우이 형식에 저장 된 데이터에 대 한 프로 비전 된 처리량을 기준으로 복잡 한 분석 쿼리가 비용이 많이 들 수 있습니다. 프로 비전 된 처리량을 높게 설정 하면 실시간 응용 프로그램 및 서비스에서 사용 하는 트랜잭션 워크 로드의 성능에 영향을 줍니다.

일반적으로 많은 양의 데이터를 분석하기 위해 Microsoft Azure Cosmos DB의 트랜잭션 저장소에서 작동 데이터가 추출되어 별도의 데이터 계층에 저장됩니다. 예를 들어, 데이터는 데이터 웨어하우스나 데이터 레이크에 적절한 형식으로 저장됩니다. 이 데이터는 나중에 대규모 분석에 사용되며 Apache Spark 클러스터와 같은 컴퓨팅 엔진을 사용하여 분석됩니다. 트랜잭션 워크로드에 미치는 잠재적 영향을 최소화하기 위해 ETL(추출, 변환 및 로드) 파이프라인이 덜 자주 실행되기 때문에 작동 데이터에서 분석 스토리지와 컴퓨팅 레이어를 이렇게 분리하면 대기 시간이 증가합니다.

또한 새로 수집된 작동 데이터만 처리할 때보다 작동 데이터의 업데이트를 처리할 때 ETL 파이프라인이 복잡해집니다. 

## <a name="column-oriented-analytical-store"></a>열 기반 분석 저장소

Microsoft Azure Cosmos DB 분석 저장소는 기존의 ETL 파이프라인에서 발생하는 복잡성과 대기 시간 문제를 해결합니다. Microsoft Azure Cosmos DB 분석 저장소는 작동 데이터를 별도의 열 저장소로 자동 동기화할 수 있습니다. 열 저장소 형식은 대규모 분석 쿼리를 최적화된 방식으로 수행하는 데 적합합니다. 따라서 열 저장소 형식을 사용하면 대규모 분석 쿼리의 대기 시간이 향상됩니다.

이제 Azure Synapse Link로 Synapse Analytics에서 Microsoft Azure Cosmos DB 분석 저장소에 직접 연결하여 비 ETL HTAP 솔루션을 구축할 수 있습니다. 이를 통해 작동 데이터에 대해 거의 실시간으로 대규모 분석을 실행할 수 있습니다.

## <a name="analytical-store-details"></a>분석 저장소 정보

Microsoft Azure Cosmos DB 컨테이너에서 분석 저장소를 사용하도록 설정하면 컨테이너의 작동 데이터를 기반으로 새 열 저장소가 내부적으로 만들어집니다. 이 열 저장소는 해당 컨테이너의 행 기반 트랜잭션 저장소와 별도로 유지됩니다. 작동 데이터의 삽입, 업데이트 및 삭제는 분석 저장소에 자동으로 동기화됩니다. 데이터를 동기화하는 데 변경 피드나 ETL이 필요하지 않습니다.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>작동 데이터에 대한 분석 워크로드의 열 저장소

분석 워크로드에는 일반적으로 선택된 필드의 집계와 순차 검사가 포함됩니다. 데이터를 열 우선 순서로 저장하면 분석 저장소에서 각 필드의 값 그룹을 함께 직렬화할 수 있습니다. 이 형식은 특정 필드에 대한 통계를 검사하거나 계산하는 데 필요한 IOPS를 줄입니다. 이를 통해 큰 데이터 세트 검사를 위한 쿼리 응답 시간이 크게 향상됩니다. 

예를 들어, 운영 테이블이 다음과 같은 형식입니다.

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="운영 테이블 예" border="false":::

행 저장소는 디스크에 행별로 직렬화된 형식으로 위의 데이터를 저장합니다. 이 형식을 사용하면 트랜잭션 읽기와 쓰기 속도는 물론 "Product1에 대한 정보 반환"과 같은 운영 쿼리 속도를 높일 수 있습니다. 그러나 데이터 세트가 커지고 데이터에 대해 복잡한 분석 쿼리를 실행하려는 경우 비용이 많이 들 수 있습니다. 예를 들어, "여러 사업부와 월에 걸쳐 '장비'라는 범주에 속하는 제품의 판매 추세"를 알아보려면 복잡한 쿼리를 실행해야 합니다. 이 데이터 세트에 대한 대규모 검사는 프로비저닝된 처리량 측면에서 비용이 많이 들 수 있으며 실시간 애플리케이션 및 서비스를 지원하는 트랜잭션 워크로드의 성능에 영향을 줄 수도 있습니다.

열 저장소인 분석 저장소가 유사한 데이터 필드를 함께 직렬화하고 디스크 IOPS를 줄이므로 이러한 쿼리에 더 적합합니다.

다음 이미지에서는 Microsoft Azure Cosmos DB의 트랜잭션 행 저장소와 분석 열 저장소를 비교하여 보여줍니다.

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Microsoft Azure Cosmos DB의 트랜잭션 행 저장소와 분석 열 저장소" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>분석 워크로드를 위한 분리된 성능

분석 저장소는 트랜잭션 저장소와 별개이므로 분석 쿼리가 트랜잭션 워크로드의 성능에 영향을 미치지 않습니다.  분석 저장소에 별도의 RU(요청 단위)를 할당할 필요가 없습니다.

### <a name="auto-sync"></a>자동 동기화

자동 동기화는 작동 데이터의 삽입, 업데이트 및 삭제가 5분 내에 거의 실시간으로 트랜잭션 저장소에서 분석 저장소로 자동 동기화되는 Microsoft Azure Cosmos DB의 완전 관리형 기능입니다.

자동 동기화 기능은 분석 저장소와 함께 다음과 같은 주요 이점을 제공합니다.

#### <a name="scalability--elasticity"></a>확장성 및 탄력성

Microsoft Azure Cosmos DB 트랜잭션 저장소는 행 분할을 사용하여 가동 중지 시간 없이 스토리지와 처리량을 탄력적으로 스케일링할 수 있습니다. 트랜잭션 저장소의 행 분할은 자동 동기화의 확장성과 탄력성을 제공하여 데이터가 분석 저장소에 거의 실시간으로 동기화되도록 합니다. 데이터 동기화는 트랜잭션 트래픽 처리량이 초당 작업 1,000개인지 아니면 초당 작업 100만개인지와 무관하게 수행되며 트랜잭션 저장소의 프로비저닝된 처리량에 영향을 주지 않습니다. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>스키마 업데이트 자동 처리

Microsoft Azure Cosmos DB 트랜잭션 저장소는 스키마에 구애받지 않으며 스키마나 인덱스 관리를 처리할 필요 없이 트랜잭션 애플리케이션을 반복할 수 있습니다. 이와 달리 Microsoft Azure Cosmos DB 분석 저장소는 분석 쿼리 성능을 최적화하도록 스키마화됩니다. 자동 동기화 기능을 통해 Microsoft Azure Cosmos DB는 트랜잭션 저장소의 최신 업데이트에 대한 스키마 유추를 관리합니다.  또한 중첩된 데이터 형식 처리를 포함하여 기본적으로 분석 저장소에서 스키마 표현을 관리합니다.

시간 경과에 따라 새 속성이 추가되는 스키마 진화의 경우 분석 저장소가 트랜잭션 저장소에 있는 모든 기록 스키마의 통합된 스키마를 자동으로 제공합니다.

Microsoft Azure Cosmos DB의 모든 작동 데이터가 분석을 위해 잘 정의된 스키마를 따르는 경우 스키마가 분석 저장소에서 자동으로 유추되고 올바르게 표시됩니다. 아래 정의된 대로 분석을 위해 잘 정의된 스키마를 특정 항목이 위반하는 경우 분석 저장소에 해당 스키마가 포함되지 않습니다. 분석 정의를 위해 잘 정의된 스키마로 인해 차단된 시나리오가 있는 경우 [Microsoft Azure Cosmos DB 팀](mailto:cosmosdbsynapselink@microsoft.com)에 이메일을 보내세요.

분석을 위해 잘 정의된 스키마는 다음 사항을 고려하여 정의됩니다.

* 속성이 여러 항목에서 항상 동일한 형식입니다.

  * 예를 들어, `"a"`는 문자열인 경우도 있고 숫자인 경우도 있기 때문에 `{"a":123} {"a": "str"}`에는 잘 정의된 스키마가 없습니다. 
  
    이 경우 분석 저장소는 컨테이너 수명 동안 첫 번째로 발생하는 항목에 `“a”`의 데이터 형식을 `“a”`의 데이터 형식으로 등록합니다. `“a”`의 데이터 형식이 다른 항목은 분석 저장소에 포함되지 않습니다.
  
    null 속성에는 이 조건이 적용되지 않습니다. 예를 들어, `{"a":123} {"a":null}`은 여전히 잘 정의되어 있습니다.

* 배열 형식이 반복되는 단일 형식을 포함해야 합니다.

  * 예를 들어, 배열에 정수 형식과 문자열 형식이 혼합되어 있기 때문에 `{"a": ["str",12]}`는 잘 정의된 스키마가 아닙니다.

* 스키마의 모든 중첩 수준에는 최대 200개의 속성이 있고 최대 중첩 깊이는 5입니다.

  * 최상위 수준에 201개의 속성이 있는 항목에는 잘 정의된 스키마가 없습니다.

  * 스키마에서 5개가 넘는 중첩된 수준이 있는 항목에는 잘 정의된 스키마가 없습니다. 예를 들어 `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* 대소문자를 구분하지 않는 방식으로 비교 시 속성 이름이 고유합니다.

  * 예를 들어, 다음 항목에는 잘 정의된 스키마 `{"Name": "fred"} {"name": "john"}`이 없습니다. 대소문자를 구분하지 않는 방식으로 비교 시 `"Name"`과 `"name"`은 동일합니다.

### <a name="cost-effective-archival-of-historical-data"></a>비용 효율적으로 기록 데이터 보관

데이터 계층화는 서로 다른 시나리오에 최적화된 스토리지 인프라 간에 데이터를 분리하는 것을 말합니다. 그러면 엔드투엔드 데이터 스택의 전반적인 성능과 비용 효율성이 향상됩니다. Microsoft Azure Cosmos DB는 이제 분석 저장소를 사용하여 다른 데이터 레이아웃을 가진 트랜잭션 저장소에서 분석 저장소로의 데이터 자동 계층화를 지원합니다. 트랜잭션 저장소와 비교하여 스토리지 비용 측면에서 최적화된 분석 저장소를 사용하면 기록 분석을 위해 훨씬 더 긴 기간의 작동 데이터를 보존할 수 있습니다.

트랜잭션 저장소의 데이터 보존 요구 사항에 따라 분석 저장소를 사용하도록 설정한 후 특정 기간이 지나면 트랜잭션 저장소에서 레코드가 자동으로 삭제되도록 '트랜잭션 저장소 TTL(트랜잭션 TTL(Time to Live))' 속성을 구성할 수 있습니다. 마찬가지로 '분석 저장소 TTL(분석 TTL)'을 사용하면 트랜잭션 저장소와는 독립적으로 분석 저장소에 보존되는 데이터의 수명 주기를 관리할 수 있습니다. 분석 저장소를 사용하도록 설정하고 TTL 속성을 구성하여 두 저장소의 데이터 보존 기간을 원활하게 계층화하고 정의할 수 있습니다.

### <a name="global-distribution"></a>글로벌 배포

전역적으로 분산된 Microsoft Azure Cosmos DB 계정이 있는 경우 컨테이너에 분석 저장소를 사용하도록 설정하면 해당 계정의 모든 지역에서 이 계정을 사용할 수 있습니다.  작동 데이터의 변경 내용은 모든 지역에서 전역적으로 복제됩니다. Microsoft Azure Cosmos DB에서 데이터의 가장 가까운 지역 복사본을 대상으로 분석 쿼리를 효과적으로 실행할 수 있습니다.

### <a name="security"></a>보안

분석 저장소를 사용한 인증은 지정된 데이터베이스의 트랜잭션 저장소와 동일합니다. 인증을 위해 마스터 또는 읽기 전용 키를 사용할 수 있습니다. Synapse Studio에서 연결된 서비스를 활용하여 Spark 노트북에 Microsoft Azure Cosmos DB 키 붙여넣기를 방지할 수 있습니다. 이 연결된 서비스에 대한 액세스는 작업 영역에 액세스할 수 있는 모든 사람이 사용할 수 있습니다.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>여러 Azure Synapse Analytics 런타임 지원

분석 저장소는 컴퓨팅 실행 시간에 종속되지 않고 분석 워크로드에 확장성, 탄력성 및 성능을 제공하도록 최적화되었습니다. 스토리지 기술은 수동 작업 없이 분석 워크로드를 최적화하기 위해 자체 관리됩니다.

분석 컴퓨팅 시스템에서 분석 스토리지 시스템을 분리하면 Azure Synapse Analytics에서 지원하는 여러 분석 런타임에서 Microsoft Azure Cosmos DB 분석 저장소의 데이터를 동시에 쿼리할 수 ​​있습니다. 현재 Synapse Analytics는 Microsoft Azure Cosmos DB 분석 저장소를 통해 Apache Spark와 SQL 서버리스를 지원합니다.

> [!NOTE]
> Synapse Analytics 런타임을 사용하는 분석 저장소에서만 읽을 수 있습니다. 트랜잭션 저장소에 서비스 레이어로 데이터를 다시 작성할 수 있습니다.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> 가격 책정

분석 저장소는 다음에 대해 청구되는 소비 기반 가격 책정 모델을 따릅니다.

* 스토리지: 분석 TTL에서 정의한 기록 데이터를 포함하여 매달 분석 저장소에 보존되는 데이터의 양입니다.

* 분석 쓰기 작업: 트랜잭션 저장소에서 분석 저장소로 작동 데이터 업데이트의 완전 관리형 동기화(자동 동기화)

* 분석 읽기 작업: Synapse Analytics Spark 및 SQL 서버를 사용 하지 않는 실행 시간에서 분석 저장소에 대해 수행 되는 읽기 작업입니다.

> [!NOTE]
> Azure Cosmos DB 분석 저장소는 현재 무료로 공개 미리 보기로 제공 됩니다.

분석 저장소 가격은 트랜잭션 저장소 가격 책정 모델과는 별개입니다. 분석 저장소에는 프로비저닝된 RU의 개념이 없습니다. 분석 저장소의 가격 책정 모델에 대한 자세한 내용은 [Microsoft Azure Cosmos DB 가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

Microsoft Azure Cosmos DB 컨테이너에서 분석 저장소를 사용하도록 설정하기 위한 대략적인 비용 예상이 필요한 경우 [Microsoft Azure Cosmos DB Capacity Planner](https://cosmos.azure.com/capacitycalculator/)를 사용하여 분석 스토리지와 쓰기 작업 비용을 예상할 수 있습니다. 분석 읽기 작업 비용은 분석 워크로드 특성에 따라 달라지지만 대략적으로 어림하여 분석 저장소에서 1TB의 데이터를 검사할 경우 대개 13만개의 분석 읽기 작업이 수행되고 결과적으로 $0.065의 비용이 발생합니다.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> 분석 TTL(Time-to-Live)

분석 TTL은 컨테이너에 대해 분석 저장소에 데이터를 보존해야 하는 기간을 나타냅니다. 

분석 저장소를 사용 하는 경우 트랜잭션 TTL 구성과 관계 없이 작업 데이터에 대 한 삽입, 업데이트, 삭제가 트랜잭션 저장소에서 분석 저장소로 자동 동기화 됩니다. 분석 저장소에서 이 작동 데이터의 보존은 아래에 지정된 대로 컨테이너 수준에서 분석 TTL 값으로 제어할 수 있습니다.

컨테이너의 분석 TTL은 `AnalyticalStoreTimeToLiveInSeconds` 속성을 사용하여 설정됩니다.

* 값이 "0"으로 설정되거나 누락되거나 null로 설정되면 분석 저장소를 사용할 수 없으며 트랜잭션 저장소에서 분석 저장소로 데이터가 복제되지 않습니다.

* 존재하고 값이 "-1"로 설정되면 트랜잭션 저장소의 데이터 보존과 관계없이 분석 저장소에 모든 기록 데이터가 보존됩니다. 이 설정은 분석 저장소에 작동 데이터가 무기한으로 보존됨을 나타냅니다.

* 존재하고 값이 양수 "n"으로 설정되면 항목이 트랜잭션 저장소에서 마지막으로 수정되고 "n"초 후에 분석 저장소에서 만료됩니다. 이 설정은 트랜잭션 저장소의 데이터를 보존 하는 것과 관계 없이 분석 저장소에서 제한 된 기간 동안 운영 데이터를 보존 하려는 경우에 활용할 수 있습니다.

몇 가지 고려할 점은 다음과 같습니다.
*   분석 TTL 값으로 분석 저장소를 사용하도록 설정한 후 나중에 다른 유효한 값으로 분석 저장소를 업데이트할 수 있습니다. 
*   컨테이너 또는 항목 수준에서 트랜잭션 TTL을 설정할 수 있지만 현재 분석 TTL은 컨테이너 수준에서만 설정할 수 있습니다.
*   컨테이너 수준에서 분석 TTL > = 트랜잭션 TTL을 설정하여 분석 저장소에 작동 데이터를 더 길게 보존할 수 있습니다.
*   분석 TTL = 트랜잭션 TTL로 설정하여 트랜잭션 저장소를 미러링하도록 분석 저장소를 만들 수 있습니다.

자세한 내용은 [컨테이너에 분석 TTL을 구성하는 방법](configure-synapse-link.md#create-analytical-ttl)을 참조하세요

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 문서를 참조하세요

* [Microsoft Azure Cosmos DB용 Azure Synapse Link](synapse-link.md)

* [Microsoft Azure Cosmos DB용 Azure Synapse Link 시작하기](configure-synapse-link.md)

* [Microsoft Azure Cosmos DB용 Azure Synapse Link에 대한 질문과 대답](synapse-link-frequently-asked-questions.md)

* [Microsoft Azure Cosmos DB용 Azure Synapse Link 사용 사례](synapse-link-use-cases.md)
