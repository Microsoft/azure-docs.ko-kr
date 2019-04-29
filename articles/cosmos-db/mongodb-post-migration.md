---
title: MongoDB에 대 한 Azure Cosmos DB API를 사용 하는 경우 마이그레이션 후 최적화 단계
description: 이 문서에서 마이그레이션 후 최적화 기술이 MongoDB에서 Azure Cosmos DB APi Mongo DB에 대 한 제공합니다.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331218"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB API를 사용 하는 경우 마이그레이션 후 최적화 단계 

MongoDB 데이터베이스에 저장된 데이터를 MongoDB용 Azure Cosmos DB의 API로 마이그레이션한 후 Azure Cosmos DB에 연결하고 데이터를 관리할 수 있습니다. 이 가이드는 마이그레이션 후 고려해 야 하는 단계를 제공 합니다. 참조 된 [MongoDB 자습서에 대 한 Azure Cosmos DB API에 MongoDB 마이그레이션](../dms/tutorial-mongodb-cosmos-db.md) 마이그레이션 단계에 대 한 합니다.

이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.
- [응용 프로그램 연결](#connect-account)
- [인덱싱 정책 최적화](#indexing)
- [MongoDB에 대 한 Azure Cosmos DB API에 대 한 전역 배포 구성](#distribute-data)
- [일관성 수준 설정](#consistency)

> [!NOTE]
> 응용 프로그램 수준에만 필수 마이그레이션 후 단계에는 새 Azure Cosmos DB 계정의를 가리키도록 응용 프로그램에서 연결 문자열을 변경 됩니다. 다른 모든 마이그레이션 단계가 최적화 권장 됩니다.
>

## <a id="connect-account"></a>응용 프로그램 연결 

1. 에 새 창 로그인에는 [Azure portal](https://www.portal.azure.com/)
2. [Azure portal](https://www.portal.azure.com/)를 열고 왼쪽된 창에서를 **모든 리소스** 메뉴 및 데이터를 마이그레이션한 Azure Cosmos DB 계정을 찾습니다.
3. 엽니다는 **연결 문자열** 블레이드입니다. 오른쪽 창에는 계정에 성공적으로 연결하는 데 필요한 모든 정보가 포함되어 있습니다.
4. 앱에서 MongoDB 연결에 대 한 Azure Cosmos DB API에 맞게 응용 프로그램의 구성 (또는 다른 관련 위치)에서 연결 정보를 사용 합니다. 
![Connection-String](./media/mongodb-post-migration/connection-string.png)

자세한 내용은 참조 하십시오 합니다 [Azure Cosmos DB에 MongoDB 응용 프로그램 연결](connect-mongodb-account.md) 페이지입니다.

## <a id="indexing"></a>인덱싱 정책 최적화

모든 데이터 필드는 자동으로 인덱싱되지 기본적으로 Azure Cosmos DB로 데이터 마이그레이션 중입니다. 대부분의 경우이 기본 인덱싱 정책을 적합 합니다. 일반적으로 쓰기 요청을 최적화 인덱스를 제거 하 고 읽기 요청을 최적화 기본 인덱싱 정책 (예: 자동 인덱싱) 필요 합니다.

인덱싱에 대 한 자세한 내용은 참조 하세요. [MongoDB에 대 한 Azure Cosmos DB API에서 데이터를 인덱싱](mongodb-indexing.md) 뿐만 [Azure Cosmos DB의 인덱싱은](index-overview.md) 문서입니다.

## <a id="distribute-data"></a>데이터를 전역적으로 배포

Azure Cosmos DB는 모든 사용 가능한 [Azure 지역](https://azure.microsoft.com/regions/#services) 전 세계입니다. Azure Cosmos DB 계정에 대 한 기본 일관성 수준을 선택한 후 (글로벌 배포 필요)에 따라 하나 이상의 Azure 지역을 연결할 수 있습니다. 고가용성 및 비즈니스 연속성에 대 한 2 개 이상의 지역에서 실행 되는 것이 좋습니다. 에 대 한 팁을 검토할 수 있습니다 [Azure Cosmos DB의 다중 지역 배포의 비용을 최적화](optimize-cost-regions.md)합니다.

데이터를 전역적으로 배포 하려면를 참조 하세요 [MongoDB에 대 한 Azure Cosmos DB API에서 전역적으로 데이터 배포](tutorial-global-distribution-mongodb.md)합니다. 

## <a id="consistency"></a>일관성 수준 설정
Azure Cosmos DB는 잘 정의 된 5 [일관성 수준](consistency-levels.md)합니다. MongoDB 및 Azure Cosmos DB 일관성 수준 간 매핑에 대 한 내용은 읽을 [일관성 수준 및 Azure Cosmos DB Api](consistency-levels-across-apis.md)합니다. 기본 일관성 수준에는 세션 일관성 수준이입니다. 일관성 수준을 변경 하는 것은 선택적 이며 앱 용으로 최적화할 수 있습니다. 변경 하려면 Azure portal을 사용 하는 일관성 수준:

1. 로 이동 합니다 **기본 일관성** 블레이드의 설정 합니다.
2. 선택에 [일관성 수준](consistency-levels.md)

대부분의 사용자는 일관성 수준을 기본 세션 일관성 설정은 그대로 둡니다. 그러나 [다양 한 일관성 수준에 대 한 가용성 및 성능 절충](consistency-levels-tradeoffs.md)합니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md)
* [Studio 3T를 사용 하 여 Azure Cosmos DB 계정에 연결](mongodb-mongochef.md)
* [Azure Cosmos DB API를 사용 하 여 MongoDB에 대 한 전역적으로 배포 하는 방법 읽기](mongodb-readpreference.md)
* [MongoDB에 대 한 Azure Cosmos DB API 사용 하 여 데이터를 만료](mongodb-time-to-live.md)
* [Azure Cosmos DB의 일관성 수준](consistency-levels.md)
* [Azure Cosmos DB의 인덱싱](index-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)





