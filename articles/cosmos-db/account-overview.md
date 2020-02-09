---
title: Azure Cosmos DB 계정으로 작업
description: 이 문서에서는 Azure Cosmos 계정을 만들고 사용 하는 방법을 설명 합니다. 또한 Azure Cosmos 계정에 있는 요소의 계층 구조를 보여 줍니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: d29ed68b2945b2473b33aa88176e6f5d832a0fba
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872267"
---
# <a name="work-with-azure-cosmos-account"></a>Azure Cosmos 계정 작업

Azure Cosmos DB는 완전 관리형 PaaS(platform-as-a-service)입니다. Azure Cosmos DB를 사용하려면 먼저 Azure 구독에서 Azure Cosmos 계정을 만들어야 합니다. Azure Cosmos 계정에는 고유한 DNS 이름이 포함되어 있으며, Azure Portal 및 Azure CLI를 사용하여 또는 다양한 언어별 SDK를 사용하여 계정을 관리할 수 있습니다. 자세한 내용은 [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)을 참조하세요.

Azure Cosmos 계정은 글로벌 배포와 고가용성을 위한 기본 단위입니다. 데이터를 여러 Azure 지역에 걸쳐 글로벌 배포하려면 언제든지 Azure Cosmos 계정에서 Azure 지역을 추가하거나 제거할 수 있습니다. Azure Cosmos 계정이 단일 또는 복수 쓰기 지역을 갖도록 구성할 수 있습니다. 자세한 내용은 [Azure Cosmos 계정에서 Azure 지역을 추가하고 제거하는 방법](how-to-manage-database-account.md)을 참조하세요. Azure Cosmos 계정에서 [기본 일관성 ](consistency-levels.md) 수준을 구성할 수 있습니다. Azure Cosmos DB는 처리량 99번째 백분위수의 대기 시간, 일관성 및 고가용성을 포함하는 종합적인 SLA를 제공합니다. 자세한 내용은 [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)를 참조하세요.

Azure Cosmos 계정 내의 모든 데이터에 대한 액세스를 안전 하 게 관리 하려면 계정에 연결 된 [마스터 키](secure-access-to-data.md) 를 사용할 수 있습니다. 데이터에 대한 액세스를 더욱 안전 하 게 보호 하기 위해 Azure Cosmos 계정에서 [VNET 서비스 엔드포인트](vnet-service-endpoint.md) 및 [IP 방화벽](firewall-support.md) 을 구성할 수 있습니다. 

## <a name="elements-in-an-azure-cosmos-account"></a>Azure Cosmos 계정의 요소

Azure Cosmos 컨테이너는 확장성의 기본 단위입니다. 하나의 컨테이너는 무제한으로 프로비전된 처리량(RU/s)과 스토리지를 무제한으로 가질 수 있습니다. Azure Cosmos DB는 사용자가 지정한 논리 파티션을 사용하여 컨테이너를 투명하게 분할함으로써 프로비전된 처리량과 스토리지를 탄력적으로 확장합니다. 자세한 내용은 [Azure Cosmos 컨테이너와 항목으로 작업](databases-containers-items.md)을 참조하세요.

Azure 구독을 사용하면 현재 Azure Cosmos 계정을 최대 100개까지 만들 수 있습니다. 하나의 Azure Cosmos 계정은 데이터와 프로비전된 처리량을 무제한으로 관리할 수 있습니다. 계정에서 하나 이상의 Azure Cosmos 데이터베이스를 만들고 이 데이터베이스 내에서 하나 이상의 컨테이너를 만들어서 데이터와 프로비전된 처리량을 관리할 수 있습니다. 다음 그림은 Azure Cosmos 계정의 요소 계층 구조를 보여 줍니다.

![Azure Cosmos 계정의 계층 구조](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정 및 기타 개념을 관리 하는 방법을 알아봅니다.

* [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)
* [글로벌 분포](distribute-data-globally.md)
* [일관성 수준](consistency-levels.md)
* [Azure Cosmos 컨테이너와 항목으로 작업](databases-containers-items.md)
* [Azure Cosmos 계정에 대한 VNET 서비스 엔드포인트](vnet-service-endpoint.md)
* [Azure Cosmos 계정에 대한 IP 방화벽](firewall-support.md)
* [Azure Cosmos 계정에서 Azure 지역을 추가하고 제거하는 방법](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
