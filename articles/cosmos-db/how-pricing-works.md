---
title: Azure Cosmos DB의 가격 책정 모델
description: 이 문서에서는 Azure Cosmos DB의 가격 책정 모델과 이 모델로 비용 관리 및 비용 계획을 간소화하는 방법을 설명합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: rimman
ms.openlocfilehash: 997a80ed1a8089c5255292f23bc5dacf8a6cb0e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059676"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB의 가격 책정 모델 

Azure Cosmos DB의 가격 책정 모델은 비용 관리 및 계획을 간소화합니다. Azure Cosmos DB를 사용하면 프로비전된 처리량 및 사용하는 스토리지의 요금을 해야 합니다.

* **프로비전된 처리량**: 프로비전된 처리량(예약된 처리량이라고도 함)은 모든 규모에서 높은 성능을 보장합니다. 필요한 처리량(RU/초)을 지정하면 Azure Cosmos DB는 구성된 처리량을 보장하는 데 필요한 리소스를 전담 투입합니다. 지정된 시간의 최대 프로비전된 처리량을 기준으로 시간별로 요금이 청구됩니다.

   > [!NOTE]
   > 프로비전된 처리량 모델은 컨테이너 또는 데이터베이스에 리소스를 전담으로 투입하므로 워크로드를 실행하지 않더라도 프로비전된 처리량에 대해 비용이 부과됩니다.

* **사용된 스토리지**: 지정된 1시간 동안 데이터 및 인덱스에 사용된 총 스토리지 양(GB)에 대해 정액 요금이 부과됩니다.

초당 [요청 단위](request-units.md)(RU/초를)로 지정되는 프로비전된 처리량을 사용하여 컨테이너 또는 데이터베이스에서 데이터를 읽고 쓸 수 있습니다. [데이터베이스 또는 컨테이너에 대해 처리량을 프로비전](set-throughput.md)할 수 있습니다. 워크로드 요구 수준에 따라, 언제든지 처리량을 확장/축소할 수 있습니다. Azure Cosmos DB 가격 책정은 탄력적이며 데이터베이스 또는 컨테이너에 대해 구성하는 처리량에 비례합니다. 최소 처리량 및 스토리지 값과 크기 조정 증분은 소규모 컨테이너부터 대규모 컨테이너까지 모든 고객 부문에 전체 가격 및 탄력성 범위를 제공합니다. 각 데이터베이스 또는 컨테이너 요금은 최소 크기가 400RU/초인 100 RU/초 단위로 프로비전되는 처리량과 사용된 스토리지(GB)에 따라 시간별로 청구됩니다. 프로비전된 처리량과 달리, 스토리지 요금은 소비량을 기준으로 청구됩니다. 즉, 스토리지를 미리 예약할 필요가 없습니다. 사용한 스토리지에 대해서만 요금이 청구됩니다.

자세한 내용은 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 및 [Azure Cosmos DB 청구서 이해](understand-your-bill.md)를 참조하세요.

Azure Cosmos DB의 가격 책정 모델은 모든 API에서 일치합니다. 자세한 내용은 [Azure Cosmos DB 가격 책정 모델이 고객에게 경제적인 방식](total-cost-ownership.md)을 참조하세요. SLA를 보장하기 위해 데이터베이스 또는 컨테이너에 대해 필요한 최소 처리량이 있으며, 100RU/초당 $6가 부과되는 프로비전된 처리량을 늘리거나 줄일 수 있습니다.

현재, 데이터베이스 및 컨테이너 기반 처리량 둘 다에 대한 최소 가격은 월 $24입니다. 최신 정보는 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요. 워크로드가 여러 컨테이너를 사용하는 경우 데이터베이스 수준 처리량을 사용하면 데이터베이스의 컨테이너가 개수에 제한없이 처리량을 공유할 수 있으므로 비용을 최적화할 수 있습니다. 다음 표에서는 여러 다른 엔터티에 대한 프로비전된 처리량 및 비용을 요약해서 보여 줍니다.

