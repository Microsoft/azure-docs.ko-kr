---
title: Azure Cosmos DB의 가격 책정 모델
description: 이 문서에서는 Azure Cosmos DB의 가격 책정 모델과 이 모델로 비용 관리 및 비용 계획을 간소화하는 방법을 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: a992d240955f42ec030a84c887ba086ce92f9790
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605258"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB의 가격 책정 모델

Azure Cosmos DB의 가격 책정 모델은 비용 관리 및 계획을 간소화합니다. Azure Cosmos DB를 사용 하 여 데이터베이스 및 데이터에서 사용 하는 저장소에 대해 수행 하는 작업에 대해 비용을 지불 합니다.

- **데이터베이스 작업**: 데이터베이스 작업에 대해 부과 되는 방법은 사용 하는 Azure Cosmos 계정 유형에 따라 달라 집니다.

  - **프로 비전**된 처리량: [프로 비전](set-throughput.md) 된 처리량 (예약 된 처리량이 라고도 함)은 모든 규모에서 높은 성능을 보장 합니다. 초당 [요청 단위](request-units.md) (전용/s)에 필요한 처리량을 지정 하 고 구성 된 처리량을 보장 하는 데 필요한 리소스를 Azure Cosmos DB 합니다. [데이터베이스 또는 컨테이너에 대해 처리량을 프로비전](set-throughput.md)할 수 있습니다. 워크 로드 요구 사항에 따라 언제 든 지 처리량을 확장/축소 하거나 [자동 크기 조정을](provision-throughput-autoscale.md) 사용할 수 있습니다 (sla를 보장 하는 데 필요한 최소 처리량은 데이터베이스 또는 컨테이너에만 필요 함). 지정된 시간의 최대 프로비전된 처리량을 기준으로 시간별로 요금이 청구됩니다.

   > [!NOTE]
   > 프로 비전 된 처리량 모델은 리소스를 컨테이너 또는 데이터베이스에 전용 워크 로드를 실행 하지 않는 경우에도 프로 비전 한 처리량에 대해 요금이 청구 됩니다.

  - **서버**를 사용 하지 않음: [서버](serverless.md) 리스 모드에서는 Azure Cosmos 계정에 리소스를 만들 때 처리량을 프로 비전 할 필요가 없습니다. 청구 기간이 종료 되 면 데이터베이스 작업에서 사용한 요청 단위 양에 대 한 요금이 청구 됩니다.

- **저장소**: 지정 된 시간 동안 데이터 및 인덱스에서 사용 하는 총 저장소 크기 (gb)에 대 한 정액 요금이 청구 됩니다. 저장소는 소비 단위로 청구 되므로 저장소를 미리 예약할 필요가 없습니다. 사용한 스토리지에 대해서만 요금이 청구됩니다.

Azure Cosmos DB의 가격 책정 모델은 모든 API에서 일치합니다. 자세한 내용은 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조 하 고, [Azure Cosmos DB 청구서를 이해](understand-your-bill.md) 하 고, [가격 책정 모델을 고객에 게 비용 효율적으로 적용 하는 Azure Cosmos DB 방법을](total-cost-ownership.md)참조 하세요.

Azure Cosmos DB 계정을 미국의 비 정부 지역에 배포 하는 경우 프로 비전 된 처리량 모드의 데이터베이스 및 컨테이너 기반 처리량에 대 한 최소 가격이 있습니다. 서버 리스 모드에서는 최소 가격이 없습니다. 가격은 사용 하 고 있는 지역에 따라 달라 지 며, 최신 가격 책정 정보는 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 를 참조 하세요.

## <a name="try-azure-cosmos-db-for-free"></a>무료로 Azure Cosmos DB 사용해 보기

Azure Cosmos DB는 개발자에 게 무료로 제공 되는 다양 한 옵션을 제공 합니다. 이러한 옵션에는 다음이 포함됩니다.

