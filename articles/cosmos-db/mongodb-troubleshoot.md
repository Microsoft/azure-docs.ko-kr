---
title: Mongo DB에 대한 Azure Cosmos DB의 API의 일반적인 오류 문제 해결
description: 이 문서에서는 Azure Cosmos DB의 MongoDB API에서 발생 하는 일반적인 문제를 해결 하는 방법을 설명 합니다.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941840"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대한 Azure Cosmos DB API의 일반적인 문제 해결

Azure Cosmos DB는 MongoDB을 포함 하 여 일반적인 NoSQL 데이터베이스의 유선 프로토콜을 구현 합니다. 유선 프로토콜 구현으로 인해 NoSQL 데이터베이스에서 작동 하는 기존 클라이언트 Sdk, 드라이버 및 도구를 사용 하 여 Azure Cosmos DB와 투명 하 게 상호 작용할 수 있습니다. Azure Cosmos DB는 모든 NoSQL 데이터베이스에 대해 유선 호환 Api를 제공 하기 위해 데이터베이스의 소스 코드를 사용 하지 않습니다. 유선 프로토콜 버전을 이해 하는 모든 MongoDB 클라이언트 드라이버는 Azure Cosmos DB에 연결할 수 있습니다.

MongoDB에 대한 Azure Cosmos DB API는 MongoDB의 유선 프로토콜 3.2 버전과 호환 되지만, 버전 3.4에 추가 된 쿼리 연산자 및 기능은 현재 미리 보기로 제공 됩니다 .에 해당 하는 일부 사용자 지정 오류 코드가 있습니다 Azure Cosmos DB 특정 오류. 이 문서에서는 다양 한 오류, 오류 코드 및 해당 오류를 해결 하는 단계에 대해 설명 합니다.

## <a name="common-errors-and-solutions"></a>일반적인 오류 및 해결 방법

| 오류               | 코드  | Description  | 솔루션  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 사용된 총 요청 단위 수가 컬렉션에 프로비전된 요청 단위 비율을 초과하여 제한되었습니다. | Azure Portal에서 컨테이너 또는 컨테이너 집합에 할당 된 처리량을 확장 하거나 작업을 다시 시도할 수 있습니다. |
| ExceededMemoryLimit | 16501 | 다중 테넌트 서비스로써 작업이 클라이언트의 메모리 할당량을 초과했습니다. | [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 보다 제한적인 쿼리 조건을 통해 작업 범위를 줄이거나 고객 지원에 문의하세요. 예: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 지정 된 order by 항목에 해당 하는 인덱스 경로가 제외 되거나 order by 쿼리에 해당 하는 복합 인덱스가 제공 되지 않습니다. | 2 | 쿼리에서 인덱싱되지 않은 필드에 대한 정렬을 요청 합니다. | 시도 중인 정렬 쿼리에 대해 일치 하는 인덱스 (또는 복합 인덱스)를 만듭니다. |
| MongoDB 유선 버전 문제 | - | 이전 버전의 MongoDB 드라이버는 연결 문자열에서 Azure Cosmos 계정의 이름을 검색할 수 없습니다. | 추가 *appName = @**accountName**@* Cosmos DB의 API에 대한 MongoDB 연결 문자열의 끝에 있는 ***accountName*** 은 Cosmos DB 계정 이름 . |


## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](mongodb-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.

