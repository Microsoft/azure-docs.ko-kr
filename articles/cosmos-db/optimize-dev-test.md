---
title: Azure Cosmos DB에서 개발 및 테스트 최적화
description: 이 문서에서는 Azure Cosmos DB에서 서비스의 개발 및 테스트를 위해 여러 가지 옵션을 무료로 제공하는 방법을 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 9390f8a2ab9372927b434ea94d7545c9ec540c58
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606263"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 개발 및 테스트 비용 최적화

이 문서에서는 개발 및 테스트를 위해 Azure Cosmos DB 사용 하는 다양 한 옵션과 개발 또는 테스트 계정 비용을 최적화 하는 기술에 대해 설명 합니다.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB 에뮬레이터(로컬로 다운로드 가능한 버전)

[Azure Cosmos DB 에뮬레이터](local-emulator.md)는 Azure Cosmos DB 클라우드 서비스를 모방하는, 로컬로 다운로드 가능한 버전입니다. 네트워크 연결이 없어도 비용 발생 없이 Azure Cosmos DB API를 사용하는 코드를 작성하고 테스트할 수 있습니다. Azure Cosmos DB 에뮬레이터는 클라우드 서비스와 유사한, 개발 목적의 로컬 환경을 제공합니다. Azure 구독을 만들지 않고 로컬에서 애플리케이션을 개발하고 테스트할 수 있습니다. 애플리케이션을 클라우드에 배포할 준비가 되면 클라우드의 Azure Cosmos DB 엔드포인트에 연결하도록 연결 문자열을 업데이트하면 됩니다. 다른 수정은 필요하지 않습니다. 테스트 실행을 위해 Azure DevOps에서 [Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하여 CI/CD 파이프라인을 설정](tutorial-setup-ci-cd.md)할 수도 있습니다. [Azure Cosmos DB 에뮬레이터](local-emulator.md) 문서를 방문하여 시작할 수 있습니다.

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB 무료 계층

Azure Cosmos DB 무료 계층을 사용 하면 쉽게 시작 하 고, 응용 프로그램을 개발 하 고 테스트 하거나, 소규모 프로덕션 워크 로드를 무료로 실행할 수 있습니다. 계정에서 무료 계층을 사용 하는 경우 계정에서 처음 400 r u/s 및 5gb의 저장소를 무료로 얻을 수 있습니다. 또한 데이터베이스 수준에서 400 o s/s를 공유 하는 25 개의 컨테이너를 사용 하 여 공유 처리량 데이터베이스를 만들 수 있습니다 .이는 무료 계층 (무료 계층 계정에서 5 개의 공유 처리량 데이터베이스 제한)을 포함 합니다. 무료 계층을 사용 하는 경우 최소 처리량이 400 r u/초 인 공유 데이터베이스를 프로 비전 하는 경우 해당 데이터베이스 내의 모든 컨테이너는 처리량을 공유할 수 있습니다. 공유 처리량을 포함 하는 모든 새 데이터베이스 또는 전용 처리량의 컨테이너는 정기적인 가격으로 청구 됩니다.

> [!NOTE]
> 무료 계층은 프로 비전 된 처리량 모드 에서만 사용할 수 있습니다.

