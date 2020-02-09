---
title: MongoDB 용 Azure Cosmos DB API를 사용한 마이그레이션 후 최적화 단계
description: 이 문서는 MongoDB에서 Mongo DB 용 APi Azure Cosmos DB로의 마이그레이션 후 최적화 기술을 제공 합니다.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: lbosq
ms.openlocfilehash: 3a8da5df4c661a160c7ace37144f6ac1a9859da2
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942272"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대한 Azure Cosmos DB API를 사용 하는 경우 마이그레이션 후 최적화 단계 

MongoDB 데이터베이스에 저장된 데이터를 MongoDB용 Azure Cosmos DB의 API로 마이그레이션한 후 Azure Cosmos DB에 연결하고 데이터를 관리할 수 있습니다. 이 가이드는 마이그레이션 후에 고려해 야 하는 단계를 제공 합니다. 마이그레이션 단계는 [MongoDB to Azure Cosmos DB의 API For MongoDB API 자습서](../dms/tutorial-mongodb-cosmos-db.md) 를 참조 하세요.

이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.
- [응용 프로그램 연결](#connect-account)
- [인덱싱 정책 최적화](#indexing)
- [MongoDB에 대한 Azure Cosmos DB API에 대한 전역 배포 구성](#distribute-data)
- [일관성 수준 설정](#consistency)

> [!NOTE]
> 응용 프로그램 수준의 필수 마이그레이션 후 단계는 새 Azure Cosmos DB 계정을 가리키도록 응용 프로그램의 연결 문자열을 변경 하는 것입니다. 다른 모든 마이그레이션 단계를 최적화 하는 것이 좋습니다.
>

## <a id="connect-account"></a>응용 프로그램 연결 

1. 새 창에서 [Azure Portal](https://www.portal.azure.com/) 에 로그인 합니다.
2. [Azure Portal](https://www.portal.azure.com/)의 왼쪽 창에서 **모든 리소스** 메뉴를 열고 데이터를 마이그레이션한 Azure Cosmos DB 계정을 찾습니다.
3. **연결 문자열** 블레이드를 엽니다. 오른쪽 창에는 계정에 성공적으로 연결하는 데 필요한 모든 정보가 포함되어 있습니다.
4. 응용 프로그램의 구성 (또는 기타 관련 위치)에 있는 연결 정보를 사용 하 여 앱의 MongoDB 연결에 대한 Azure Cosmos DB API를 반영 합니다. 
![연결 문자열](./media/mongodb-post-migration/connection-string.png)

자세한 내용은 [Azure Cosmos DB에 MongoDB 응용 프로그램 연결](connect-mongodb-account.md) 페이지를 참조 하세요.

## <a id="indexing"></a>인덱싱 정책 최적화

기본적으로 모든 데이터 필드는 Azure Cosmos DB 데이터를 마이그레이션하는 동안 자동으로 인덱싱됩니다. 대부분의 경우이 기본 인덱싱 정책을 사용할 수 있습니다. 일반적으로 인덱스를 제거 하면 쓰기 요청이 최적화 되 고 기본 인덱싱 정책 (즉, 자동 인덱싱)이 읽기 요청을 최적화 합니다.

인덱싱에 대한 자세한 내용은 [MongoDB에 대한 Azure Cosmos DB의 API의 데이터 인덱싱](mongodb-indexing.md) 및 Azure Cosmos DB 문서의 [인덱싱](index-overview.md) 을 참조 하세요.

## <a id="distribute-data"></a>데이터를 전역적으로 배포

Azure Cosmos DB은 전 세계 모든 [Azure 지역](https://azure.microsoft.com/regions/#services) 에서 사용할 수 있습니다. Azure Cosmos DB 계정에 대한 기본 일관성 수준을 선택한 후에는 글로벌 배포 요구 사항에 따라 하나 이상의 Azure 지역에 연결할 수 있습니다. 고가용성 및 비즈니스 연속성을 위해 항상 두 개 이상의 지역에서를 실행 하는 것이 좋습니다. [Azure Cosmos DB에서 다중 지역 배포의 비용을 최적화](optimize-cost-regions.md)하기 위한 팁을 검토할 수 있습니다.

데이터를 전역적으로 배포 하려면 [MongoDB에 대한 Azure Cosmos DB의 API에서 전역적으로 데이터 배포](tutorial-global-distribution-mongodb.md)를 참조 하세요. 

## <a id="consistency"></a>일관성 수준 설정
Azure Cosmos DB는 잘 정의 된 5 가지 [일관성 수준을](consistency-levels.md)제공 합니다. MongoDB와 Azure Cosmos DB 일관성 수준 간의 매핑에 대한 자세한 내용은 읽기 [일관성 수준 및 Azure Cosmos DB api](consistency-levels-across-apis.md)를 참조 하세요. 기본 일관성 수준은 세션 일관성 수준입니다. 일관성 수준을 변경 하는 것은 선택 사항이 며 앱에 맞게 최적화할 수 있습니다. Azure Portal를 사용 하 여 일관성 수준을 변경 하려면:

1. 설정에서 **기본 일관성** 블레이드로 이동 합니다.
2. [일관성 수준](consistency-levels.md) 선택

대부분의 사용자는 기본 세션 일관성 설정에서 일관성 수준을 유지 합니다. 그러나 [다양 한 일관성 수준에 대한 가용성 및 성능 절충](consistency-levels-tradeoffs.md)은 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md)
* [Studio 3T를 사용 하 여 Azure Cosmos DB 계정에 연결](mongodb-mongochef.md)
* [MongoDB에 대한 Azure Cosmos DB API를 사용 하 여 읽기를 전역적으로 배포 하는 방법](mongodb-readpreference.md)
* [Azure Cosmos DB의 MongoDB API를 사용하여 데이터 만료](mongodb-time-to-live.md)
* [Azure Cosmos DB의 일관성 수준](consistency-levels.md)
* [Azure Cosmos DB의 인덱싱](index-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)





