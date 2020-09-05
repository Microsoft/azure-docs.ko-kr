---
title: Azure Cosmos DB의 API for MongoDB를 사용하여 글로벌 배포를 설정하는 자습서
description: Azure Cosmos DB의 API for MongoDB를 사용하여 글로벌 배포를 설정하는 방법을 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: e167ccf1717c539dc676d85a19eb6eacc3ec0bda
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021122"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB를 사용하여 글로벌 분산 데이터베이스 설정

이 문서에서는 Azure Portal을 통해 Azure Cosmos DB의 API for MongoDB를 사용하여 글로벌 분산 데이터베이스를 설정하고 연결하는 방법을 보여줍니다.

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * [Azure Cosmos DB의 API for MongoDB](mongodb-introduction.md)를 사용하여 글로벌 배포 구성

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>지역별 설정 확인 
Cosmos DB의 API for MongoDB를 사용하는 글로벌 구성을 확인하는 간단한 방법은 Mongo Shell에서 *isMaster()* 명령을 실행하는 것입니다.

Mongo Shell에서 다음을 수행합니다.

   ```
      db.isMaster()
   ```
   
결과 예:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>기본 설정 지역에 연결 

Azure Cosmos DB의 API for MongoDB를 통해 전역적으로 분산된 데이터베이스에 대한 컬렉션의 읽기 기본 설정을 지정할 수 있습니다. 짧은 대기 시간 읽기 및 글로벌 고가용성을 위해 컬렉션의 읽기 기본 설정을 *nearest*(최근접)로 설정하는 것이 좋습니다. *nearest*(최근접)의 읽기 기본 설정은 가장 가까운 지역에서 읽도록 구성됩니다.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

주 읽기/쓰기 지역 및 재해 복구(DR) 시나리오를 위한 보조 지역이 있는 애플리케이션에는 컬렉션의 읽기 기본 설정을 *secondary preferred*(보조 기본 설정)로 설정하는 것이 좋습니다. *secondary preferred*(보조 기본 설정)의 읽기 기본 설정은 주 지역의 데이터를 사용할 수 없는 경우 보조 지역에서 읽도록 구성됩니다.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

마지막으로 읽기 지역을 수동으로 지정하려고 합니다. 읽기 기본 설정 내에서 지역 태그를 설정할 수 있습니다.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

이것으로 끝이며, 이 자습서를 완료했습니다! [Azure Cosmos DB의 일관성 수준](consistency-levels.md)을 참조하여 전역적으로 복제한 계정의 일관성을 관리하는 방법에 대해 알아볼 수 있습니다. 그리고 Azure Cosmos DB에서 전역 데이터베이스 복제가 작동하는 방법에 대한 자세한 내용은 [Azure Cosmos DB를 사용하여 전역적으로 데이터 배포](distribute-data-globally.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * Cosmos DB의 API for MongoDB를 사용하여 글로벌 배포 구성

이제 다음 자습서로 진행하여 Azure Cosmos DB 로컬 에뮬레이터를 사용하여 로컬로 개발하는 방법에 대해 자세히 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB 에뮬레이터를 사용하여 로컬로 개발](local-emulator.md)