|**엔터티**  | **최소 처리량 및 비용** |**눈금 증분 및 비용** |**프로비전 범위** |
|---------|---------|---------|-------|
|데이터베이스    | 400RU/초($24/월)    | 100RU/초($6/월)   |처리량은 데이터베이스용으로 예약되고, 데이터베이스 내의 컨테이너에서 공유됩니다. |
|컨테이너     | 400RU/초($24/월)    | 100RU/초($6/월)  |처리량은 특정 컨테이너용으로 예약됩니다. |

위 표와 같이, Azure Cosmos DB의 최소 처리량은 $24/월부터 시작합니다. 최소 처리량부터 시작하면서 프로덕션 워크로드를 지원하도록 서서히 확장할 경우 비용이 $6/월 단위로 매끄럽게 증가합니다. Azure Cosmos DB의 가격 책정 모델은 탄력적이며 확장 또는 축소함에 따라 비용도 매끄럽게 증가하거나 감소합니다.

## <a name="try-azure-cosmos-db-for-free"></a>무료로 Azure Cosmos DB 사용해 보기 

Azure Cosmos DB는 개발자를 위해 몇 가지 옵션을 무료로 제공합니다. 이러한 옵션에는 다음이 포함됩니다.

* **Azure 체험 계정**: Azure는 처음 30일 동안 $200의 Azure 크레딧과 12개월 동안 무료 서비스라는 제한된 수량을 제공하는 [무료 계층](https://azure.microsoft.com/free/)을 제공합니다. 자세한 내용은 [Azure 체험 계정](../billing/billing-avoid-charges-free-account.md)을 참조하세요. Azure Cosmos DB의 Azure 체험 계정의 일부로 제공됩니다. Azure Cosmos DB와 관련해서 이 체험 계정은 1년 동안 5GB의 스토리지와 400RU의 프로비전된 처리량을 제공합니다. 

* **무료로 Azure Cosmos DB 사용해 보기** Azure Cosmos DB는 체험 계정이 있을 때 Azure Cosmos DB를 사용해볼 수 있는 시간 제한 체험을 제공합니다. 빠른 시작 및 자습서를 사용하여 Azure Cosmos DB 계정을 만들고, 데이터베이스 및 컬렉션을 만들고, 애플리케이션 예제를 실행할 수 있습니다. Azure 계정에 가입하거나 신용 카드를 사용하지 않고 애플리케이션 예제를 실행할 수 있습니다. [Try Azure Cosmos DB for free](https://azure.microsoft.com/try/cosmosdb/)(Azure Cosmos DB 평가판 사용하기)를 통해 1개월간 횟수 제한 없이 계정을 갱신하면서 Azure Cosmos DB를 체험해 볼 수 있습니다.

* **Azure Cosmos DB 에뮬레이터**: Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 안정적인 에뮬레이터가 무료로 클라우드 서비스에 제공됩니다. Azure Cosmos DB 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 애플리케이션을 테스트할 수 있습니다. 프로덕션으로 전환하기 전에 로컬로 에뮬레이터를 사용하여 애플리케이션을 개발할 수 있습니다. 에뮬레이터를 사용해본 결과, 애플리케이션의 기능이 만족스러우면 클라우드의 Azure Cosmos DB 계정 사용으로 전환하면 비용을 크게 절약할 수 있습니다. 에뮬레이터에 대한 자세한 내용은 [개발 및 테스트에 Azure Cosmos DB 사용](local-emulator.md)을 참조하세요.

## <a name="pricing-with-reserved-capacity"></a>예약 용량이 있는 가격

Azure Cosmos DB [예약 용량](cosmos-db-reserved-capacity.md)을 통해 1년 또는 3년 동안 Azure Cosmos DB 리소스 비용을 선불로 지불하면 경비를 절약할 수 있습니다. 1년 또는 3년 간의 사전 약정을 비용을 획기적으로 절감하고 정상 가격에 비해 20~65%의 할인 혜택을 받을 수 있습니다. Azure Cosmos DB 예약 용량을 사용하면 1년 또는 3년 기간 동안 프로비전된 처리량(RU/s) 비용을 선불로 지불하고 프로비전된 처리량에 대해 할인을 받아 비용을 절약할 수 있습니다. 

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
