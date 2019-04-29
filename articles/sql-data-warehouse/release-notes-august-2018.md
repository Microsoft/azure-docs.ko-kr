---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 8월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/13/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 9547380d4636c1a72add9dab7a9eed850abd0307
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475000"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Azure SQL Data Warehouse의 새로운 기능 2018년 8월
Azure SQL Data Warehouse는 지속적으로 개선 사항을 수신합니다. 이 문서에서는 2018년 8월에 도입된 새로운 기능과 변경 사항에 대해 설명합니다.

## <a name="automatic-intelligent-insights"></a>자동 Intelligent Insights
Microsoft는 데이터 웨어하우스에 대한 자동화를 클라우드에 제공하는 [자동 intelligent insights](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/)를 도입했습니다. 데이터 기울이기 및 최적이 아닌 테이블 통계에 대해 데이터 웨어하우스를 더 이상 모니터링할 필요가 없습니다. 추가 비용 없이 SQL Data Warehouse는 모든 Gen2 인스턴스에 대한 intelligent insights를 표시합니다. [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations)와 통합하여 활성 워크로드의 성능을 향상시키기 위해 모범 사례 권장 사항을 자동으로 받을 수 있습니다. SQL Data Warehouse는 사용량에 따라 워크로드 및 표면 권장 사항을 분석합니다. 이 분석은 워크로드의 향상된 기능에 대한 사용량 보고서 및 권장 사항을 모니터링할 수 있도록 매일 발생합니다.

Azure Advisor 포털에서 권장 사항을 확인할 수 있습니다. ![Azure SQL Data Warehouse에 대한 Azure Advisor 포털 권장 사항](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

각 범주를 자세히 확인하면 특정 경고에 대한 권장 사항을 확인할 수 있습니다. ![Azure SQL Data Warehouse에 대한 Azure Advisor 포털 권장 사항 세부 정보](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)


## <a name="bug-fixes"></a>버그 수정

| 제목 | 설명 |
|:---|:---|
| **분할 개수가 최대 제한을 초과하는 경우 잠재적인 쿼리 오류** |백만 개의 상한 파일 분할 제한을 초과하는 경우 처리되지 않은 예외로 인해 SQL 엔진이 덤프되고 모든 쿼리가 실패합니다. 이 수정 프로그램은 예외를 올바르게 처리하고 오류를 반환할 때 쿼리가 실패하지 않도록 하여 이러한 문제를 해결합니다. |
| **ExternalMoveReadersPerNode 기본값을 늘려 부하 성능 개선** |이 문제는 ExternalMoveReadersPerNode 속성 설정이 Service Fabric 설정과 동기화되지 않기 때문에 발생합니다. 이 회귀는 Gen2 부하 성능 저하를 발생합니다. 수정 프로그램은 최적화된 디자인 매개 변수 내에서 Gen2 부하 성능을 다시 회복합니다.|


## <a name="next-steps"></a>다음 단계
SQL Data Warehouse에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 발견하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

* [고객 성공 사례]
* [블로그]
* [기능 요청]
* [비디오]
* [고객 자문 팀 블로그]
* [Stack Overflow 포럼]
* [Twitter]


[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[고객 자문 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[고객 성공 사례]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow 포럼]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
