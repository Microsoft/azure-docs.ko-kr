---
title: Azure SQL Database에서 쿼리 저장소 운영
description: Azure SQL Database에서 쿼리 저장소를 운영하는 방법 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 3ceb8569d952f2947870ce7314f869623b2d87f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584746"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Azure SQL Database에서 쿼리 저장소 운영

Azure의 쿼리 저장소는 모든 쿼리에 대한 자세한 기록 정보를 지속적으로 수집하고 제공하는, 완전히 관리되는 데이터베이스 기능입니다. 쿼리 저장소는 비행기의 블랙박스와 비슷하게 생각할 수 있으며, 클라우드와 온-프레미스 고객의 쿼리 성능 문제 해결을 상당히 간소화합니다. 이 문서는 Azure의 쿼리 저장소 운영에 대한 구체적인 측면을 설명합니다. 미리 수집된 쿼리 데이터를 사용하면, 성능 문제를 신속하게 진단하고 해결할 수 있기 때문에 업무에 더 많은 시간을 집중할 수 있습니다. 

쿼리 저장소는 2015년 11월부터 Azure SQL Database에서 [전역적으로 사용할 수 있습니다](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) . 쿼리 저장소는 [SQL Database 관리자 및 성능 대시보드](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)같은 성능 분석 및 기능 조정을 위한 기반입니다. 이 문서를 게시하는 순간에도, 200,000개가 넘는 Azure의 사용자 데이터베이스에서 쿼리 저장소가 실행 중이며, 쿼리 관련 정보를 몇 달 동안 중단 없이 수집하고 있습니다.

> [!IMPORTANT]
> Microsoft는 모든 Azure SQL 데이터베이스(기존 및 신규)에 대해 쿼리 저장소를 활성화하는 과정에 있습니다. 

## <a name="optimal-query-store-configuration"></a>최적인 쿼리 저장소 구성

이 섹션은 쿼리 저장소는 물론 [SQL Database 관리자 및 성능 대시보드](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)등과 같은 종속적인 기능의 안정적인 운영을 보장하기 위해 설계된 최적의 구성 기본값을 설명합니다. 기본 구성은 지속적인 데이터 수집을 위해 최적화됩니다(예: OFF/READ_ONLY 상태에 소요되는 시간 최소화).

| 구성 | 설명 | 기본값 | 주석 |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |쿼리 저장소가 사용자 데이터베이스 내부에서 사용하는 데이터 공간에 대한 제한을 지정합니다. |100 |새 데이터베이스에 적용 |
| INTERVAL_LENGTH_MINUTES |쿼리 계획에 대해 수집된 런타임 통계가 집계되고 지속되는 기간의 규모를 정의합니다. 모든 활성 쿼리 계획은 이 구성을 통해 정의된 기간에 대해 최대 하나의 행을 갖습니다. |60 |새 데이터베이스에 적용 |
| STALE_QUERY_THRESHOLD_DAYS |지속되는 런타임 통계 및 비활성 쿼리의 보존 기간을 제어하는 시간 기반 정리 정책 |30 |새 데이터베이스 및 이전 기본값을 포함하는 데이터베이스에 적용(367) |
| SIZE_BASED_CLEANUP_MODE |쿼리 저장소 데이터 크기가 한도에 접근할 때, 자동 데이터 정리를 수행할지를 지정합니다. |AUTO |모든 데이터베이스에 적용 |
| QUERY_CAPTURE_MODE |모든 쿼리를 추적할지 또는 쿼리의 하위 집합만 추적할지를 지정합니다. |AUTO |모든 데이터베이스에 적용 |
| FLUSH_INTERVAL_SECONDS |캡처한 런타임 통계를 디스크에 플러시하기 전에 메모리에 유지하는 최대 기간을 지정합니다. |900 |새 데이터베이스에 적용 |
|  | | | |

> [!IMPORTANT]
> 이러한 기본값은 모든 Azure SQL 데이터베이스의 쿼리 저장소 활성화 마지막 단계에서 자동으로 적용됩니다(위의 중요 항목 참조). 이후로, Azure SQL Database는 고객이 설정한 구성 값이 기본 워크로드 또는 쿼리 저장소의 안정적인 운영에 부정적인 영향을 미치지 않는 한 고객이 설정한 구성 값을 변경하지 않습니다.

사용자 지정 설정을 유지하려는 경우에는, [쿼리 저장소 옵션을 사용하여 ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) 를 사용하여 이전 상태로 구성을 되돌립니다. 최적의 구성 매개 변수를 선택하는 방법을 알아보려면, [쿼리 저장소 모범 사례](https://msdn.microsoft.com/library/mt604821.aspx) 를 확인하세요.

## <a name="next-steps"></a>다음 단계

[SQL Database 성능 Insight](sql-database-performance.md)

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 다음 문서를 확인하세요.

- [데이터베이스에 대한 블랙박스](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [쿼리 저장소를 사용한 성능 모니터링](https://msdn.microsoft.com/library/dn817826.aspx)
- [쿼리 저장소 사용 시나리오](https://msdn.microsoft.com/library/mt614796.aspx)