무료 계층은 계정 수명 동안 무기한으로 지속 되며 무제한 저장소 및 처리량 (r u/초), Sla, 고가용성, 턴키 전역 배포를 비롯 하 여 모든 Azure 지역에서 일반 Azure Cosmos DB 계정의 모든 [이점과 기능](introduction.md#key-benefits) 을 제공 합니다. Azure 구독 당 최대 1 개의 무료 계층 계정을 가질 수 있으며 계정을 만들 때 옵트인 해야 합니다. 시작 하려면 [무료 계층이 사용 하도록 설정 된 Azure Portal에서 새 계정을 만들거나](create-cosmosdb-resources-portal.md) [ARM 템플릿을](manage-sql-with-resource-manager.md#free-tier)사용 합니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

## <a name="try-azure-cosmos-db-for-free"></a>무료로 Azure Cosmos DB 사용해 보기

[무료로 Azure Cosmos DB 체험](https://azure.microsoft.com/try/cosmosdb/) 은 Azure 계정에 등록 하거나 신용 카드를 사용 하지 않고도 클라우드에서 Azure Cosmos DB를 시험해 볼 수 있는 무료 무료 환경입니다. 시도 Azure Cosmos DB 계정은 제한 된 시간 (현재 30 일) 동안 사용할 수 있습니다. 언제든지 계정을 갱신할 수 있습니다. Azure Cosmos DB 계정을 사용해 보기 쉽게 Azure Cosmos DB를 평가 하 고, 응용 프로그램을 빌드 및 테스트 하거나 빠른 시작 또는 자습서를 사용할 수 있습니다. 데모를 만들거나 유닛 테스트를 수행 하거나 여러 지역 계정을 만들어 비용을 들이지 않고 앱을 실행할 수도 있습니다. 시도 Azure Cosmos DB 계정에는 최대 25 개의 컨테이너와 2만 u s/s의 처리량을 포함 하는 하나의 공유 처리량 데이터베이스 또는 최대 5000 r u/s를 포함 하는 하나의 컨테이너가 있을 수 있습니다. 시작하려면 [Azure Cosmos DB 체험해 보기](https://azure.microsoft.com/try/cosmosdb/)를 참조하세요.

## <a name="azure-free-account"></a>Azure 무료 체험 계정

Azure Cosmos DB는 Azure 크레딧과 리소스를 일정 기간 동안 무료로 제공하는 [Azure 체험 계정](https://azure.microsoft.com/free)에 포함됩니다. Azure Cosmos DB와 관련해서 이 체험 계정은 1년 동안 5GB의 스토리지와 400RU의 프로비전된 처리량을 제공합니다. 이 환경을 통해 개발자는 비용 없이 Azure Cosmos DB의 기능을 쉽게 테스트하거나 다른 Azure 서비스와 통합할 수 있습니다. Azure 체험 계정과 함께 처음 30일 동안 사용할 수 있는 $200 크레딧을 받게 됩니다. 서비스 사용이 시작된 후에도 업그레이드를 선택하기 전에는 요금이 청구되지 않습니다. 시작하려면 [Azure 체험 계정](https://azure.microsoft.com/free) 페이지를 방문하세요.

## <a name="azure-cosmos-db-serverless"></a>서버를 사용 하지 않는 Azure Cosmos DB

[서버](serverless.md) 를 사용 하지 않는 Azure Cosmos DB 데이터베이스 작업에서 사용한 요청 단위 및 데이터에서 사용 하는 저장소에 대해서만 요금이 부과 되는 소비 기반 방식으로 Azure Cosmos 계정을 사용할 수 있습니다. 서버를 사용 하지 않는 모드로 Azure Cosmos DB를 사용 하는 경우에는 최소한의 요금이 부과 되지 않습니다. 프로 비전 된 용량의 개념을 제거 하기 때문에 대부분의 시간 동안 데이터베이스가 유휴 상태일 때 개발 또는 테스트 작업에 가장 적합 합니다.

## <a name="use-shared-throughput-databases"></a>공유 처리량 데이터베이스 사용

[공유 처리량 데이터베이스](set-throughput.md#set-throughput-on-a-database)에서 데이터베이스 내의 모든 컨테이너는 데이터베이스의 프로 비전 된 처리량 (r u/초)을 공유 합니다. 예를 들어 400 u s e/s를 사용 하 여 데이터베이스를 프로 비전 하 고 4 개의 컨테이너를 포함 하는 경우 4 개의 컨테이너는 모두 400 r u/s를 공유 합니다. 개발 또는 테스트 환경에서 각 컨테이너에 자주 액세스 하지 않으므로 최소 400 r u/초 보다 낮은 값이 필요한 경우 공유 처리량 데이터베이스에 컨테이너를 배치 하면 비용을 최적화할 수 있습니다.

예를 들어 개발 또는 테스트 계정에 4 개의 컨테이너가 있다고 가정 합니다. 전용 처리량을 사용 하 여 4 개의 컨테이너 (최소 400 r u/초)를 만드는 경우 총 r u/초는 1600 r u/초입니다. 반면 공유 처리량 데이터베이스 (최소 400 r u/초)를 만들고 컨테이너를 배치 하는 경우 총 r u/초는 400 r u/초입니다. 일반적으로 공유 처리량 데이터베이스는 개별 컨테이너에 대해 보장 된 처리량이 필요 하지 않은 시나리오에 적합 합니다.  [공유 처리량 데이터베이스](set-throughput.md#set-throughput-on-a-database) 에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 에뮬레이터 또는 Azure Cosmos DB 체험 계정 사용을 시작할 수 있습니다.

* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* 서버를 사용 하지 않는 [Azure Cosmos DB](serverless.md) 에 대 한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](optimize-cost-queries.md)에 대한 자세한 정보
* [다중 지역 Azure Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보
