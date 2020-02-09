---
title: Azure Cosmos DB capacity planner를 사용 하 여 비용 추정
description: Azure Cosmos DB capacity planner를 사용 하 여 워크 로드에 필요한 처리량 (r u/초)과 비용을 예측할 수 있습니다. 이 문서에서는 새 버전의 capacity planner를 사용 하 여 필요한 처리량과 비용을 예측 하는 방법을 설명 합니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707631"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Azure Cosmos DB capacity planner를 사용 하 여 r u/초 예측

프로 비전 된 처리량을 적절 하 게 제공 하는 Azure Cosmos 데이터베이스 및 컨테이너를 구성 하거나 작업에 대한 [요청 단위 (r u/초)](request-units.md)를 구성 하는 것은 비용과 성능을 최적화 하는 데 필요 합니다. 이 문서에서는 Azure Cosmos DB [capacity planner](https://cosmos.azure.com/capacitycalculator/) 를 사용 하 여 필요한 r u/초 및 작업 비용을 예상 하는 방법을 설명 합니다. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Azure Cosmos DB capacity planner를 사용 하 여 처리량 및 비용을 계산 하는 방법

Capacity planner는 두 가지 모드에서 사용할 수 있습니다.

|**모드**  |**설명**  |
|---------|---------|
|Basic|빠른 상위 수준/초 및 비용 예측을 제공 합니다. 이 모드는 인덱싱 정책, 일관성 및 기타 매개 변수에 대한 기본 Azure Cosmos DB 설정을 가정 합니다. <br/><br/>Azure Cosmos DB에서 실행 될 잠재적인 작업을 평가할 때 신속 하 고 높은 수준의 예상치에 기본 모드를 사용 합니다.|
|고급|추가 설정, 즉 인덱싱 정책, 일관성 수준 및 비용 및 처리량에 영향을 주는 기타 매개 변수를 조정 하는 기능을 사용 하 여 보다 자세한 정보 및 비용 예측을 제공 합니다. <br/><br/>새 프로젝트에 대한 o s/s를 예상 하거나 더 자세한 추정치를 원하는 경우 고급 모드를 사용 합니다. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>기본 모드를 사용 하 여 프로 비전 된 처리량 및 비용 예측
기본 모드를 사용 하 여 작업에 대한 빠른 예측을 얻으려면 [capacity planner](https://cosmos.azure.com/capacitycalculator/)로 이동 합니다. 워크 로드에 따라 다음 매개 변수를 입력 합니다. 

|**입력**  |**설명**  |
|---------|---------|
|영역 수|Azure Cosmos DB는 모든 새 Azure 하위 지역에 제공됩니다. 워크 로드에 필요한 지역 수를 선택 합니다. Cosmos 계정에 원하는 수의 지역을 연결할 수 있습니다. 자세한 내용은 Azure Cosmos DB의 [전역 배포](distribute-data-globally.md) 를 참조 하세요.|
|다중 지역 쓰기|[다중 지역 쓰기](distribute-data-globally.md#key-benefits-of-global-distribution)를 사용 하도록 설정 하면 응용 프로그램에서 모든 Azure 지역에 대한 읽기 및 쓰기를 수행할 수 있습니다. 다중 지역 쓰기를 사용 하지 않도록 설정 하면 응용 프로그램에서 단일 지역에 데이터를 쓸 수 있습니다. <br/><br/> 다른 지역에서 짧은 대기 시간 쓰기가 필요한 활성-활성 작업이 있을 것으로 간주 되는 경우 다중 지역 쓰기를 사용 하도록 설정 합니다. 예를 들어 다른 지역의 대용량 볼륨에 있는 데이터베이스에 데이터를 기록 하는 IOT 작업입니다. <br/><br/> 다중 지역 쓰기는 99.999% 읽기 및 쓰기 가용성을 보장 합니다. 다중 지역 쓰기에는 단일 쓰기 지역과 비교할 때 더 많은 처리량이 필요 합니다. 자세한 내용은 [단일 및 다중 쓰기 지역에 대한 RUs가 어떻게 다른 지](optimize-cost-regions.md) 알아보세요 문서를 참조 하세요.|
|저장 된 총 데이터 (지역별)|단일 지역에 GB 단위로 저장 된 총 예상 데이터입니다.|
|항목 크기|1 ~ 2mb 범위의 데이터 항목에 대한 예상 크기 (예: 문서)입니다. |
|지역별 읽기/초|초당 예상 읽기 수입니다. |
|지역별 쓰기 수/초|초당 예상 되는 쓰기 수입니다. |

필요한 세부 정보를 채운 후 **계산**을 선택 합니다. **비용 예측** 탭은 저장소 및 프로 비전 된 처리량에 대한 총 비용을 보여 줍니다. 이 탭에서 **자세한 정보 표시** 링크를 확장 하 여 읽기 및 쓰기 요청에 필요한 처리량을 파악할 수 있습니다. 필드의 값을 변경할 때마다 **계산** 을 선택 하 여 예상 비용을 다시 계산 합니다. 

![Capacity planner 기본 모드](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>고급 모드를 사용 하 여 프로 비전 된 처리량 및 비용 예측

고급 모드에서는 r u/초 예상치에 영향을 주는 더 많은 설정을 제공할 수 있습니다. 이 옵션을 사용 하려면 [capacity planner](https://cosmos.azure.com/capacitycalculator/) 로 이동 하 여 Azure에 사용 하는 계정으로 도구에 로그인 합니다. 로그인 옵션은 오른쪽 모서리에서 사용할 수 있습니다. 

로그인 한 후에는 기본 모드의 필드와 비교 하 여 추가 필드를 볼 수 있습니다. 워크 로드에 따라 추가 매개 변수를 입력 합니다. 

|**입력**  |**설명**  |
|---------|---------|
|API|Azure Cosmos DB 다중 모델 및 다중 API 서비스입니다. 새 워크 로드의 경우 SQL (코어) API를 선택 합니다. |
|영역 수|Azure Cosmos DB는 모든 새 Azure 하위 지역에 제공됩니다. 워크 로드에 필요한 지역 수를 선택 합니다. Cosmos 계정에 원하는 수의 지역을 연결할 수 있습니다. 자세한 내용은 Azure Cosmos DB의 [전역 배포](distribute-data-globally.md) 를 참조 하세요.|
|다중 지역 쓰기|[다중 지역 쓰기](distribute-data-globally.md#key-benefits-of-global-distribution)를 사용 하도록 설정 하면 응용 프로그램에서 모든 Azure 지역에 대한 읽기 및 쓰기를 수행할 수 있습니다. 다중 지역 쓰기를 사용 하지 않도록 설정 하면 응용 프로그램에서 단일 지역에 데이터를 쓸 수 있습니다. <br/><br/> 다른 지역에서 짧은 대기 시간 쓰기가 필요한 활성-활성 작업이 있을 것으로 간주 되는 경우 다중 지역 쓰기를 사용 하도록 설정 합니다. 예를 들어 다른 지역의 대용량 볼륨에 있는 데이터베이스에 데이터를 기록 하는 IOT 작업입니다. <br/><br/> 다중 지역 쓰기는 99.999% 읽기 및 쓰기 가용성을 보장 합니다. 다중 지역 쓰기에는 단일 쓰기 지역과 비교할 때 더 많은 처리량이 필요 합니다. 자세한 내용은 [단일 및 다중 쓰기 지역에 대한 RUs가 어떻게 다른 지](optimize-cost-regions.md) 알아보세요 문서를 참조 하세요.|
|기본 일관성|Azure Cosmos DB는 개발자가 일관성, 가용성 및 대기 시간 간의 균형을 조정 하는 데 사용할 수 있는 5 가지 일관성 수준을 지원 합니다. 자세히 알아보려면 [일관성 수준](consistency-levels.md) 문서를 참조 하세요. <br/><br/> 기본적으로 Azure Cosmos DB는 세션 일관성을 사용 하 여 세션에서 자신의 쓰기를 읽을 수 있는 기능을 보장 합니다. <br/><br/> 강력 또는 제한 된 부실을 선택 하려면 세션, 일관 된 접두사 및 최종 일관성과 비교 했을 때 읽기에 필요한 r u/s를 2 배가 요구 합니다. 다중 지역 쓰기와의 강력한 일관성은 지원 되지 않으며, 강력한 일관성을 가진 단일 지역 쓰기에 자동으로 기본값을 사용 합니다. |
|인덱싱 정책|기본적으로 Azure Cosmos DB는 유연 하 고 효율적인 쿼리를 위해 모든 항목의 [모든 속성을 인덱싱합니다](index-policy.md) ( **자동** 인덱싱 정책에 매핑됨). <br/><br/> **Off**를 선택 하는 경우에는 어떤 속성도 인덱싱되지 않습니다. 이로 인해 쓰기 비용이 가장 낮습니다. [지점 읽기](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (키 값 조회) 및/또는 쓰기만 필요 하 고 쿼리를 실행 하지 않을 것으로 간주 되는 경우 정책 **해제** 를 선택 합니다. <br/><br/> 사용자 지정 인덱싱 정책을 사용 하면 낮은 쓰기 처리량 및 저장소에 대한 인덱스의 특정 속성을 포함 하거나 제외할 수 있습니다. 자세히 알아보려면 [인덱싱 정책](index-overview.md) 및 [샘플 인덱싱 정책](how-to-manage-indexing-policy.md#indexing-policy-examples) 문서를 참조 하세요.|
|저장 된 총 데이터 (지역별)|단일 지역에 GB 단위로 저장 된 총 예상 데이터입니다.|
|작업 모드|작업 볼륨이 상수인 경우 **일정** 을 선택 합니다. <br/><br/> 시간이 지남에 따라 작업 볼륨이 변경 되는 경우 **변수** 를 선택 합니다.  예를 들어, 특정 일 또는 한 달 중 하나입니다. <br/><br/> 가변 작업 옵션을 선택한 경우 다음 설정을 사용할 수 있습니다.<ul><li>최대 사용량 시간 비율: 워크 로드의 최고 처리량 (최고)을 요구 하는 한 달의 시간 백분율입니다. <br/><br/> 예를 들어 오전 9 시 – 오후 6 시 평일 업무 시간 동안 작업량이 많은 작업이 있는 경우 최대 사용량 시간 백분율은 다음과 같습니다. 최대/730 시간/월 45 시간 = ~ 6%<br/><br/></li><li>피크에서 지역별 읽기/초-초당 예상 읽기 수입니다.</li><li>가장 많이 사용 되는 지역별 쓰기/초-초당 예상 되는 쓰기 수입니다.</li><li>하위 지역 당 읽기 수/초-사용량이 많은 시간에 예상 되는 초당 읽기 수입니다.</li><li>하위 지역 당 쓰기 수/초-사용량이 적을 때 예상 되는 초당 쓰기 수입니다.</li></ul>최대 및 사용률이 낮은 간격으로 [프로 비전 된 처리량을 프로그래밍 방식으로 확장](set-throughput.md#update-throughput-on-a-database-or-a-container) 및 축소 하 여 비용을 최적화할 수 있습니다.|
|항목 크기|1kb에서 2mb 사이의 데이터 항목 크기 (예: 문서)입니다. <br/><br/>보다 정확한 예측을 위해 **샘플 (JSON)** 문서를 업로드할 수도 있습니다.<br/><br/>워크 로드에 여러 유형의 항목 (JSON 콘텐츠 포함)이 동일한 컨테이너에 있는 경우 여러 JSON 문서를 업로드 하 고 예상 값을 얻을 수 있습니다. **새 항목 추가** 단추를 사용 하 여 여러 샘플 JSON 문서를 추가 합니다.|

또한 **예상 값 저장** 단추를 사용 하 여 현재 추정치를 포함 하는 CSV 파일을 다운로드할 수 있습니다. 

![Capacity planner 고급 모드](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Azure Cosmos DB capacity planner에 표시 된 가격은 처리량 및 저장소에 대한 공용 가격 책정 요금을 기준으로 예상 됩니다. 모든 가격은 미국 달러 단위로 표시 됩니다. 지역별 모든 요금을 보려면 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 를 참조 하세요.  

## <a name="estimating-throughput-for-queries"></a>쿼리의 처리량 예측

Azure Cosmos capacity 계산기는 지점 읽기 (예: 단일 항목 읽기, ID 및 파티션 키 값을 기준으로 문서) 및 작업에 대한 쓰기를 가정 합니다. 쿼리에 필요한 처리량을 예측 하려면 Cosmos 컨테이너에 있는 대표 데이터 집합에 대해 쿼리를 실행 하 고이에 대한 [비용을 확보](find-request-unit-charge.md)하세요. 필요한 총 r u/초를 얻기 위해 실행 될 것으로 예상 되는 쿼리 수에 따라 지 수 비용을 곱합니다. 

예를 들어, 워크 로드에 쿼리가 ``SELECT * FROM c WHERE c.id = 'Alice'`` 필요한 경우 초당 100 번 실행 되 고 쿼리의 사용 요금은 10 RUs 인 경우 이러한 요청을 처리 하려면 100 query/sec * 10 r u/쿼리 = 1000 r u/초를 사용 해야 합니다. 작업에서 발생 하는 읽기 또는 쓰기에 필요한 r o s/s를 추가 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 가격 책정 모델](how-pricing-works.md)에 대해 자세히 알아보세요.
* 새 [Cosmos 계정, 데이터베이스 및 컨테이너](create-cosmosdb-resources-portal.md)를 만듭니다.
* [프로 비전 된 처리량 비용을 최적화](optimize-cost-throughput.md)하는 방법을 알아봅니다.
* [예약 된 용량으로 비용을 최적화](cosmos-db-reserved-capacity.md)하는 방법을 알아봅니다.

