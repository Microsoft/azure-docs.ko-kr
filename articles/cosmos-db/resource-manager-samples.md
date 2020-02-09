---
title: Azure Cosmos DB에 대한 Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB을 만들고 구성 합니다.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961849"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 Azure Resource Manager 템플릿

다음 표에는 Azure Cosmos DB에 대한 Azure Resource Manager 템플릿에 대한 링크가 포함 되어 있습니다.

## <a name="sql-core-api"></a>SQL(Core) API

|**템플릿**|**설명**|
|---|---|
|[Azure Cosmos 계정, 데이터베이스, 컨테이너 만들기](manage-sql-with-resource-manager.md#create-resource) | 이 템플릿은 공유 데이터베이스 처리량이 있는 두 개의 컨테이너와 전용 처리량을 가진 컨테이너를 사용 하 여 두 지역에 SQL (Core) API 계정을 만듭니다. 업데이트 된 처리량 속성 값으로 템플릿을 다시 전송 하 여 처리량을 업데이트할 수 있습니다. |
|[저장 프로시저, 트리거 및 UDF를 사용 하 여 Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](manage-sql-with-resource-manager.md#create-sproc) | 이 템플릿에서는 컨테이너에 대한 저장 프로시저, 트리거 및 UDF를 사용 하 여 두 지역에 SQL (Core) API 계정을 만듭니다. |

## <a name="mongodb-api"></a>MongoDB API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, 데이터베이스, 컬렉션 만들기](manage-mongodb-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 활성화 된 두 지역에서 MongoDB 용 Azure Cosmos DB API를 사용 하 여 계정을 만듭니다. Azure Cosmos 계정에는 데이터베이스 수준 처리량을 공유 하는 두 개의 컨테이너가 있습니다. |

## <a name="cassandra-api"></a>Cassandra API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos account, keyspace, table 만들기](manage-cassandra-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 설정 된 두 지역에 Cassandra API 계정을 만듭니다. Azure Cosmos 계정에는 keyspace 수준 처리량을 공유 하는 두 개의 테이블이 포함 됩니다. |

## <a name="gremlin-api"></a>Gremlin API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos 계정, 데이터베이스, 그래프 만들기](manage-gremlin-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 활성화 된 두 지역에 Gremlin API 계정을 만듭니다. Azure Cosmos 계정에는 데이터베이스 수준 처리량을 공유 하는 두 개의 그래프가 있습니다. |

## <a name="table-api"></a>테이블 API

|**템플릿**|**설명**|
|---| ---|
|[Azure Cosmos account, 테이블을 만듭니다.](manage-table-with-resource-manager.md#create-resource) | 이 템플릿은 다중 마스터가 설정 된 두 지역에 Table API 계정을 만듭니다. Azure Cosmos 계정에는 단일 테이블이 포함 됩니다. |

> [!TIP]
> Table API를 사용할 때 공유 처리량을 사용 하도록 설정 하려면 Azure Portal에서 계정 수준 처리량을 사용 하도록 설정 합니다.

참조 설명서는 [Azure Cosmos DB 페이지 Azure Resource Manager 참조](/azure/templates/microsoft.documentdb/allversions) 를 참조 하세요.