* **Azure Cosmos DB 무료 계층**: Azure Cosmos DB 무료 계층을 사용 하면 쉽게 시작 하 고, 응용 프로그램을 개발 하 고 테스트 하거나, 소규모 프로덕션 워크 로드를 무료로 실행할 수도 있습니다. 계정에 대 한 무료 계층을 사용 하는 경우 계정 수명 동안 계정에 처음 400 r u/초 및 5gb의 저장소를 무료로 얻을 수 있습니다. Azure 구독 당 최대 1 개의 무료 계층 계정을 가질 수 있으며 계정을 만들 때 옵트인 해야 합니다. 시작 하려면 [무료 계층이 사용 하도록 설정 된 Azure Portal에서 새 계정을 만들거나](create-cosmosdb-resources-portal.md) [ARM 템플릿을](manage-sql-with-resource-manager.md#free-tier)사용 합니다.

* **Azure 무료 계정**: azure는 처음 30 일 동안 azure 크레딧을 $200 제공 하 고 12 개월 동안 무료 서비스의 제한 된 수량을 제공 하는 [무료 계층](https://azure.microsoft.com/free/) 을 제공 합니다. 자세한 내용은 [Azure 체험 계정](../cost-management-billing/manage/avoid-charges-free-account.md)을 참조하세요. Azure Cosmos DB의 Azure 체험 계정의 일부로 제공됩니다. 특히 Azure Cosmos DB의 경우이 무료 계정은 5gb 저장소를 제공 하 고 전체 년도에 대해 프로 비전 된 처리량을 400 r u/초까지 제공 합니다.

* **무료로 Azure Cosmos DB 사용해 보기**: Azure Cosmos DB 체험 계정에 대 한 체험 Azure Cosmos DB를 사용 하 여 시간이 제한 된 환경을 제공 합니다. 빠른 시작 및 자습서를 사용하여 Azure Cosmos DB 계정을 만들고, 데이터베이스 및 컬렉션을 만들고, 애플리케이션 예제를 실행할 수 있습니다. Azure 계정에 가입하거나 신용 카드를 사용하지 않고 애플리케이션 예제를 실행할 수 있습니다. [Try Azure Cosmos DB for free](https://azure.microsoft.com/try/cosmosdb/)(Azure Cosmos DB 평가판 사용하기)를 통해 1개월간 횟수 제한 없이 계정을 갱신하면서 Azure Cosmos DB를 체험해 볼 수 있습니다.

* **Azure Cosmos DB emulator**: Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공 합니다. 안정적인 에뮬레이터가 무료로 클라우드 서비스에 제공됩니다. Azure Cosmos DB 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 애플리케이션을 테스트할 수 있습니다. 프로덕션으로 전환하기 전에 로컬로 에뮬레이터를 사용하여 애플리케이션을 개발할 수 있습니다. 에뮬레이터를 사용해본 결과, 애플리케이션의 기능이 만족스러우면 클라우드의 Azure Cosmos DB 계정 사용으로 전환하면 비용을 크게 절약할 수 있습니다. 에뮬레이터에 대한 자세한 내용은 [개발 및 테스트에 Azure Cosmos DB 사용](local-emulator.md)을 참조하세요.

## <a name="pricing-with-reserved-capacity"></a>예약 용량이 있는 가격

Azure Cosmos DB [예약 된 용량](cosmos-db-reserved-capacity.md) 을 사용 하면 1 년 또는 3 년 동안 Azure Cosmos DB 리소스를 미리 지불 하 여 프로 비전 된 처리량 모드를 사용 하는 경우 비용을 절감할 수 있습니다. 1년 또는 3년 간의 사전 약정을 비용을 획기적으로 절감하고 정상 가격에 비해 20~65%의 할인 혜택을 받을 수 있습니다. Azure Cosmos DB 예약 용량을 사용하면 1년 또는 3년 기간 동안 프로비전된 처리량(RU/s) 비용을 선불로 지불하고 프로비전된 처리량에 대해 할인을 받아 비용을 절약할 수 있습니다. 

예약 용량은 청구 할인을 제공하며, Azure Cosmos DB 리소스의 런타임 상태에는 영향을 주지 않습니다. 예약 용량은 MongoDB, Cassandra, SQL, Gremlin, Azure Tables를 포함하는 모든 API와 전 세계 모든 지역에서 일관되게 사용할 수 있습니다. [예약 용량을 통해 Azure Cosmos DB 리소스에 대한 선불 결제](cosmos-db-reserved-capacity.md) 문서에서 예약 용량에 대해 자세히 알아보고 [Azure Portal](https://portal.azure.com/)에서 예약 용량을 구입할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 Azure Cosmos DB 리소스에 대한 비용을 최적화하는 방법에 대한 추가 정보를 확인할 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](optimize-cost-queries.md)에 대한 자세한 정보
* [다중 지역 Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보
* [Azure Cosmos DB 예약 용량](cosmos-db-reserved-capacity.md)에 대한 자세한 정보
* [Azure Cosmos DB 에뮬레이터](local-emulator.md)에 대한 자세한 정보
