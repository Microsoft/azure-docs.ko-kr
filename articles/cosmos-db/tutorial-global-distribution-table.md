---
title: Table API의 Azure Cosmos DB 글로벌 배포 자습서
description: Azure Cosmos DB Table API 계정에서 글로벌 배포가 작동하는 방법 및 기본 지역 목록을 구성하는 방법 알아보기
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900187"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Table API를 사용하여 Azure Cosmos DB 전역 배포 설정

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * [Table API](table-introduction.md)를 사용하여 전역 배포 구성

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Table API를 사용하여 기본 설정 지역에 연결

[글로벌 배포](distribute-data-globally.md)를 활용하려면 클라이언트 애플리케이션이 애플리케이션을 실행하는 현재 위치를 지정해야 합니다. 이 작업은 `CosmosExecutorConfiguration.CurrentRegion` 속성을 설정하여 수행됩니다. `CurrentRegion` 속성은 단일 위치를 포함해야 합니다. 각 클라이언트 인스턴스는 짧은 대기 시간 읽기를 위해 자체 지역을 지정할 수 있습니다. “미국 서부”와 같은 [표시 이름](https://msdn.microsoft.com/library/azure/gg441293.aspx)을 사용하여 지역 이름을 지정해야 합니다. 

Azure Cosmos DB Table API SDK는 계정 구성 및 현재 가용성에 따라 최상의 엔드포인트를 선택하여 통신합니다. 클라이언트에 더 나은 대기 시간을 제공하기 위해 가장 가까운 지역의 우선 순위를 지정합니다. 현재 `CurrentRegion` 속성을 설정한 후 읽기 및 쓰기 요청은 다음과 같이 전달됩니다.

* **읽기 요청:** 모든 읽기 요청은 구성된 `CurrentRegion`에 전송됩니다. 근접성에 따라 SDK는 고가용성을 위해 대체 지역에서 복제된 대체 지역을 자동으로 선택합니다.

* **쓰기 요청:** SDK는 현재 쓰기 지역에 모든 쓰기 요청을 자동으로 보냅니다. 다중 마스터 계정에서 현재 지역은 쓰기 요청에도 서비스를 제공합니다. 근접성에 따라 SDK는 고가용성을 위해 대체 지역에서 복제된 대체 지역을 자동으로 선택합니다.

`CurrentRegion` 속성을 지정하지 않으면 SDK는 모든 작업에 대해 현재 쓰기 영역을 사용합니다.

예를 들어 Azure Cosmos 계정이 “미국 서부” 및 “미국 동부” 지역에 있는 경우입니다. “미국 서부”가 쓰기 지역이고 애플리케이션이 “미국 동부”에 있는 경우입니다. CurrentRegion 속성이 구성되지 않은 경우 모든 읽기 및 쓰기 요청은 항상 “미국 서부” 지역으로 전달됩니다. CurrentRegion 속성이 구성된 경우 모든 읽기 요청은 “미국 동부” 지역으로 전달됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * Azure Cosmos DB Table API를 사용하여 전역 배포를 설정하는 방법

